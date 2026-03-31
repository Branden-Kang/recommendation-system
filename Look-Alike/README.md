# Lookalike / Audience Expansion 논문 비교 정리

## 논문 비교표

| 논문 / 사례 | 문제정의 | 데이터 | 방법 | 장점 | 한계 | 우리 서비스 적용 포인트 |
|---|---|---|---|---|---|---|
| **Finding Users Who Act Alike: Transfer Learning for Expanding Advertiser Audiences** (Pinterest, KDD 2019) | 광고주가 보유한 **seed customer**를 기반으로 더 넓은 잠재 고객군을 찾는 audience look-alike targeting 문제 | Pinterest 광고 플랫폼의 실제 advertiser audience/seed user 데이터 기반 production 문제 | **2-stage embedding-based audience expansion**과 transfer learning을 결합한 산업형 시스템 | 실서비스 배포 사례라서 구조가 현실적이고, 초기 lookalike 시스템의 기준점으로 좋음 | 최근 논문 대비 user heterogeneity, multi-view feature, graph/text signal 반영이 약함 | 우리 서비스에서 **가장 먼저 만들기 쉬운 baseline**. seed 유저 정의 → user embedding 생성 → ANN 검색 구조로 MVP를 빠르게 만들 수 있음 |
| **A Sub-linear, Massive-scale Look-alike Audience Extension System** (Yahoo, 2016) | 광고주 seed와 비슷한 사용자를 **초대규모 유저 풀에서 빠르게 찾는** audience extension 문제 | 수십억 사용자, 수백만 피처, 앱 설치 광고 캠페인 데이터 | **Graph-constrained look-alike system**. 전역 user-to-user similarity graph를 만든 뒤, nearest-neighbor filtering으로 후보를 줄이고 캠페인별 모델링 수행 | **대규모 확장성**이 매우 강함. 논문 기준 3 billion+ users, 3000+ campaigns를 4시간 내 처리. 전환율 및 CPA 개선도 제시 | 그래프 구축 비용이 크고, 피처/그래프 품질에 민감. 최신 deep embedding/LLM 기반 표현력은 없음 | 사용자 수가 많고 검색 시간을 강하게 제약받는 환경에 적합. **후보군 축소용 retrieval layer** 설계에 특히 참고할 가치가 큼 |
| **Learning to Expand Audience via Meta Hybrid Experts and Critics for Recommendation and Advertising** (KDD 2021) | 다양한 캠페인이 동시에 존재하고, 각 캠페인의 seed set이 작아 **캠페인별 overfitting**이 발생하는 audience expansion 문제 | 추천/광고 플랫폼의 다수 마케팅 캠페인과 seed audience | **MetaHeac**라는 2-stage meta-learning 프레임워크. 여러 캠페인에서 transferable knowledge를 학습하고 특정 캠페인에 빠르게 적응 | 신규 캠페인이나 seed가 작은 캠페인에 강하고, “캠페인 간 일반화”라는 실무 문제를 정면으로 다룸 | 학습/운영 구조가 복잡하고, 메타러닝 파이프라인 유지비용이 큼 | 캠페인 종류가 많고 **seed 수가 작은 세그먼트**가 많다면 유용. 금융/커머스에서 상품군별 타기팅 모델을 따로 만들기 어려울 때 적합 |
| **네이버 DEVIEW 2021 - Look-alike Modeling and Serving 비슷한 사람을 찾아주세요** | 단순히 seed와 비슷한 사람을 찾는 것이 아니라, **광고 효율(전환)**을 높이면서도 CTR/노출 규모까지 고려하는 lookalike 문제 | 익명화된 사용자 행동 데이터, 구매 카테고리 여부, 광고 플랫폼 로그. 슬라이드 기준 IAB purchase intent taxonomy 약 600개 카테고리 사용 | **User Embedding Model + PU learning + offline/online serving 분리**. 행동 데이터를 기반으로 사용자 임베딩을 학습하고, online에서는 광고주 요청 seed로 lookalike를 생성 | 실제 광고 플랫폼 운영 관점이 강함. 성능뿐 아니라 **CTCVR/CVR/CTR**과 처리량을 동시에 고려. 1억+ 유저에 대해 10분 이내 유사도 계산 목표 제시 | 슬라이드 자료라 논문처럼 엄밀한 실험 세부가 제한적. 광고 플랫폼 내부 데이터 의존성이 큼 | **광고 성과 중심 lookalike**를 만들 때 매우 참고 가치가 큼. 단순 similarity보다 비즈니스 지표 최적화를 목표로 삼아야 할 때 적합 |
| **Finding Lookalike Customers for E-Commerce Marketing** (Walmart, 2023) | CRM email/push 마케팅에서 기존 고객(seed set)과 유사한 고객을 찾아 **캠페인 reach와 revenue**를 키우는 문제 | Walmart e-commerce의 대규모 고객 데이터 | **Deep customer embedding + approximate nearest neighbor search**. 비즈니스 관심사별 해석 가능한 similarity metric 구성 | 대규모 확장성, 구현 단순성, retrieval serving 용이성이 큼 | 복잡한 관계 구조나 텍스트 의미 신호는 graph/LLM 계열보다 덜 반영될 수 있음 | 우리 서비스에서 **가장 추천되는 1순위 실전형 구조**. user feature가 충분하고 대규모 타깃 검색이 필요하면 빠르게 온라인화 가능 |
| **Exploring 360-Degree View of Customers for Lookalike Modeling** (2023) | 사용자층의 **이질성(heterogeneity)**과 **희소성(sparsity)** 때문에 단순 feature만으로는 lookalike가 어렵다는 문제 | Rakuten Ichiba(e-commerce)와 Rakuten Travel 데이터를 결합하고, demographics·buying behavior·loyalty behavior를 함께 사용 | 고객의 서로 다른 행동/속성을 통합하는 **360-degree multi-view framework** | 구매이력뿐 아니라 라이프스타일, affordability, loyalty까지 반영해 더 풍부한 customer representation 생성 가능 | 데이터 통합 비용이 크고, 여러 서비스의 ID 연결·특징 정합성 문제가 있음 | 앱 행동, 결제, 검색, 클릭, 멤버십, 제휴채널 로그가 흩어져 있다면 중요. **단일 채널 기반 lookalike보다 훨씬 실무적** |
| **Adobe Tech Blog - Look-Alike Audiences: AI-enabled Audience Expansion in Real-Time CDP** (2023) | RTCDP 환경에서 고객이 선택한 audience를 바탕으로 **설명 가능하고 운영 가능한** look-alike audience를 생성하는 문제 | Real-Time Customer Profile 상의 **profile-level data, event-level data, segmentation data**를 자동 feature engineering | 문제를 **similarity problem**으로 정의하고, 프로필을 문서처럼 보고 **TF-IDF feature vector**를 만든 뒤, seed audience 평균 벡터와의 similarity로 lookalike를 산출 | 계산 비용이 classification/clustering보다 낮고, **설명 가능성**이 높으며, noise와 day-to-day fluctuation에 강하다고 설명 | deep embedding/graph/LLM 기반 최신 방식보다 표현력은 제한될 수 있음. 고객별 스키마 일반화는 강하지만 복잡한 관계 학습은 약함 | **범용 CDP형 lookalike baseline**으로 매우 유용. 데이터 스키마가 고객마다 다르고 설명 가능성이 중요할 때 적합 |
| **Graph-Based Audience Expansion Model for Marketing Campaigns** (SIGIR 2024) | seed 사용자와 유사한 행동 패턴을 가진 새로운 audience를 찾는 문제를 **graph 관점**으로 재정의 | 마케팅 캠페인 audience expansion 문제를 위한 graph-based 설정 | 사용자/행동/관계를 그래프로 모델링하는 **graph-based audience expansion** | 단순 임베딩보다 복잡한 연결관계와 sparse interaction을 더 잘 활용할 여지가 큼 | 그래프 구성 품질에 민감하고, 온라인 추론/서빙 복잡도가 커질 수 있음 | 유저-상품-카테고리-검색어-콘텐츠 간 연결성이 강하다면 적용 가치가 큼. **행동 그래프가 풍부한 커머스/콘텐츠 플랫폼**에 적합 |
| **GATS: Generative Audience Targeting System for Online Advertising** (SIGIR 2024) | 기존 rule-based, look-alike, graph-based 방법의 한계를 넘어서 **유연하고 해석 가능한 audience criteria**를 만들고자 하는 문제 | 온라인 광고 타기팅 문제를 대상으로 한 생성형 프레임워크 | **LLM 기반 Generative Audience Targeting System**으로 타기팅 기준을 생성·표현 | marketer가 이해하기 쉬운 표현과 높은 유연성을 제공. lookalike를 넘어서 “타깃 정의 자체”를 고도화 | 엄밀한 의미의 seed-based lookalike와는 문제정의가 다소 다르며, 생성 결과의 안정성/통제 가능성이 실무 이슈가 될 수 있음 | 마케터가 **자연어로 타깃 정의를 만들고 싶어할 때** 유용. 추천보다는 campaign planning 툴에 가까움 |
| **Lookalike Audience Expansion: A Graph-Based Model with LLMs** (SIGIR eCom Workshop 2025) | 기존 graph approach가 ID 기반 정보에 치우치고, user reviews / item descriptions 같은 텍스트를 놓친다는 문제와 privacy 규제 문제를 동시에 다룸 | lookalike audience expansion 문제를 대상으로 하며, LLM으로 textual information을 graph 모델에 통합하는 방향 | **Graph-Lookalike Model (GLoM)**: graph 기반 구조에 LLM을 결합해 semantic/textual signal 활용 | 구조적 관계와 텍스트 의미를 함께 쓸 수 있어, sparse ID 기반 모델의 약점을 줄일 수 있음 | 워크숍 논문이라 메인 컨퍼런스 논문보다 검증 범위가 제한적일 가능성이 큼 | 리뷰 텍스트, 상품 설명, 검색어 등 **텍스트 자산이 많은 서비스**에 특히 적합. 행동 로그만으로 부족할 때 실험 가치 큼 |
| **Few-shot and Zero-shot Audience Expansion with User and Task Model Pre-training on Tabular Data** (WWW 2026 Short Paper accepted) | 적은 seed 또는 신규 task에서도 audience expansion을 가능하게 하려는 **few-shot / zero-shot** 문제 | 제목상 **tabular data** 기반 user/task pre-training을 활용 | user model + task model pre-training을 통해 low-data campaign에 적응하려는 방향 | 신규 캠페인, sparse seed, cold-start 문제에 잘 맞는 최신 방향성 | full paper를 아직 확인하지 못하면 방법/실험/한계를 단정하기 어려움 | **신규 캠페인이 자주 생기고 라벨이 적다면** 중요. 당장 구현보다 추적이 필요한 최신 연구 후보 |

