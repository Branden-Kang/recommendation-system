# 광고 피로도 스코어링 관련 연구 논문 및 산업 사례 정리

## 1. 정리 목적

본 문서는 광고 피로도(ad fatigue) 스코어링을 설계하기 위해 관련 연구 논문과 산업 사례를 정리한 자료이다.  
광고 피로도는 동일 광고, 동일 크리에이티브, 또는 유사한 메시지를 반복적으로 노출받은 사용자가 광고에 둔감해져 CTR, CVR, ROAS 등 성과가 하락하고 CPA, CPC 등 비용 지표가 악화되는 현상을 의미한다.

광고 피로도 스코어링의 실무적 목표는 다음과 같다.

- 특정 사용자-광고-크리에이티브 조합에서 피로 위험을 사전에 감지
- 반복 노출로 인한 CTR/CVR 하락 가능성 추정
- 입찰가 조정, 노출 제한, 소재 교체, 타깃 확장, 캠페인 중단 판단에 활용
- 단순 hard frequency cap이 아닌, 성과 예측 모델과 통합된 soft fatigue penalty 설계

---

## 2. 핵심 용어

| 용어 | 의미 | 스코어링 관점 |
|---|---|---|
| Ad fatigue | 반복 노출로 광고 반응이 감소하는 현상 | 최종적으로 예측하려는 위험 상태 |
| Creative fatigue | 동일 또는 유사 크리에이티브 반복으로 성과가 감소하는 현상 | ad ID보다 creative cluster 기준 집계 필요 |
| Advertising wear-in | 반복 노출 초기에는 인지도와 반응이 증가하는 구간 | 초기 노출을 무조건 피로로 보지 않아야 함 |
| Advertising wear-out | 과도한 반복 노출 이후 성과가 감소하는 구간 | 피로도 스코어의 주요 대상 |
| Frequency capping | 일정 기간 동안 동일 사용자에게 광고 노출 횟수를 제한하는 방식 | hard cap 또는 soft cap으로 구현 가능 |
| Soft frequency capping | 빈도 feature를 예측 모델에 넣어 점진적으로 penalty를 학습하는 방식 | 고정 임계값보다 유연한 접근 |
| FoRI | Frequency over Recent Intervals. 최근 구간별 frequency-recency feature | 최근성 기반 피로도 feature 설계에 유용 |
| Viewable impression | 실제로 사용자가 볼 수 있었던 노출 | 피로도 집계의 기본 단위로 권장 |

---

## 3. 관련 연구 논문 흐름: 연도순 정리

