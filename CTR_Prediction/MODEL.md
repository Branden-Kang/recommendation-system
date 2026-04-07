# Multi-head Auto Targeting Model README

## 1. Overview

이 문서는 **유저별 카테고리 클릭 확률**과 **실제 광고별 클릭 확률**을 함께 예측하는  
**멀티헤드 자동 타겟팅 모델** 구조를 설명합니다.

이 구조의 핵심 목적은 다음과 같습니다.

1. 유저가 어떤 **광고 카테고리**에 관심이 높은지 먼저 추정한다.
2. 관심도가 높은 카테고리 내에서 어떤 **실제 광고(ad/message)** 를 클릭할 가능성이 높은지 정교하게 랭킹한다.
3. 최종적으로 CTR, CVR, fatigue 등을 함께 고려해 발송 대상을 결정한다.

즉, 이 모델은 아래 두 가지를 동시에 수행합니다.

- **Category-level prediction**  
  예: 이 유저는 가전/여행 카테고리에 반응할 확률이 높다.
- **Ad-level prediction**  
  예: 이 유저는 여행 카테고리 중에서도 “제주 항공권 특가” 광고를 클릭할 확률이 높다.

---

## 2. Why Multi-head?

광고 자동 타겟팅에서 카테고리 수준 점수만 사용하면 너무 거칠고,  
광고 수준 점수만 사용하면 후보가 너무 많아 비효율적일 수 있습니다.

따라서 아래와 같이 **2단계 역할 분담**을 하는 것이 좋습니다.

- **Category Head**
  - broad interest 추정
  - 후보 카테고리 생성
  - cold-start 시 초기 방향 제시
- **Ad-level CTR Head**
  - 실제 광고 단위 클릭 확률 예측
  - 최종 랭킹 및 발송 결정

---

## 3. Model Architecture

```text
[User Features] ──► [User Encoder] ───────────────┐
                                                  │
                                                  ├─► [Category CTR Heads]
[Ad Features] ───► [Ad Encoder] ─► [Fusion Layer] ┤     - P(click_electronics | user, context)
                                                  │     - P(click_food | user, context)
[Context] ───────► [Context Encoder] ─────────────┤     - P(click_travel | user, context)
                                                  │
                                                  └─► [Ad-level CTR Head]
                                                        - P(click | user, ad, context)
```

---

## 4. Inputs

모델 입력은 크게 3가지입니다.

### 4.1 User Features
유저의 행동/구매/반응 이력 기반 특징입니다.

예시:
- 최근 7일, 30일, 90일 클릭 수
- 최근 7일, 30일, 90일 구매 수
- 앱 방문 빈도
- 최근 메시지 오픈/클릭률
- 결제 카테고리 이용 이력
- 관심 서비스 사용 이력
- 최근 피로도(fatigue): 최근 노출 수, 연속 무반응 수

### 4.2 Ad / Message Features
광고 자체의 특징입니다.

예시:
- 광고 카테고리: 가전, 식품, 여행
- 제목
- 본문
- CTA 버튼 문구
- 이미지 embedding
- 프로모션 타입
- 할인율
- 가격대
- 랜딩 페이지 정보

### 4.3 Context Features
광고가 노출되는 상황 정보입니다.

예시:
- 채널: push / in-app / banner
- 발송 시간대
- 요일
- 시즌성
- 최근 경쟁 캠페인 수
- 최근 동일 카테고리 노출 횟수

---

## 5. Input Example

아래는 **서빙 시점**에 한 유저와 여러 광고 후보를 평가하기 위한 입력 예시입니다.

```json
{
  "user": {
    "user_id": "U12345",
    "age_bucket": "30s",
    "gender": "F",
    "recent_click_count_7d": 4,
    "recent_click_count_30d": 12,
    "recent_purchase_count_30d": 3,
    "app_open_count_7d": 15,
    "push_open_rate_30d": 0.42,
    "push_click_rate_30d": 0.18,
    "fatigue_exposure_count_7d": 9,
    "fatigue_consecutive_nonclick": 5,
    "category_affinity_history": {
      "electronics": 0.76,
      "food": 0.21,
      "travel": 0.63
    }
  },
  "candidate_ads": [
    {
      "ad_id": "AD_ELEC_001",
      "category": "electronics",
      "title": "노트북 특가전",
      "body": "인기 노트북 최대 20% 할인",
      "cta": "지금 확인",
      "discount_pct": 20,
      "price_bucket": "high",
      "image_embedding": [0.12, -0.03, 0.88]
    },
    {
      "ad_id": "AD_TRAVEL_003",
      "category": "travel",
      "title": "제주 항공권 특가",
      "body": "이번 주말 제주 왕복 특가",
      "cta": "항공권 보기",
      "discount_pct": 15,
      "price_bucket": "mid",
      "image_embedding": [0.31, 0.22, 0.45]
    },
    {
      "ad_id": "AD_FOOD_002",
      "category": "food",
      "title": "식품 정기배송 할인",
      "body": "첫 정기배송 신청 시 10% 할인",
      "cta": "할인 받기",
      "discount_pct": 10,
      "price_bucket": "low",
      "image_embedding": [0.09, 0.77, -0.14]
    }
  ],
  "context": {
    "channel": "push",
    "send_hour": 19,
    "day_of_week": "Fri",
    "season_event": "summer_promo",
    "competing_campaign_count": 2
  }
}
```

---

## 6. Outputs

이 모델은 크게 두 종류의 output을 냅니다.

### 6.1 Category CTR Output
유저가 각 카테고리 광고를 클릭할 가능성을 예측합니다.

