# CTR Prediction 논문 비교 정리

## CTR Prediction 논문 비교표

| 논문 / 사례 | 문제정의 | 데이터 | 방법 | 장점 | 한계 | 우리 서비스 적용 포인트 |
|---|---|---|---|---|---|---|
| **Wide & Deep Learning for Recommender Systems (Google, 2016)** | sparse categorical feature가 많은 추천/광고 환경에서 memorization과 generalization을 동시에 달성하는 문제 | Google Play production 환경의 대규모 추천/광고성 데이터 | wide linear model + deep neural network를 joint training하는 hybrid 구조 | CTR/추천 분야의 대표적인 산업형 baseline. 해석 가능한 wide와 일반화에 강한 deep를 결합 | feature interaction 표현력이 이후 모델들보다 제한적 | CTR prediction의 가장 기본적인 실무 baseline으로 적합. 우리 서비스에서도 **wide feature cross + embedding MLP** 구조로 빠르게 MVP를 만들 수 있음 |
| **DeepFM: A Factorization-Machine based Neural Network for CTR Prediction (IJCAI 2017)** | 별도의 수작업 feature engineering 없이 low-order와 high-order interaction을 동시에 학습하는 문제 | benchmark 데이터 + commercial data | FM + DNN을 end-to-end로 결합한 구조 | 구현이 비교적 단순하면서도 성능이 강한 대표 모델. Wide & Deep보다 입력 설계가 더 간결 | user sequence나 time dynamics 반영은 약함 | 범용 CTR baseline으로 매우 유용. tabular/categorical feature가 중심인 광고 시스템에서 첫 고도화 모델로 적합 |
| **xDeepFM: Combining Explicit and Implicit Feature Interactions for Recommender Systems (KDD 2018)** | plain DNN이 feature interaction을 implicit하게만 학습하는 한계를 개선하는 문제 | 실세계 추천/CTR 데이터셋 | CIN(Compressed Interaction Network) + DNN으로 explicit/implicit interaction 동시 학습 | feature cross를 더 강하게 모델링 가능 | 구조가 DeepFM보다 복잡하고 튜닝 포인트가 늘어남 | 광고주, 상품, placement, 디바이스, 시간대 등 **명시적 조합 효과**가 중요한 서비스에 적합 |
| **AutoInt: Automatic Feature Interaction Learning via Self-Attentive Neural Networks (CIKM 2019)** | sparse high-dimensional feature 간 고차 상호작용을 자동으로 학습하는 문제 | CTR prediction benchmark 데이터 | self-attention으로 feature interaction을 학습 | 수작업 cross 없이 고차 interaction 학습 가능. attention 기반이라 해석 여지도 일부 존재 | sequence modeling 자체를 위한 구조는 아니며, attention tuning 비용이 있음 | feature 종류가 많고 상호작용 구조가 복잡할 때 유용. 금융/커머스 광고에서 다양한 context feature를 함께 쓸 때 적합 |
| **Deep Interest Network (DIN, KDD 2018)** | user behavior history를 고정 길이 벡터로 압축하면 candidate ad별 관심사를 충분히 반영하지 못한다는 문제 | Alibaba production data + public datasets | candidate ad conditioned attention으로 user interest를 동적으로 표현 | 광고/상품별로 다른 user interest를 반영 가능. sequence 기반 CTR 모델의 대표 출발점 | behavior sequence가 충분히 있어야 효과적 | 사용자의 클릭/조회/구매 로그가 풍부하다면 매우 중요. 단순 tabular CTR보다 **behavior-aware pCTR**로 발전시킬 때 핵심 참고 모델 |
| **Deep Interest Evolution Network (DIEN, AAAI 2019)** | user interest가 시간에 따라 변화하는데, 기존 모델이 interest evolution을 충분히 반영하지 못하는 문제 | public datasets + Taobao display advertising system | interest extractor + interest evolving layer를 통한 sequential interest modeling | 동적 관심사 변화를 반영해 CTR 예측 정확도 향상. 논문에서는 Taobao 시스템 배포 및 CTR 20.7% 개선 사례 제시 | sequence 길이, 학습 난이도, 서빙 복잡도가 증가 | 최근 행동과 과거 행동의 의미가 다르고, time-aware modeling이 중요한 서비스에 적합 |
| **DCN V2: Improved Deep & Cross Network and Practical Lessons for Web-scale Learning to Rank Systems (WWW 2021)** | explicit feature crossing을 더 expressive하면서도 실무적으로 효율적으로 학습하는 문제 | Google의 web-scale learning-to-rank 환경 + benchmark datasets | improved cross network + low-rank / mixture 구조 | production 적용을 강하게 의식한 구조. explicit cross 학습 성능과 효율성을 함께 강조 | behavior sequence modeling은 별도 설계 필요 | strong baseline으로 매우 추천. 대규모 광고 랭킹 시스템에서 **실무형 CTR/LTR backbone**으로 쓰기 좋음 |
| **BARS-CTR: Open Benchmarking for Click-Through Rate Prediction (2024 update)** | CTR 논문들이 서로 다른 데이터셋/튜닝 조건으로 비교되어 재현성이 낮다는 문제 | 여러 공개 CTR benchmark datasets | 24개 CTR 모델을 대규모 재평가한 reproducible benchmark | “새 모델”보다 **튜닝, 데이터셋, 평가 프로토콜**이 더 중요하다는 점을 보여줌 | 새로운 모델 제안이 아니라 benchmark 성격 | 우리 서비스에서 모델 선택 시 논문 headline만 보기보다 **같은 데이터/같은 feature/같은 튜닝 budget**으로 재평가해야 함을 시사 |
| **A Comprehensive Survey on Advertising Click-Through Rate Prediction Algorithm (2025)** | CTR prediction 연구 전반을 shallow/deep/sequence/graph 등으로 체계화하는 문제 | 기존 CTR prediction 문헌 전반 | survey paper | CTR 기술 발전 흐름을 정리하기 좋음 | 직접적인 실험 성능 비교 자료는 아님 | 내부 기술 전략 문서 작성 시 전체 landscape를 설명하는 참고 문헌으로 적합 |
| **A Generative Paradigm of CTR Prediction Models (ICML 2026 poster)** | 기존 discriminative CTR 모델의 표현 한계를 generative paradigm으로 바꾸어 개선하려는 문제 | CTR prediction task 전반 | feature embedding을 생성 관점으로 재구성하는 최신 접근 | CTR prediction의 최신 연구 방향성 파악에 유용 | 아직 실무 표준이라 보기에는 이르며, production 검증은 제한적일 수 있음 | 당장 도입보다는 최신 기술 추적 후보. 장기적으로 representation learning 개선 관점에서 관심 가질 만함 |