---

## Lookalike 기술 카테고리 정리

lookalike 기술은 하나의 고정된 방법이라기보다,  
**사용자 표현 방식(feature/representation)**,  
**유사도 계산 방식(scoring)**,  
**대규모 후보 검색 방식(retrieval/serving)**  
의 조합으로 이해하는 것이 좋다.

---

### 1. 규칙/통계 기반 Similarity 모델
**설명**  
사용자를 sparse feature vector로 표현한 뒤, seed audience의 평균 벡터와 각 사용자 벡터 사이의 유사도를 계산하는 방식이다.  
정보검색(IR) 관점과 유사하며, TF-IDF, cosine similarity, mean audience vector 같은 기법이 대표적이다.

**대표 사례**
- Adobe RTCDP Look-Alike Audiences

**핵심 특징**
- 해석 가능성이 높음
- 운영 비용이 비교적 낮음
- 고객별 데이터 스키마가 달라도 범용 적용이 쉬움
- 복잡한 관계 구조나 비선형 패턴 학습은 상대적으로 약함

**언제 적합한가**
- 설명 가능성이 매우 중요할 때
- 고객사별 데이터 구조가 제각각일 때
- 빠르게 범용 baseline을 만들고 싶을 때

---

### 2. Embedding 기반 Retrieval 모델
**설명**  
사용자 행동/프로필 데이터를 바탕으로 dense embedding을 학습하고, seed audience와 가까운 사용자를 nearest neighbor 검색으로 찾는 방식이다.