예:
- `P(click_electronics | user, context) = 0.82`
- `P(click_food | user, context) = 0.24`
- `P(click_travel | user, context) = 0.68`

이 값은 주로 **후보 카테고리 선택**에 사용됩니다.

### 6.2 Ad-level CTR Output
유저가 특정 광고를 클릭할 가능성을 예측합니다.

예:
- `P(click | user, AD_ELEC_001, context) = 0.15`
- `P(click | user, AD_TRAVEL_003, context) = 0.12`
- `P(click | user, AD_FOOD_002, context) = 0.04`

이 값은 주로 **최종 광고 랭킹 및 발송 결정**에 사용됩니다.

---

## 7. Output Example

```json
{
  "user_id": "U12345",
  "category_ctr": {
    "electronics": 0.82,
    "food": 0.24,
    "travel": 0.68
  },
  "ad_ctr": [
    {
      "ad_id": "AD_ELEC_001",
      "category": "electronics",
      "predicted_click_prob": 0.15
    },
    {
      "ad_id": "AD_TRAVEL_003",
      "category": "travel",
      "predicted_click_prob": 0.12
    },
    {
      "ad_id": "AD_FOOD_002",
      "category": "food",
      "predicted_click_prob": 0.04
    }
  ],
  "recommended_category_order": [
    "electronics",
    "travel",
    "food"
  ],
  "recommended_ad_order": [
    "AD_ELEC_001",
    "AD_TRAVEL_003",
    "AD_FOOD_002"
  ]
}
```

---

## 8. Interpretation of Output

위 예시는 아래처럼 해석할 수 있습니다.

- 이 유저는 **가전(electronics)** 카테고리 광고에 가장 반응할 가능성이 높다.
- 그 다음은 **여행(travel)** 이다.
- 식품(food)은 상대적으로 반응 가능성이 낮다.
- 실제 광고 수준에서는:
  - `노트북 특가전` 광고가 가장 클릭 가능성이 높고
  - 그 다음은 `제주 항공권 특가`
  - `식품 정기배송 할인`은 가장 낮다

즉, 실제 발송 우선순위는 다음과 같이 둘 수 있습니다.

1. 가전 광고
2. 여행 광고
3. 식품 광고 제외 또는 후순위

---

## 9. Training Labels

### 9.1 Category Head Labels
카테고리 Head는 보통 **유저 단위 multi-label target** 으로 만들 수 있습니다.

예시:

```json
{
  "user_id": "U12345",
  "category_label": {
    "electronics": 1,
    "food": 0,
    "travel": 1
  }
}
```

의미:
- 최근 기준 기간 동안 가전 광고 클릭 경험 있음
- 식품 광고 클릭 경험 없음
- 여행 광고 클릭 경험 있음

### 9.2 Ad-level CTR Labels
광고 CTR Head는 `(user, ad, context)` 단위의 노출 로그로 학습합니다.

예시:

```json
[
  {
    "user_id": "U12345",
    "ad_id": "AD_ELEC_001",
    "channel": "push",
    "send_hour": 19,
    "clicked": 1
  },
  {
    "user_id": "U12345",
    "ad_id": "AD_TRAVEL_003",
    "channel": "push",
    "send_hour": 19,
    "clicked": 0
  }
]
```

---

## 10. Serving Flow

실제 운영 흐름은 보통 아래와 같습니다.

```text
Step 1. Category Head로 유저별 카테고리 클릭 확률 계산
Step 2. 상위 카테고리 선택
Step 3. 선택된 카테고리 내 광고 후보 생성
Step 4. Ad-level CTR Head로 광고별 클릭 확률 계산
Step 5. CVR / fatigue / frequency cap 반영
Step 6. 최종 광고 발송 결정
```

---

## 11. Example of End-to-End Decision

예를 들어 아래와 같은 점수가 나왔다고 가정합니다.

### Category CTR
- electronics: 0.82
- travel: 0.68
- food: 0.24

### Ad-level CTR
- AD_ELEC_001: 0.15
- AD_TRAVEL_003: 0.12
- AD_FOOD_002: 0.04

그러면 시스템은 보통:

1. food 카테고리는 제외하거나 낮은 우선순위로 둔다.
2. electronics, travel 카테고리 광고만 우선 검토한다.
3. 실제 발송은 `AD_ELEC_001` > `AD_TRAVEL_003` 순으로 정한다.

---

## 12. Optional Additional Heads

실무에서는 CTR만으로 최종 발송을 결정하지 않는 경우가 많습니다.  
아래 Head를 추가로 둘 수 있습니다.

- **Conversion Head**
  - `P(conversion | user, ad, context)`
- **Fatigue / Unsubscribe Head**
  - `P(unsubscribe_or_fatigue | user, ad, context)`
- **Revenue / Value Head**
  - expected revenue, LTV proxy 등

최종 스코어는 예를 들어 아래처럼 조합할 수 있습니다.

```text
Final Score = α * P(click) + β * P(conversion) - γ * P(fatigue)
```

---

## 13. Summary

이 멀티헤드 구조의 핵심은 다음과 같습니다.

- **Category CTR Head**
  - 유저의 broad interest를 파악한다.
  - 후보 카테고리를 줄이는 데 사용한다.
- **Ad-level CTR Head**
  - 실제 광고 단위 클릭 확률을 예측한다.
  - 최종 랭킹과 발송 결정에 사용한다.

즉, 이 구조는  
**“어느 카테고리에 반응할 것인가?”** 와  
**“그 카테고리 안에서 어떤 광고를 클릭할 것인가?”**  
를 함께 해결하는 자동 타겟팅 구조입니다.