---

## CTR 기술 카테고리 정리

CTR prediction 기술은 하나의 고정된 방법이라기보다,  
**(1) feature interaction을 어떻게 표현할 것인지**,  
**(2) user behavior sequence를 어떻게 반영할 것인지**,  
**(3) online ranking / bidding / calibration에 어떻게 연결할 것인지**의 조합으로 이해하는 것이 좋다.

### 1. Wide + Deep Hybrid 계열

#### 설명
선형 모델의 memorization 능력과 deep model의 generalization 능력을 결합하는 고전적 구조이다.  
광고 CTR prediction에서 가장 오래되고 널리 쓰인 산업형 baseline이다.

#### 대표 사례
- Wide & Deep (Google, 2016)

#### 핵심 특징
- 산업 적용성이 높음
- 구현과 운영이 비교적 안정적임
- sparse categorical feature 처리에 강함
- 복잡한 고차 interaction이나 behavior sequence 반영은 제한적임

#### 언제 적합한가
- CTR prediction MVP를 빠르게 만들고 싶을 때
- baseline으로 strong하고 해석 가능한 구조가 필요할 때
- user history보다 context / item / advertiser / placement feature가 중심일 때

---

### 2. Feature Interaction 강화 계열

#### 설명
CTR 예측에서 핵심이 되는 categorical feature 간 조합 효과를 더 잘 학습하기 위해 explicit/implicit interaction을 강화한 계열이다.

#### 대표 사례
- DeepFM
- xDeepFM
- AutoInt
- DCN V2