**대표 사례**
- Pinterest (2019)
- Walmart (2023)
- 네이버 DEVIEW 2021

**핵심 특징**
- 현대적인 lookalike 시스템의 가장 일반적인 형태
- 대규모 검색에 적합
- ANN(Approximate Nearest Neighbor)과 결합하기 좋음
- 표현력과 확장성의 균형이 좋음

**세부 하위 유형**
- **일반 embedding retrieval형**: Pinterest, Walmart
- **광고 성과 최적화형**: 네이버  
  단순 유사도뿐 아니라 CTR, CVR, CTCVR 개선과 serving latency를 함께 고려

**언제 적합한가**
- 대규모 사용자 풀에서 빠르게 후보를 찾고 싶을 때
- 오프라인 표현 학습 + 온라인 검색 구조를 만들고 싶을 때
- 실무적으로 가장 무난한 첫 구현이 필요할 때

---

### 3. Graph 기반 Lookalike 모델
**설명**  
사용자-상품-행동-캠페인 관계를 그래프로 보고, 그래프 위에서 유사 사용자나 확장 audience를 찾는 방식이다.

**대표 사례**
- Yahoo (2016)
- Graph-Based Audience Expansion Model for Marketing Campaigns (SIGIR 2024)

**핵심 특징**
- 사용자 간 직접 유사도뿐 아니라 구조적 연결관계를 반영 가능
- sparse interaction 환경에 강할 수 있음
- 그래프 구축 비용과 운영 복잡도가 큼