| 연도 | 논문 / 연구 | 주요 내용 | 광고 피로도 스코어링 시사점 |
|---:|---|---|---|
| 1988 | Pechmann & Stewart, **Advertising Repetition: A Critical Review of Wearin and Wearout** | 광고 반복 효과를 wear-in과 wear-out 관점에서 정리한 고전 연구이다. 반복 노출이 항상 부정적인 것은 아니며, 초기에는 인지도와 기억을 높일 수 있지만 과도한 반복은 효과 저하를 만들 수 있다고 설명한다. | 피로도는 단순히 노출 횟수가 많다는 이유만으로 1이 되는 것이 아니라, 초기 긍정 효과와 후기 부정 효과가 공존하는 비선형 구조로 설계해야 한다. |
| 2011 / 2014 | Buchbinder et al., **Frequency Capping in Online Advertising** | frequency capping이 적용된 display ad serving을 online assignment 문제로 다룬 연구이다. 사용자 공급이 사전에 알려지지 않는 상황에서 frequency cap 제약을 만족하면서 광고를 배분하는 문제를 분석한다. | 피로도는 사용자 경험뿐 아니라 광고 배분 최적화 문제와 연결된다. 운영 관점에서는 cap 제약과 수익 최적화를 동시에 고려해야 한다. |
| 2015 | Schmidt & Eisend, **Advertising Repetition: A Meta-Analysis on Effective Frequency in Advertising** | 광고 반복 노출 효과에 대한 메타분석이다. 실험 환경에서 브랜드 태도는 약 10회 노출 부근에서 최대화되고, 회상은 8회 전까지 선형적으로 증가하는 경향을 보고한다. | 고정 frequency 기준을 정할 때 산업/캠페인별 보정이 필요하지만, 반복 효과가 inverted-U 형태를 가질 수 있다는 근거로 활용 가능하다. |
| 2015 | Zhang et al., **An Empirical Study on Display Ad Impression Viewability Measurements** | 단순 impression이 실제 노출 효과를 정확히 반영하지 못한다는 점을 지적하고, pixel percentage와 exposure time 기준의 viewability 측정을 분석한다. | 피로도 feature는 전체 impression보다 viewable impression을 기준으로 산출하는 것이 바람직하다. 보이지 않은 노출까지 피로도로 반영하면 noise가 커진다. |
| 2019 | Aharon et al., **Soft Frequency Capping for Improved Ad Click Prediction in Yahoo Gemini Native** | Yahoo Gemini Native 광고에서 hard frequency capping 대신 frequency feature를 pCTR/pCONV 예측 모델에 통합하는 soft frequency capping을 제안했다. 온라인 bucket test에서 7.3% revenue lift를 보고했다. | 피로도는 rule-based cap보다 예측 모델 내부 feature 또는 penalty로 학습하는 방식이 효과적일 수 있다. |
| 2019 / 2020 | Moriwaki et al., **Fatigue-Aware Ad Creative Selection** | 반복 노출에 따른 사용자의 심리적 상태를 고려해 온라인 디스플레이 광고 크리에이티브를 선택하는 알고리즘을 제안했다. 실제 production 환경에서 baseline 대비 우수한 성과를 보고했다. | 광고 단위뿐 아니라 creative-level fatigue를 별도로 모델링해야 한다. 동일 캠페인 안에서도 소재별 피로도 차이가 존재한다. |
| 2021 | Romero Leguina et al., **Optimizing the Frequency Capping: A Robust and Reliable Methodology to Define the Number of Ads to Maximize ROAS** | 기존의 전체 impression 횟수 대신 non-clicked impression 수를 기준으로 frequency cap을 최적화하는 방법론을 제안했다. ROAS 최대화와 사용자 짜증 감소를 함께 고려한다. | 단순 노출 횟수보다 “반복 노출되었지만 반응하지 않은 횟수”가 피로도 예측에 더 강한 feature가 될 수 있다. |
| 2022 | Kitada et al., **Ad Creative Discontinuation Prediction with Multi-Modal Multi-Task Neural Survival Networks** | 광고 크리에이티브 중단 시점을 survival analysis 기반 hazard function으로 예측했다. 텍스트, 카테고리, 이미지, 수치 feature를 결합한 multi-modal DNN을 사용했고, 10B scale impression 데이터로 평가했다. | 피로도 스코어를 “소재를 교체하거나 중단해야 할 위험도”로 해석하려면 survival/hazard 기반 모델이 적합하다. |
| 2023 | Silberstein et al., **Combating Ad Fatigue via Frequency-Recency Features in Online Advertising Systems** | FoRI(Frequency over Recent Intervals) feature를 제안했다. 최근 구간별 frequency-recency 정보를 CTR 예측 모델에 추가해 반복 광고 발생을 줄이고 unseen ad exposure를 늘리는 방향을 보였다. | 누적 frequency보다 1시간, 1일, 7일, 30일 등 최근 구간별 노출 feature가 중요하다. |
| 2024 | Guo & Jiang, **Optimal Dynamic Advertising Policy Considering Consumer Ad Fatigue** | 소비자 광고 피로도를 고려한 동적 광고 지출 정책을 연구했다. 과도한 광고 지출의 부정적 영향을 감쇠 계수로 반영해 광고 반응 함수가 inverted-U 형태를 보이도록 모델링했다. | 피로도가 높아지는 구간에서는 캠페인 예산, 입찰가, 노출 강도를 동적으로 낮추는 정책이 필요하다. |
| 2025 | Gao & Qiao, **Reach Measurement, Optimization and Frequency Capping under k-Anonymity** | 개인정보 보호 환경에서 reach measurement와 frequency capping을 다룬 연구이다. 사용자 단위 식별이 제한되는 환경에서 probabilistic discounting 기반의 reach/frequency 관리가 필요함을 보인다. | 쿠키, IDFA, AdID 제약이 강해지는 환경에서는 완전한 user-level fatigue 관측이 어렵다. 집계 기반 또는 확률 기반 피로도 스코어가 필요하다. |
| 2025 | Shaw, **A Path Signature Framework for Detecting Creative Fatigue in Digital Advertising** | 광고 성과 시계열을 path로 보고, 연속 구간의 signature distance를 통해 creative fatigue의 변화점을 탐지하는 방법론을 제안했다. | 피로도 탐지를 CTR/CVR 시계열의 change-point detection 문제로 확장할 수 있다. |