#### 핵심 특징
- tabular 광고 데이터에 매우 잘 맞음
- feature cross engineering 부담을 줄여줌
- sequence 없이도 strong baseline이 가능함
- user interest dynamics 자체를 학습하는 데에는 한계가 있음

#### 세부 하위 유형
- **FM hybrid형**: DeepFM  
  low-order + high-order interaction을 동시에 학습
- **explicit cross 강화형**: xDeepFM, DCN V2  
  조합 효과를 더 직접적으로 학습
- **attention interaction형**: AutoInt  
  self-attention으로 고차 feature interaction을 자동 학습

#### 언제 적합한가
- 광고 요청, 지면, 디바이스, 시간대, 소재 속성 간 조합 효과가 중요할 때
- user history가 약해도 context-rich feature가 충분할 때
- 실무에서 가장 무난한 CTR 고도화 축이 필요할 때

---

### 3. Behavior Sequence / Interest Modeling 계열

#### 설명
사용자의 과거 행동 로그를 바탕으로, 후보 광고나 상품에 대해 현재 관심사가 무엇인지 동적으로 추정하는 방식이다.

#### 대표 사례
- DIN
- DIEN

#### 핵심 특징
- 광고나 아이템별로 user interest를 다르게 표현 가능
- e-commerce / feed / display advertising에 강함
- user behavior history가 풍부할수록 성능 개선 여지가 큼
- 학습/서빙 복잡도가 증가함

#### 세부 하위 유형
- **candidate-conditioned interest형**: DIN  
  광고별로 다른 관심사 representation을 생성
- **interest evolution형**: DIEN  
  관심사의 시간적 변화까지 반영

#### 언제 적합한가
- 클릭, 조회, 장바구니, 구매 등 sequence 로그가 풍부할 때
- 같은 사용자라도 광고/상품별 반응이 크게 달라질 때
- static tabular model보다 dynamic interest model이 필요한 서비스일 때

---

### 4. Benchmark / Reproducibility 계열

#### 설명
CTR prediction 분야는 논문마다 데이터셋, 전처리, 튜닝 budget이 달라 공정한 비교가 어렵다.  
이 문제를 해결하기 위해 동일한 조건에서 여러 모델을 재평가하는 benchmark 계열 연구가 중요해졌다.

#### 대표 사례
- BARS-CTR

#### 핵심 특징
- 모델 headline보다 재현 가능한 비교가 중요함을 강조
- strong baseline의 중요성을 보여줌
- 같은 feature set / same tuning budget 비교가 핵심임

#### 언제 적합한가
- 내부 모델 선정 기준을 정할 때
- 논문 성능을 그대로 믿기보다 우리 데이터에서 재검증해야 할 때
- 조직 차원의 CTR experimentation framework를 만들고 싶을 때

---

### 5. Survey / 최신 연구 방향 계열

#### 설명
CTR prediction이 wide-deep, feature interaction, sequence, graph, generative representation 등으로 어떻게 발전해왔는지를 정리하는 계열이다.

#### 대표 사례
- A Comprehensive Survey on Advertising Click-Through Rate Prediction Algorithm (2025)
- A Generative Paradigm of CTR Prediction Models (ICML 2026 poster)

#### 핵심 특징
- 기술 지도 작성에 유리
- 장기 연구 방향 탐색에 적합
- 바로 production에 넣을 모델이라기보다 기술 전략 수립용 자료에 가까움

#### 언제 적합한가
- 내부 기술 로드맵을 작성할 때
- 최신 연구 동향을 파악하고 싶을 때
- graph / generative / multimodal CTR의 차세대 방향을 추적할 때

---

## 기술 카테고리별 대표 사례 매핑

| 카테고리 | 대표 사례 | 핵심 키워드 |
|---|---|---|
| Wide + Deep Hybrid | Wide & Deep | memorization, generalization, wide cross, deep embedding |
| Feature Interaction 강화 | DeepFM, xDeepFM, AutoInt, DCN V2 | feature crossing, explicit interaction, implicit interaction, self-attention |
| Behavior Sequence / Interest Modeling | DIN, DIEN | user behavior sequence, interest attention, interest evolution |
| Benchmark / Reproducibility | BARS-CTR | reproducibility, strong baseline, tuning fairness |
| Survey / 최신 연구 방향 | Survey 2025, Generative CTR 2026 | research landscape, generative paradigm, future direction |