**세부 하위 유형**
- **후보군 축소형 graph retrieval**: Yahoo  
  global graph를 만든 뒤 campaign-specific modeling 수행
- **관계 학습형 graph modeling**: SIGIR 2024  
  audience expansion 자체를 graph 문제로 재정의

**언제 적합한가**
- 유저-상품-콘텐츠-카테고리 간 관계가 풍부할 때
- 단순 벡터 유사도보다 구조 정보가 중요할 때
- candidate generation layer를 고도화하고 싶을 때

---

### 4. Graph + LLM / Semantic Hybrid 모델
**설명**  
그래프 구조와 텍스트 의미 정보를 함께 사용해 lookalike를 찾는 방식이다.  
예를 들어 product reviews, item descriptions, LLM-generated user/item profiles를 그래프 정보와 함께 사용한다.

**대표 사례**
- Lookalike Audience Expansion: A Graph-Based Model with LLMs (GLoM, 2025)

**핵심 특징**
- 구조 정보 + semantic 정보 동시 활용
- 텍스트 자산이 많은 서비스에 유리
- private user feature 의존도를 줄일 수 있음
- 아직은 최신 연구 단계의 성격이 강함

**언제 적합한가**
- 리뷰, 검색어, 상품 설명, 상담 텍스트가 중요할 때
- 구조 정보만으로는 사용자 의미를 충분히 설명하기 어려울 때

---

### 5. Meta-learning 기반 Audience Expansion 모델
**설명**  
여러 캠페인에서 공통적으로 통하는 lookalike 확장 전략을 학습한 뒤, 새로운 캠페인에 빠르게 적응하는 방식이다.

**대표 사례**
- MetaHeac (KDD 2021)

**핵심 특징**
- seed가 작은 캠페인에 강함
- 캠페인 간 일반화에 유리
- 학습 구조가 복잡하고 운영 난이도가 높음

**언제 적합한가**
- 캠페인 수가 많고 각각의 데이터가 적을 때
- cold-start campaign 대응이 중요할 때

---

### 6. Generative Targeting 모델
**설명**  
기존 lookalike처럼 “비슷한 사람 찾기”만 하는 것이 아니라, 타깃 audience 정의 자체를 생성형 모델로 유연하게 만드는 방식이다.

**대표 사례**
- GATS (SIGIR 2024)

**핵심 특징**
- marketer 친화적
- 자연어 기반 타깃 정의 가능
- lookalike보다 더 넓은 audience design 문제로 확장됨
- 엄밀한 의미의 seed-based lookalike와는 다소 다름

**언제 적합한가**
- 마케터가 직접 타깃 조건을 생성/수정하고 싶을 때
- recommendation보다 campaign planning 성격이 강할 때

---