---

## 4. 연구 흐름 요약

연구 흐름은 크게 네 단계로 구분할 수 있다.

| 단계 | 시기 | 연구 초점 | 특징 |
|---|---:|---|---|
| 1단계 | 1980s~2010s | 광고 반복 효과와 wear-in/wear-out | 반복 노출의 긍정/부정 효과를 이론적으로 정리 |
| 2단계 | 2010s | frequency capping 최적화 | 노출 횟수 제한을 광고 serving 및 allocation 문제로 모델링 |
| 3단계 | 2019~2023 | 모델 기반 fatigue feature 통합 | frequency, recency, non-clicked impression, creative state를 pCTR/CVR 모델에 반영 |
| 4단계 | 2024~현재 | privacy-aware, dynamic policy, time-series fatigue detection | 개인정보 보호, 동적 예산 정책, change-point detection, survival modeling으로 확장 |

핵심 변화는 **hard cap 중심의 운영 규칙**에서 **예측 모델에 피로도 관련 feature를 통합하는 soft scoring 방식**으로 이동했다는 점이다.

---

## 5. 산업 사례: 표 형식 정리

| 기업 / 플랫폼 | 사용 용어 | 주요 감지 신호 | 주요 대응 방식 | 스코어링 설계에 주는 시사점 | 참고 링크 |
|---|---|---|---|---|---|
| Meta | Creative fatigue | 동일 creative element 반복 노출, 성과 하락, high fatigue case | 새 크리에이티브 추가, 피로도가 높은 ad set 개선, 광고 delivery optimization에 fatigue signal 활용 | ad ID가 아니라 creative element 또는 creative cluster 기준으로 피로도를 계산해야 한다. 동일 이미지/영상/메시지를 여러 ad가 공유할 수 있기 때문이다. | [Analytics at Meta: Creative Fatigue](https://medium.com/%40AnalyticsAtMeta/creative-fatigue-how-advertisers-can-improve-performance-by-managing-repeated-exposures-e76a0ea1084d), [Meta Business Help](https://www.facebook.com/business/help/1346816142327858) |
| Amazon Ads | Ad fatigue / Creative ad fatigue | 반복 노출, 좁은 audience segment, engagement decline | creative refresh, creative rotation, frequency capping, audience segmentation, dynamic creative optimization | 작은 타깃 세그먼트일수록 audience saturation feature가 중요하다. 신규 도달 비율과 평균 frequency를 함께 봐야 한다. | [Amazon Ads: How to prevent and cure creative ad fatigue](https://advertising.amazon.com/library/guides/ad-fatigue) |
| TikTok Ads | Creative fatigue | 성과 하락, 낮은 daily new users, 영상 소재 반복 | 기존 ad group에 새 creative 추가, creative asset library 유지, Smart Creative, Video Insights 활용 | short-form video 광고는 피로 발생 속도가 빠를 수 있으므로 creative age, hook performance, first-seconds engagement를 별도 feature로 관리할 필요가 있다. | [TikTok Ads: Creative best practices](https://ads.tiktok.com/help/article/creative-best-practices) |
| Google Ads | Frequency capping | 동일 사용자에게 반복 노출되는 display/video ads | Display/Video 캠페인에서 동일 사용자 노출 횟수 제한 | hard frequency cap은 운영상 기본 장치이나, 성과 최적화에는 fatigue score 기반 bid adjustment와 병행하는 것이 좋다. | [Google Ads Help: Use frequency capping](https://support.google.com/google-ads/answer/6034106?hl=en) |
| Google Display & Video 360 | Frequency capping | campaign, insertion order, line item 수준의 반복 노출 | 노출 횟수와 기간을 기준으로 cap 설정. 2025년 2월 28일 이후 lifetime frequency cap 최대 기간이 30일로 제한됨 | 장기 누적 fatigue보다 최근 30일 이내의 windowed feature가 실무적으로 중요해질 수 있다. | [DV360 Help: Manage frequency with frequency caps](https://support.google.com/displayvideo/answer/2696786?hl=en) |
| Google Privacy Sandbox | Privacy-preserving frequency capping | on-device counter, ad-specific event, predefined filter strategy | Protected Audience에서 on-device counter와 ad filtering으로 frequency cap 구현 | 개인정보 보호 환경에서는 서버에서 완전한 user-level exposure history를 보지 못할 수 있다. on-device 또는 집계 기반 scoring 구조가 필요하다. | [Privacy Sandbox: Protected Audience frequency capping](https://privacysandbox.google.com/private-advertising/protected-audience/android/frequency-capping) |
| Google Active View | Viewable impression | display: 50% 이상 1초 이상, video: 50% 이상 2초 이상 | viewability 기준으로 실제 노출 여부 측정 | 피로도 계산의 기본 impression은 raw impression보다 viewable impression이 적합하다. | [Google Ads Help: Active View metrics](https://support.google.com/google-ads/answer/7029393?hl=en) |

---

## 6. 광고 피로도 스코어링 설계 방향

광고 피로도 스코어는 다음과 같이 정의할 수 있다.

```text
FatigueScore(u, a, c, t) =
    sigmoid(
        w1 * RecentFrequency
      + w2 * RecencyDensity
      + w3 * ConsecutiveNonClick
      + w4 * CreativeSimilarity
      + w5 * PerformanceDecay
      + w6 * AudienceSaturation
      + w7 * NegativeFeedback
      - w8 * Novelty
    )
```

여기서 `u`는 사용자, `a`는 광고, `c`는 크리에이티브 또는 크리에이티브 클러스터, `t`는 예측 시점을 의미한다.

---

## 7. 추천 Feature 설계

| Feature 그룹 | 예시 변수 | 설명 | 중요도 |
|---|---|---|---|
| 최근 노출 빈도 | `impressions_1h`, `impressions_24h`, `impressions_7d`, `impressions_30d` | 최근 기간별 동일 광고/소재 노출 횟수 | 매우 높음 |
| 최근성 밀도 | `exp(-time_since_last_impression / tau)` | 마지막 노출 이후 시간이 짧을수록 피로도 증가 | 높음 |
| 무반응 반복 | `consecutive_non_clicked_impressions`, `non_click_impressions_7d` | 반복 노출되었지만 클릭하지 않은 횟수 | 매우 높음 |
| 크리에이티브 유사도 | `creative_embedding_similarity`, `same_creative_cluster_count` | 완전히 같은 광고가 아니어도 유사 이미지/문구 반복이면 피로 가능 | 매우 높음 |
| 성과 하락 | `rolling_ctr_drop`, `rolling_cvr_drop`, `cpa_increase` | 과거 대비 CTR/CVR 하락 또는 CPA 상승 | 매우 높음 |
| audience saturation | `frequency_per_reach`, `new_reach_ratio`, `repeat_user_ratio` | 신규 사용자 도달 감소와 반복 사용자 비중 증가 | 높음 |
| 부정 반응 | `hide_rate`, `skip_rate`, `report_rate`, `mute_rate` | 사용자의 명시적 또는 암묵적 부정 신호 | 높음 |
| 소재 신선도 | `creative_age`, `days_since_launch`, `first_seen_flag` | 오래된 소재일수록 피로 가능성 증가 | 중간~높음 |
| viewability | `viewable_impressions`, `viewable_time`, `pixel_in_view` | 실제로 볼 수 있었던 노출 기준 보정 | 높음 |
| campaign context | `budget_pressure`, `bid_level`, `audience_size` | 예산 소진과 좁은 타깃으로 인한 반복 노출 | 중간 |

---

## 8. 추천 Label 정의

광고 피로도는 직접적인 정답 label이 거의 없으므로 proxy label 설계가 필요하다.

### 8.1 성과 하락 기반 label

```text
fatigue_label = 1 if
    frequency_7d >= top_quantile
    and CTR_recent <= CTR_baseline * 0.7
```

예시 기준:

- 최근 3일 CTR이 이전 7일 평균 CTR 대비 30% 이상 하락
- 최근 7일 frequency가 상위 20% 이상
- CVR 하락 또는 CPA 상승이 동반되면 positive label 신뢰도 증가

### 8.2 Non-clicked impression 기반 label

```text
fatigue_label = 1 if
    consecutive_non_clicked_impressions >= threshold
    and no_conversion_after_repeated_exposure = true
```

이 방식은 Romero Leguina et al.의 non-clicked impression 기반 frequency cap 최적화 관점과 잘 맞는다.

### 8.3 Survival / Hazard 기반 label

```text
event = creative_discontinued
time_to_event = days_from_launch_to_discontinuation
```

이 방식은 Kitada et al.처럼 광고 소재의 중단 또는 교체 시점을 예측하는 구조에 적합하다.

---

## 9. 모델링 접근법

| 접근법 | 설명 | 장점 | 한계 | 추천 단계 |
|---|---|---|---|---|
| Rule-based score | frequency, CTR drop, creative age 등을 가중합 | 빠른 적용 가능, 해석 쉬움 | 업종/캠페인별 보정 필요 | MVP |
| Logistic Regression / GBDT | fatigue proxy label을 기준으로 feature 학습 | 구현 쉬움, feature importance 확인 가능 | 장기 시계열 패턴 반영 제한 | 1차 모델 |
| pCTR/pCVR 모델 feature 통합 | 기존 CTR/CVR 모델에 fatigue feature 추가 | soft frequency capping 구현 가능 | 기존 serving 모델 수정 필요 | 실무 추천 |
| Survival Analysis | 소재 중단/교체 위험도를 hazard로 예측 | “언제 교체할지” 판단 가능 | label 구성 복잡 | 고도화 |
| Contextual Bandit | 피로도와 반응을 함께 고려해 creative 선택 | 탐색-활용 균형 가능 | 실험 설계와 안정성 필요 | 고도화 |
| Change-point Detection | CTR/CVR 시계열에서 fatigue onset 탐지 | 피로 발생 시점 탐지 가능 | 실시간 적용 시 smoothing 필요 | 고도화 |
| Multimodal DNN | 이미지, 텍스트, 카테고리, 성과 지표 결합 | creative-level fatigue에 강함 | 데이터와 serving 비용 큼 | 고도화 |

---

## 10. 운영 정책 예시

| FatigueScore 구간 | 상태 | 추천 액션 |
|---:|---|---|
| 0~30 | 정상 | 기존 입찰/노출 유지 |
| 30~50 | 초기 피로 가능성 | creative rotation 강화, frequency 모니터링 |
| 50~70 | 중간 피로 | bid down, 유사 소재 노출 제한, 신규 audience 확장 |
| 70~85 | 높은 피로 | creative refresh, ad group 내 신규 소재 추가, cap 강화 |
| 85~100 | 심각한 피로 | 광고/소재 일시 중단, 캠페인 구조 재검토, 타깃 확장 또는 메시지 교체 |

운영 기준은 업종, 캠페인 목적, funnel 단계, 광고 포맷에 따라 달라지므로 A/B test를 통해 threshold를 보정해야 한다.

---

## 11. Audience Expansion / Lookalike 연구와의 연결

광고 피로도 스코어링은 lookalike audience expansion 연구와도 연결할 수 있다.  
기존 lookalike 모델은 보통 seed audience와 유사하고 pCTR 또는 pCVR이 높은 사용자를 찾는 데 집중한다. 그러나 실제 운영에서는 같은 고반응 사용자군에 광고가 반복적으로 노출되면서 성과가 빠르게 감소할 수 있다.

따라서 다음과 같은 목적함수를 고려할 수 있다.

```text
FinalScore(u, a, t) =
    alpha * pCTR(u, a, t)
  + beta  * pCVR(u, a, t)
  + gamma * LookalikeSimilarity(u, S)
  - delta * FatigueRisk(u, a, t)
```

이 구조는 다음 연구 방향으로 확장 가능하다.

- 피로도가 낮은 신규 lookalike audience 발굴
- seed audience와 유사하지만 이미 과노출된 사용자는 ranking 하향
- audience expansion ratio별 quality retention과 fatigue risk 동시 최적화
- sparse seed campaign에서 반복 노출로 인한 성과 하락 방지

추천 연구 제목 예시는 다음과 같다.

- **Fatigue-Aware Lookalike Audience Expansion for Sustainable Ad Delivery**
- **Recency- and Similarity-Aware Ad Fatigue Scoring for Quality-Retained Audience Expansion**
- **Fatigue-Aware Frequency Optimization for Sustainable Audience Expansion under Sparse Feedback**

---

## 12. 실무 적용 로드맵

| 단계 | 목표 | 구현 내용 |
|---|---|---|
| 1단계 | 데이터 집계 | user-ad, user-campaign, user-creative-cluster, user-advertiser 단위 노출/클릭/CVR 집계 |
| 2단계 | rule 기반 MVP | frequency, non-clicked run, CTR drop, creative age 기반 점수 산출 |
| 3단계 | soft scoring 모델 | Logistic Regression, LightGBM, XGBoost 등으로 fatigue probability 예측 |
| 4단계 | pCTR/CVR 통합 | 기존 광고 ranking 모델에 fatigue feature 추가 |
| 5단계 | 운영 액션 연동 | bid multiplier, creative refresh, cap 강화, audience expansion에 연결 |
| 6단계 | A/B test | CTR, CVR, CPA, ROAS, repeat exposure, new reach ratio, user negative feedback 평가 |

---

## 13. 핵심 결론

광고 피로도 스코어링에서 가장 중요한 점은 다음과 같다.

1. **단순 frequency만으로는 부족하다.**  
   최근성, 무반응 반복, 소재 유사도, 성과 하락, audience saturation을 함께 봐야 한다.

2. **hard cap보다 soft scoring이 유리하다.**  
   Yahoo Gemini 사례처럼 frequency feature를 pCTR/CVR 모델에 통합하면 고정 cap보다 유연한 최적화가 가능하다.

3. **creative-level fatigue가 중요하다.**  
   Meta와 Moriwaki et al. 사례 모두 광고 단위보다 크리에이티브 반복 효과를 중시한다.

4. **viewable impression 기준 집계가 필요하다.**  
   실제로 보지 않은 노출까지 피로도로 계산하면 모델 noise가 커진다.

5. **피로도는 ranking, bidding, creative refresh, audience expansion과 함께 써야 한다.**  
   단순 모니터링 지표가 아니라 운영 액션과 연결되어야 실질적인 효과가 있다.

---

## 14. 참고문헌 및 자료

1. Pechmann, C., & Stewart, D. W. (1988). *Advertising Repetition: A Critical Review of Wearin and Wearout*. Current Issues and Research in Advertising.  
   https://www.tandfonline.com/doi/abs/10.1080/01633392.1988.10504936

2. Buchbinder, N., Feldman, M., Ghosh, A., & Naor, J. (2011/2014). *Frequency Capping in Online Advertising*.  
   https://theory.epfl.ch/moranfe/Publications/WADS2011.pdf

3. Schmidt, S., & Eisend, M. (2015). *Advertising Repetition: A Meta-Analysis on Effective Frequency in Advertising*. Journal of Advertising.  
   https://www.tandfonline.com/doi/abs/10.1080/00913367.2015.1018460

4. Zhang, W., Pan, Y., Zhou, T., & Wang, J. (2015). *An Empirical Study on Display Ad Impression Viewability Measurements*.  
   https://arxiv.org/abs/1505.05788

5. Aharon, M., Kaplan, Y., Levy, R., Somekh, O., Blanc, A., Eshel, N., Shahar, A., Singer, A., & Zlotnik, A. (2019). *Soft Frequency Capping for Improved Ad Click Prediction in Yahoo Gemini Native*.  
   https://dl.acm.org/doi/10.1145/3357384.3357801

6. Moriwaki, D., Fujita, K., Yasui, S., & Hoshino, T. (2019/2020). *Fatigue-Aware Ad Creative Selection*.  
   https://arxiv.org/abs/1908.08936

7. Romero Leguina, J., Cuevas, Á., Cuevas, R., & Sánchez-Fernández, L. (2021). *Optimizing the Frequency Capping: A Robust and Reliable Methodology to Define the Number of Ads to Maximize ROAS*. Applied Sciences.  
   https://www.mdpi.com/2076-3417/11/15/6688

8. Kitada, S., Iyatomi, H., & Seki, Y. (2022). *Ad Creative Discontinuation Prediction with Multi-Modal Multi-Task Neural Survival Networks*.  
   https://arxiv.org/abs/2204.11588

9. Silberstein, N., Shoham, O., et al. (2023). *Combating Ad Fatigue via Frequency-Recency Features in Online Advertising Systems*. CIKM 2023.  
   https://dl.acm.org/doi/10.1145/3583780.3615461

10. Guo, R., & Jiang, Z. (2024). *Optimal Dynamic Advertising Policy Considering Consumer Ad Fatigue*. Decision Support Systems.  
    https://dl.acm.org/doi/10.1016/j.dss.2024.114323

11. Gao, X., & Qiao, M. (2025). *Reach Measurement, Optimization and Frequency Capping under k-Anonymity*.  
    https://arxiv.org/html/2501.04882v1

12. Shaw, C. (2025). *A Path Signature Framework for Detecting Creative Fatigue in Digital Advertising*.  
    https://arxiv.org/abs/2509.09758

13. Meta / Analytics at Meta. *Creative Fatigue: How advertisers can improve performance by managing repeated exposures*.  
    https://medium.com/%40AnalyticsAtMeta/creative-fatigue-how-advertisers-can-improve-performance-by-managing-repeated-exposures-e76a0ea1084d

14. Meta Business Help. *Creative Fatigue Recommendations in Meta Ads Manager*.  
    https://www.facebook.com/business/help/1346816142327858

15. Amazon Ads. *How to prevent and cure creative ad fatigue*.  
    https://advertising.amazon.com/library/guides/ad-fatigue

16. TikTok Ads. *Creative best practices for performance ads*.  
    https://ads.tiktok.com/help/article/creative-best-practices

17. Google Ads Help. *Use frequency capping*.  
    https://support.google.com/google-ads/answer/6034106?hl=en

18. Google Display & Video 360 Help. *Manage frequency with frequency caps*.  
    https://support.google.com/displayvideo/answer/2696786?hl=en

19. Google Privacy Sandbox. *Protected Audience frequency capping*.  
    https://privacysandbox.google.com/private-advertising/protected-audience/android/frequency-capping

20. Google Ads Help. *Understanding viewability and Active View reporting metrics*.  
    https://support.google.com/google-ads/answer/7029393?hl=en

---

## 15. 후속 작업 아이디어

본 문서를 기반으로 다음 산출물로 확장할 수 있다.

- 광고 피로도 스코어링 feature specification 문서
- 피로도 label 정의 및 SQL 집계 로직
- LightGBM/XGBoost 기반 fatigue score baseline 코드
- pCTR 모델에 fatigue feature를 통합하는 실험 설계
- lookalike audience expansion 논문 주제와 연결한 연구 proposal
- 발표용 PPT 구성안