---

## 한 줄 정리

현재 CTR prediction 기술은 아래 흐름으로 발전하고 있다고 볼 수 있다.

1. Wide + Deep baseline  
2. Feature interaction 강화  
3. User behavior sequence / interest modeling  
4. Reproducible benchmarking  
5. Generative / 차세대 representation learning

즉, 과거에는 **sparse feature를 잘 조합해 클릭을 맞추는 것**이 중심이었다면,  
최근에는 **사용자의 관심사 변화를 반영하고**, **공정하게 벤치마킹하며**,  
장기적으로는 **더 풍부한 representation 자체를 학습하는 방향**으로 진화하고 있다.

---

## CTR Prediction 목표 설정에 대한 의견

CTR prediction 모델 개발 시 **“광고 효율 CTR 벤치마크 대비 120% 이상 달성”** 같은 목표를 설정할 수는 있지만, 이를 **기본 목표**로 두기보다는 **stretch goal**로 두는 것이 더 합리적이다.

문헌을 종합하면 다음과 같이 해석하는 것이 적절하다.

### 문헌 기반 원칙

- CTR 모델은 보통 **offline metric(AUC, LogLoss)** 과 **online metric(CTR uplift, revenue uplift)** 를 함께 본다.
- production strong baseline 대비 online CTR **20% uplift** 는 일반적인 기본 목표라기보다 **매우 공격적인 목표**에 가깝다.
- sequence 기반 강한 개선이 들어간 경우에는 큰 uplift가 가능할 수 있으나, 모든 환경에서 재현되는 수치로 보기는 어렵다.
- strong baseline 교체에서는 **single-digit ~ low double-digit uplift** 도 충분히 의미 있는 성과일 수 있다.

### 실무 제안 수치

- **MVP 목표**: benchmark 대비 CTR **103~108%**
- **1차 상용화 목표**: benchmark 대비 CTR **108~115%**
- **도전 목표(stretch goal)**: benchmark 대비 CTR **115~120%+**

### 해석

- benchmark가 약한 heuristic / LR baseline이면 **120%** 목표도 가능할 수 있다.
- benchmark가 이미 strong baseline(예: DCN 계열 + user behavior feature + calibration)이라면 **120%는 매우 공격적**이다.
- 따라서 내부 기획서에는 보통 아래처럼 쓰는 것이 더 설득력 있다.

> **권장 문장 예시**  
> “기존 CTR benchmark 대비 online CTR을 8~12% 개선하는 것을 1차 목표로 하며, CVR / Revenue / Calibration guardrail을 유지하는 조건에서 15~20% 개선을 도전 목표로 설정한다.”

---

## 참고 링크

아래 링크는 모두 **일반 Markdown 링크 형식**으로 다시 정리하였다.