### 7. Few-shot / Zero-shot Audience Expansion
**설명**  
seed가 매우 적거나 거의 없는 신규 캠페인에서도 audience expansion이 가능하도록, 사전학습된 user/task model을 활용하는 방식이다.

**대표 사례**
- Few-shot and Zero-shot Audience Expansion with User and Task Model Pre-training on Tabular Data (WWW 2026 short)

**핵심 특징**
- low-data campaign에 유리
- 장기적으로 매우 중요한 방향
- 아직은 실무 표준이라기보다 추적이 필요한 최신 연구 영역

**언제 적합한가**
- 신규 광고주/신규 캠페인이 자주 생길 때
- 라벨 수가 매우 적은 환경일 때

---

## 기술 카테고리별 대표 사례 매핑

| 카테고리 | 대표 사례 | 핵심 키워드 |
|---|---|---|
| 규칙/통계 기반 Similarity | Adobe | TF-IDF, cosine similarity, audience mean vector |
| Embedding 기반 Retrieval | Pinterest, Walmart, 네이버 | user embedding, ANN, retrieval, performance-aware serving |
| Graph 기반 | Yahoo, SIGIR 2024 | user graph, nearest-neighbor filtering, graph modeling |
| Graph + LLM Hybrid | GLoM | GNN, LLM, review text, semantic profile |
| Meta-learning 기반 | MetaHeac | cross-campaign generalization, small seed adaptation |
| Generative Targeting | GATS | LLM, audience generation, interpretable targeting |
| Few-shot / Zero-shot | WWW 2026 short | user/task pretraining, low-data expansion |

---

## 한 줄 정리

현재 lookalike 기술은 아래 흐름으로 발전하고 있다고 볼 수 있다.

1. **Similarity 기반**
2. **Embedding retrieval 기반**
3. **Graph 기반**
4. **Graph + semantic/LLM 기반**
5. **Meta-learning / few-shot 기반**
6. **Generative audience design 기반**

즉, 과거에는 “닮은 사람 찾기” 자체가 중심이었다면,  
최근에는 “적은 seed로도 확장 가능하게 만들기”, “텍스트 의미까지 반영하기”,  
그리고 “마케터가 직접 타깃을 생성하게 만들기”로 진화하고 있다.

## 참고 링크

- [Pinterest - Finding Users Who Act Alike: Transfer Learning for Expanding Advertiser Audiences](https://www.pinterestcareers.com/media/phkg2uau/transferlearning-kdd2019.pdf)
- [Yahoo - A Sub-linear, Massive-scale Look-alike Audience Extension System](http://proceedings.mlr.press/v53/ma16.pdf)
- [MetaHeac - Learning to Expand Audience via Meta Hybrid Experts and Critics for Recommendation and Advertising](https://arxiv.org/pdf/2105.14688)
- [Naver DEVIEW 2021 - Look-alike Modeling and Serving 비슷한 사람을 찾아주세요](https://deview.kr/data/deview/session/attach/6_Look-alike%20Modeling%20and%20Serving%20비슷한%20사람을%20찾아주세요.pdf)
- [Walmart - Finding Lookalike Customers for E-Commerce Marketing](https://arxiv.org/pdf/2301.03147)
- [Rakuten - Exploring 360-Degree View of Customers for Lookalike Modeling](https://arxiv.org/abs/2304.09105)
- [Adobe Tech Blog - Look-Alike Audiences: AI-enabled Audience Expansion in Real-Time CDP](https://medium.com/adobetech/look-alike-audiences-ai-enabled-audience-expansion-in-real-time-cdp-e143a1ce93de)
- [SIGIR 2024 - Graph-Based Audience Expansion Model for Marketing Campaigns](https://dl.acm.org/doi/10.1145/3626772.3661363)
- [SIGIR 2024 - GATS: Generative Audience Targeting System for Online Advertising](https://dl.acm.org/doi/10.1145/3626772.3661372)
- [SIGIR eCom 2025 - Lookalike Audience Expansion: A Graph-Based Model with LLMs](https://ceur-ws.org/Vol-4123/paper_13.pdf)
- [WWW 2026 Short Paper - Few-shot and Zero-shot Audience Expansion with User and Task Model Pre-training on Tabular Data](https://www2026.thewebconf.org/accepted/short-papers.html)