- [Wide & Deep Learning for Recommender Systems](https://arxiv.org/abs/1606.07792)
- [DeepFM: A Factorization-Machine based Neural Network for CTR Prediction](https://arxiv.org/abs/1703.04247)
- [xDeepFM: Combining Explicit and Implicit Feature Interactions for Recommender Systems](https://arxiv.org/abs/1803.05170)
- [AutoInt: Automatic Feature Interaction Learning via Self-Attentive Neural Networks](https://arxiv.org/abs/1810.11921)
- [Deep Interest Network for Click-Through Rate Prediction](https://arxiv.org/abs/1706.06978)
- [Deep Interest Evolution Network for Click-Through Rate Prediction](https://arxiv.org/abs/1809.03672)
- [DCN V2: Improved Deep & Cross Network and Practical Lessons for Web-scale Learning to Rank Systems](https://arxiv.org/abs/2008.13535)
- [BARS-CTR: Open Benchmarking for Click-Through Rate Prediction](https://arxiv.org/abs/2009.05794)
- [A Comprehensive Survey on Advertising Click-Through Rate Prediction Algorithm](https://www.cambridge.org/core/journals/knowledge-engineering-review/article/comprehensive-survey-on-advertising-clickthrough-rate-prediction-algorithm/C11C54F5D365D280A58CE051D9B52DE6)
- [A Generative Paradigm of CTR Prediction Models (ICML 2026 poster page)](https://icml.cc/virtual/2025/poster/45999)

---

## 참고 메모

- 실무에서는 CTR prediction 모델을 **AUC/LogLoss 개선**만으로 평가하지 않고, 반드시 **online A/B test에서 CTR, CVR, Revenue, Calibration** 을 함께 점검하는 것이 바람직하다.
- 논문에 보고된 uplift 수치는 **데이터셋, baseline 강도, feature quality, serving 환경**에 따라 크게 달라질 수 있으므로, 내부 목표 수치는 논문 수치를 그대로 복사하기보다 **우리 서비스 baseline 수준**을 반영해 재설정해야 한다.

## 대표 Multi-domain CTR 논문
1) STAR
One Model to Serve All: Star Topology Adaptive Recommender for Multi-Domain CTR Prediction https://arxiv.org/pdf/2101.11427 가장 대표적인 출발점으로 많이 인용됩니다. 여러 비즈니스 도메인에 대해 shared centered parameters + domain-specific parameters를 함께 두어, 하나의 모델이 여러 도메인을 동시에 서빙하도록 설계했습니다. Alibaba display advertising system에 배포되었고, 논문에서는 평균 CTR 8.0%, RPM 6.0% 개선을 보고합니다. 
2) AdaSparse
AdaSparse: Learning Adaptively Sparse Structures for Multi-Domain Click-Through Rate Prediction https://arxiv.org/pdf/2206.13108 도메인별로 중요한 신경망 구조가 다르다는 점에 주목해서, domain마다 다른 sparse structure를 adaptively 학습하는 방식입니다. multi-domain CTR에서 generalization과 deployment complexity를 동시에 문제로 보고 접근한 논문입니다. 
3) Uni-CTR
A Unified Framework for Multi-Domain CTR Prediction via Large Language Models LLM을 활용해 domain 공통 semantic representation을 강화하고, 동시에 pluggable domain-specific network로 domain 특성도 반영하는 구조입니다. 논문에서는 multi-domain CTR의 seesaw phenomenon과 insufficient generalization 문제를 직접 다룹니다. 
4) Star+
Star+: A New Multi-Domain Model for CTR Prediction https://arxiv.org/pdf/2406.16568 STAR를 확장한 후속 연구 성격의 논문입니다. 단순 shared-vs-domain split을 넘어서 복잡한 inter-domain relationship을 더 잘 반영하려는 방향으로 제안되었습니다. 최신 academic follow-up으로 보기 좋습니다. 
5) MLoRA
MLoRA: Multi-Domain Low-Rank Adaptive Network for CTR Prediction 기존 multi-domain 모델들이 domain module 때문에 파라미터가 커지고 학습이 비효율적이라는 점을 문제로 보고, domain별 LoRA module을 붙이는 방식으로 개선한 논문입니다. Alibaba.com production environment 배포와 online A/B superiority도 언급합니다. 
같이 보면 좋은 인접 논문 / 벤치마크
6) MDL
MDL: A Unified Multi-Distribution Learner in Large-scale Recommender Systems 제목 자체는 CTR 전용은 아니지만, 대규모 recommendation에서 multi-distribution / multi-domain 문제를 통합적으로 다루며 STAR 계열을 잇는 흐름으로 볼 수 있습니다. multi-domain CTR 문헌 리뷰에서 함께 언급되는 편입니다. 
7) Multi-scenario benchmark / related work
최근 benchmark나 survey 성격의 문헌에서는 AdaSparse를 multi-scenario CTR prediction 대표 모델로 정리하고 있습니다. 실무에서는 domain, scenario, scene이 거의 유사한 문제 설정으로 등장하는 경우가 많아 같이 보는 게 도움이 됩니다. 
흐름으로 보면
대략 이렇게 발전했다고 보시면 됩니다.
* STAR (2021): multi-domain CTR의 대표 출발점. “하나의 모델로 여러 domain 서빙”을 본격화. 
* AdaSparse (2022): domain마다 다른 sparse sub-network를 학습. 
* Uni-CTR (2023): LLM semantic representation을 multi-domain CTR에 결합. 
* Star+ / MLoRA (2024): STAR 계열의 구조 개선, parameter-efficient adaptation 강화.
