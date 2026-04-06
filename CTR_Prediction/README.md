# CTR Prediction 논문 비교 정리

아래 정리는 사용자가 제공한 Lookalike / Audience Expansion 정리 형식에 맞춰, **CTR Prediction 모델**을 같은 문체와 구조로 재정리한 버전이다. fileciteturn0file0

## 논문 비교표

| 논문 / 사례 | 문제정의 | 데이터 | 방법 | 장점 | 한계 | 우리 서비스 적용 포인트 |
|---|---|---|---|---|---|---|
| Wide & Deep Learning for Recommender Systems (Google, 2016) | sparse한 광고/추천 feature 환경에서 memorization과 generalization을 동시에 달성하는 CTR/랭킹 문제 | Google Play의 대규모 추천·랭킹 데이터 | wide linear model + deep neural network를 joint training하는 hybrid 구조 | CTR 계열 산업 모델의 고전적 기준선. 구조가 단순하고 production 친화적이며, cross feature와 embedding generalization을 함께 가져갈 수 있음 | 최근 모델 대비 feature interaction 표현력이 제한적이고, sequence behavior modeling은 약함 | CTR prediction MVP의 매우 좋은 baseline. 수작업 cross feature가 일부 가능하고, tabular sparse feature 중심이라면 가장 먼저 검토할 가치가 큼. Google은 productionized system에서 online app acquisitions 증가를 보고했다. citeturn613937search1 |
| DeepFM: An End-to-End Wide & Deep Learning Framework for CTR Prediction (2017/2018) | 별도 feature engineering 없이 low-order와 high-order interaction을 동시에 학습하는 CTR 문제 | benchmark dataset + Huawei App Market production 데이터 | FM로 low-order interaction, DNN으로 high-order interaction을 함께 학습하는 end-to-end 모델 | 실무성과 연구 인용도가 모두 높음. raw feature만으로도 강력한 baseline이 되며, wide & deep 대비 구현과 운용이 비교적 간결함 | sequence user interest, long behavior history, target-aware interest 변화는 직접적으로 반영하지 못함 | 현재 서비스에서 가장 무난한 1순위 CTR baseline 중 하나. tabular/sparse/categorical feature가 중심이라면 우선적으로 실험할 가치가 높음. Huawei App Market A/B에서 LR 대비 10% 이상 CTR 개선이 보고되었다. citeturn613937search2 |
| xDeepFM: Combining Explicit and Implicit Feature Interactions for Recommender Systems (2018) | 단순 DNN이 feature interaction을 암묵적으로만 학습하는 한계를 줄이고 explicit high-order interaction까지 강화하려는 CTR 문제 | CTR benchmark 데이터셋 | CIN(Compressed Interaction Network) + DNN을 결합해 explicit/implicit interaction을 모두 모델링 | 고차 feature interaction을 더 직접적으로 반영할 수 있어, 복잡한 범주형 조합이 많은 광고 데이터에 유리할 수 있음 | 모델 구조가 DeepFM보다 복잡하고, 데이터/튜닝에 따라 추가 이득이 크지 않을 수 있음 | 카테고리, 디바이스, placement, advertiser, creative 속성 간 조합 효과가 크다면 유용하다. 다만 strong baseline 대비 이득은 튜닝 품질에 좌우될 가능성이 크다. citeturn779778search5turn779778search16 |
| AutoInt: Automatic Feature Interaction Learning via Self-Attentive Neural Networks (2018) | 수작업 cross feature 없이 중요한 feature interaction을 자동으로 학습하려는 CTR 문제 | 여러 real-world CTR dataset | self-attention 기반으로 feature interaction을 자동 학습하는 구조 | attention을 이용해 어떤 feature interaction이 중요한지 해석 가능성이 일부 있고, 고차 상호작용 자동 학습에 강점이 있음 | 긴 user behavior sequence 자체를 다루는 모델은 아니며, sparse production feature에서 튜닝 민감도가 있을 수 있음 | 광고주, 사용자, 상품, 시간대, placement 간 상호작용을 자동 탐색하고 싶을 때 유용하다. feature engineering 비용을 줄이는 방향의 후보 모델로 적합하다. citeturn779778search2 |
| Deep Interest Network (DIN, Alibaba, 2018) | 동일 사용자라도 candidate ad/item에 따라 관심사가 달라진다는 점을 반영해야 하는 CTR 문제 | 전자상거래/광고 user behavior sequence 데이터 | target item/ad에 relevant한 user behavior만 더 강하게 반영하는 local activation 기반 interest modeling | “광고별로 달라지는 관심사”를 직접 모델링해 behavior sequence 활용도가 높음 | user history가 짧거나 sparse하면 이점이 제한될 수 있고, serving cost가 단순 MLP보다 큼 | 사용자의 최근 클릭/조회/구매 이력이 충분하고, 노출 대상 광고와 관련 행동이 중요하다면 strong candidate가 된다. 특히 추천형 광고나 상품광고에 잘 맞는다. citeturn613937search3 |
| Deep Interest Evolution Network (DIEN, Alibaba/Taobao, 2019) | user interest가 시간에 따라 진화한다는 점까지 반영해야 하는 CTR 문제 | public + industrial dataset, Taobao display ad system | interest extractor + interest evolving layer로 behavior sequence의 latent interest 변화까지 모델링 | sequence/interest modeling 계열의 대표 논문. behavior history가 풍부할수록 강력할 가능성이 큼 | 학습/서빙 구조가 복잡하고, 모든 서비스에서 reported uplift가 그대로 재현되지는 않음 | 커머스/콘텐츠형 광고처럼 user action history가 풍부한 서비스에 특히 적합하다. Taobao display ad system 배포에서 20.7% CTR 개선이 보고되었다. 다만 이는 특정 baseline과 데이터 환경에서의 결과로 해석하는 것이 안전하다. citeturn613937search3turn613937search7 |
| DCN V2: Improved Deep & Cross Network and Practical Lessons for Web-scale Learning to Rank Systems (Google, 2020) | web-scale ranking/CTR 환경에서 feature cross를 더 효율적이고 강력하게 학습하려는 문제 | Criteo, MovieLens-1M 및 Google의 web-scale ranking system 사례 | 개선된 cross network와 low-rank mixture를 결합한 practical CTR/ranking architecture | production practicality가 강하고, feature cross 학습을 효율적으로 수행하며 industrial ranking system에서의 교훈까지 제공 | pure sequence interest modeling보다는 tabular feature interaction 쪽에 더 강점이 있음 | 현재 CTR 모델을 industrial-grade로 고도화할 때 가장 참고 가치가 큰 논문 중 하나다. strong tabular baseline이 필요하고 latency 제약이 크다면 특히 유용하다. Google은 offline accuracy와 online business metrics gain을 보고했다. citeturn779778search0turn779778search3 |
| Open Benchmarking for Click-Through Rate Prediction (BARS / FuxiCTR, 2020) | CTR 모델 논문들의 비교가 재현 가능하지 않고 실험 조건에 따라 결과가 크게 흔들리는 문제 | 여러 public CTR benchmark, 24개 모델 재평가 | reproducible benchmark와 통일된 실험 환경에서 대규모 재평가 수행 | “새 모델이 항상 큰 차이를 만드는 것은 아니다”라는 점을 보여줘 실무적으로 매우 중요함 | 새로운 모델을 제안하는 논문은 아니며, 직접적인 serving architecture를 주지는 않음 | 우리 서비스에서 가장 중요한 메시지는 아키텍처보다 데이터 설계·feature·튜닝·평가 프로토콜이 더 중요할 수 있다는 점이다. strong baseline 없이 최신 모델로 바로 가는 접근을 경계하게 해준다. citeturn779778search4turn779778search16 |
| A Comprehensive Survey on Advertising Click-Through Rate Prediction Algorithm (2025 survey) | CTR prediction 연구를 shallow interaction 계열과 deep learning 계열 전반으로 정리하는 survey | 기존 CTR prediction 연구 전반 | shallow model과 deep CTR model을 체계적으로 분류하고 발전 흐름을 정리 | CTR 연구 흐름을 한 번에 파악하기 좋고, 실무자가 전체 landscape를 빠르게 이해하는 데 유용 | survey이므로 특정 production architecture를 직접 제안하지는 않음 | CTR 모델 기획 문서나 기술 검토 보고서 작성 시 매우 좋은 참고 문헌이다. 현재 CTR 기술 흐름을 broad하게 정리할 때 적합하다. citeturn613937search0turn613937search4 |
| Generative Click-through Rate Prediction with Applications in Sponsored Search Auctions (2025) | 기존 discriminative CTR model이 label sparsity, cold-start, uncertain click generation을 충분히 설명하지 못하는 문제 | sponsored search 및 CTR log 기반 설정 | 생성형 관점의 CTR 예측 프레임워크를 제안하고 auction/application까지 연결 | 최근 CTR 연구가 generative modeling 방향으로 확장되고 있음을 보여줌 | 아직 CTR 실무의 표준이라기보다 최신 연구 흐름에 가까우며, 구현 복잡도가 높음 | 곧바로 1차 production 도입보다는 중장기 R&D 후보로 적합하다. 특히 sparse click, uncertainty modeling, auction coupling까지 함께 보고 싶을 때 의미가 있다. citeturn613937search12 |
| IDProxy: Cold-Start CTR Prediction for Ads and Recommendation (2026) | item/ad ID embedding에 크게 의존하는 CTR 시스템이 신규 광고·신규 아이템에서 약한 cold-start 문제 | Xiaohongshu의 recommendation/advertising setting | ID 의존성을 완화하고 cold-start item/ad에 대응하는 최신 CTR prediction 접근 | 최신 production 문제인 cold-start CTR을 정면으로 다루며, 광고/추천 공통 이슈에 relevance가 높음 | 매우 최신 연구라 범용 표준으로 보기에는 이르며, 서비스 데이터 구조와의 적합성 검토가 필요 | 광고 소재나 신규 상품이 자주 바뀌는 서비스라면 추적 가치가 높다. creative text/image/profile signal을 더 잘 활용하는 방향으로 연결될 수 있다. citeturn613937search8 |

## CTR Prediction 기술 카테고리 정리

CTR prediction 기술은 하나의 고정된 방법이라기보다,  
**feature representation**,  
**feature interaction / user interest modeling**,  
**prediction calibration 및 serving**,  
그리고 **online optimization 목표**의 조합으로 이해하는 것이 좋다.

### 1. Wide & Deep Hybrid 기반 모델

#### 설명
wide linear component와 deep neural network를 결합해,  
한편으로는 memorization(자주 나타나는 조합 기억),  
다른 한편으로는 generalization(보지 못한 조합 일반화)을 함께 노리는 방식이다.

#### 대표 사례
- Wide & Deep
- DeepFM

#### 핵심 특징
- CTR prediction의 가장 고전적이고 실무적인 출발점
- sparse categorical feature에 강함
- 비교적 구현이 단순하고 production 이전 단계 baseline으로 좋음
- user behavior sequence를 직접 다루는 데는 한계가 있음

#### 언제 적합한가
- 첫 CTR baseline이 필요할 때
- tabular + sparse categorical feature가 중심일 때
- latency와 구현 복잡도를 크게 늘리지 않고 성능을 올리고 싶을 때

---

### 2. Explicit / Implicit Feature Interaction 강화 모델

#### 설명
광고 CTR은 feature 간 조합 효과가 매우 중요하므로,  
단순 MLP보다 feature interaction을 더 정교하게 학습하려는 계열이다.  
FM, CIN, self-attention, cross network 등이 여기에 포함된다.

#### 대표 사례
- xDeepFM
- AutoInt
- DCN-V2

#### 핵심 특징
- 고차 feature interaction을 자동 학습
- 수작업 cross feature engineering 부담을 줄여줌
- 광고주, placement, creative, user context 간 복합 효과를 반영하기 좋음
- sequence behavior 자체를 직접 반영하는 모델은 아님

#### 언제 적합한가
- feature 조합 효과가 매우 중요할 때
- 카테고리/지면/디바이스/시간/광고주 속성이 복잡하게 얽혀 있을 때
- current LR/MLP baseline보다 더 강한 tabular CTR model이 필요할 때

---

### 3. User Interest / Behavior Sequence 기반 모델

#### 설명
사용자의 과거 행동 sequence를 바탕으로,  
현재 노출되는 광고와 관련 있는 관심사를 동적으로 반영하는 방식이다.  
단순 profile보다 실제 behavior history가 중요한 서비스에서 특히 효과적이다.

#### 대표 사례
- DIN
- DIEN

#### 핵심 특징
- candidate ad/item별로 다른 관심사를 반영 가능
- 최근 행동, 관심 변화, sequence dependency를 활용 가능
- 커머스/콘텐츠형 광고에서 강력할 가능성이 큼
- 학습과 서빙 비용이 더 높아질 수 있음

#### 언제 적합한가
- 사용자의 클릭/조회/구매 이력이 풍부할 때
- 광고가 상품/콘텐츠와 직접 연결되어 있을 때
- 단순 static feature보다 recent intent가 훨씬 중요할 때

---

### 4. Industrial-Scale Feature Cross / Ranking 모델

#### 설명
단순 정확도뿐 아니라,  
web-scale traffic, latency, memory, serving efficiency까지 함께 고려하는 production형 CTR/ranking 모델이다.

#### 대표 사례
- DCN-V2
- Google production ranking lessons

#### 핵심 특징
- 정확도와 serving practicality의 균형이 좋음
- 대규모 sparse feature 환경에서 강함
- online business metric을 함께 고려한 설계가 많음
- pure research novelty보다는 실전성이 강함

#### 언제 적합한가
- 이미 CTR model을 운영 중이고 고도화가 필요할 때
- latency와 throughput 제약이 강할 때
- benchmark 성능뿐 아니라 실제 배포 가능성이 중요할 때

---

### 5. Reproducible Benchmark / Evaluation 중심 접근

#### 설명
새로운 모델 구조 자체보다,  
동일한 데이터·동일한 튜닝 조건에서 재현 가능한 비교와 평가 프로토콜을 강조하는 접근이다.

#### 대표 사례
- BARS-CTR / FuxiCTR

#### 핵심 특징
- “최신 논문 = 항상 더 좋다”는 가정을 경계하게 해줌
- feature engineering, tuning, data split, negative sampling, calibration이 더 중요할 수 있음을 보여줌
- strong baseline 설계에 매우 중요함

#### 언제 적합한가
- 모델 선정 전에 객관적인 비교 체계를 만들고 싶을 때
- 사내 벤치마크 환경을 정비해야 할 때
- 실험 결과의 재현성과 설득력이 중요할 때

---

### 6. Generative / Cold-start 대응 최신 CTR 모델

#### 설명
최근에는 CTR prediction이 단순 binary discriminative prediction을 넘어서,  
생성형 관점이나 cold-start 대응 관점으로 확장되고 있다.  
특히 신규 광고, 신규 상품, sparse click 상황에서 표현력을 높이려는 방향이 중요해지고 있다.

#### 대표 사례
- Generative CTR Prediction (2025)
- IDProxy (2026)

#### 핵심 특징
- sparse label, uncertainty, cold-start 문제에 대응 가능성
- creative text/image/profile signal 활용 확장 여지
- 아직은 실무 표준보다 연구/고도화 영역의 성격이 강함

#### 언제 적합한가
- 신규 광고 소재가 자주 들어올 때
- item/ad cold-start가 큰 문제일 때
- 장기적으로 multimodal CTR prediction으로 확장할 계획이 있을 때

## 기술 카테고리별 대표 사례 매핑

| 카테고리 | 대표 사례 | 핵심 키워드 |
|---|---|---|
| Wide & Deep Hybrid | Wide & Deep, DeepFM | memorization, generalization, FM, DNN |
| Feature Interaction 강화 | xDeepFM, AutoInt, DCN-V2 | explicit interaction, self-attention, cross network |
| User Interest / Sequence | DIN, DIEN | target-aware interest, behavior sequence, interest evolution |
| Industrial-scale Ranking | DCN-V2, Google ranking practice | web-scale, latency, deployment, online gains |
| Benchmark / Evaluation | BARS-CTR, FuxiCTR, 2025 survey | reproducibility, strong baseline, tuning, protocol |
| Generative / Cold-start | Generative CTR, IDProxy | uncertainty, cold-start, new ads, generative modeling |

## CTR Prediction 모델 개발 시 목표 설정에 대한 의견

CTR prediction 모델 목표는 단순히 **“광고 효율 CTR 벤치마크 대비 120% 이상”**으로만 두기보다는,  
**오프라인 모델 목표**와 **온라인 비즈니스 목표**를 분리해서 잡는 것이 좋다.

### 문헌 기반 원칙

1. CTR prediction은 보통 **AUC, LogLoss, calibration, online CTR uplift**를 함께 본다. 2025 survey는 CTR prediction의 핵심 목적이 사용자·광고 정보를 기반으로 클릭 확률을 추정하는 것이라고 정리하고 있으며, 이는 단순 분류 정확도보다 광고 수익 및 랭킹 품질과 직접 연결된다.
2. 최신 benchmark 연구는 충분한 튜닝 후에는 모델 간 격차가 생각보다 작을 수 있음을 보여준다. 따라서 “최신 모델 도입” 자체보다 강한 baseline과 공정한 평가 체계가 더 중요할 수 있다.
3. 논문에 reported된 online uplift는 baseline 강도와 데이터 환경에 따라 크게 다르다. DeepFM은 LR 대비 10%+ CTR 개선, DIEN은 Taobao 환경에서 20.7% CTR 개선을 보고했지만, 이를 모든 서비스의 일반 목표로 쓰기는 어렵다.

### 실무 제안 수치

#### 1. 오프라인 모델 목표
- **Primary metric**: LogLoss 개선, AUC/GAUC 개선
- **Secondary metric**: calibration 안정화, segment별 성능 유지
- **권장 해석**: strong baseline 대비 아주 작은 offline 개선도 online에서 의미가 있을 수 있으므로, 단순 평균 uplift보다 재현성과 segment robustness를 더 중시하는 것이 좋다.

#### 2. 온라인 KPI 목표
- **MVP 목표**: benchmark 대비 **CTR 103~108%**
- **1차 상용화 목표**: benchmark 대비 **CTR 108~115%**
- **Stretch goal**: benchmark 대비 **CTR 115~120%+**

#### 3. 목표 해석 가이드
- 현재 benchmark가 단순 LR / shallow model이라면 **120%**는 충분히 가능한 도전 목표가 될 수 있다.
- 현재 benchmark가 이미 DeepFM, DCN-V2, sequence feature, calibration까지 반영한 strong baseline이라면 **120%는 꽤 공격적인 목표**다.
- 따라서 일반적인 실무 문서에서는 **105~115%를 기본 목표**, **120%+를 stretch goal**로 두는 편이 더 설득력 있다. 이 제안은 DeepFM의 10%+ production uplift, DIEN의 20.7% uplift, 그리고 benchmark 연구의 “모델 간 차이가 생각보다 작을 수 있다”는 결과를 함께 고려한 실무 해석이다.

#### 4. Guardrail metric 권장
CTR만 올리면 품질이 왜곡될 수 있으므로, 다음 지표를 함께 보는 것이 좋다.
- CVR 또는 post-click quality
- CPC / CPA
- Revenue per mille 또는 광고 매출
- Calibration ratio / predicted CTR stability
- 특정 inventory / advertiser / user segment에서의 편향 여부

## 한 줄 정리

현재 CTR prediction 기술은 아래 흐름으로 발전하고 있다고 볼 수 있다.

1. Wide & Deep hybrid 기반  
2. Feature interaction 강화 기반  
3. User behavior / interest sequence 기반  
4. Industrial-scale ranking 최적화 기반  
5. Benchmark / reproducibility 중심 접근  
6. Generative / cold-start 대응 기반  

즉, 과거에는 **sparse tabular feature에서 좋은 확률 예측기 만들기**가 중심이었다면,  
최근에는 **복잡한 interaction 학습**, **사용자 관심사의 시간적 변화 반영**,  
그리고 **cold-start / uncertainty / production serving 문제까지 함께 최적화**하는 방향으로 진화하고 있다.

## 참고 링크

- Wide & Deep Learning for Recommender Systems  
- DeepFM: An End-to-End Wide & Deep Learning Framework for CTR Prediction  
- xDeepFM: Combining Explicit and Implicit Feature Interactions for Recommender Systems  
- AutoInt: Automatic Feature Interaction Learning via Self-Attentive Neural Networks  
- Deep Interest Evolution Network for Click-Through Rate Prediction  
- DCN V2: Improved Deep & Cross Network and Practical Lessons for Web-scale Learning to Rank Systems  
- Open Benchmarking for Click-Through Rate Prediction (BARS-CTR / FuxiCTR)  
- A Comprehensive Survey on Advertising Click-Through Rate Prediction Algorithm  
- Generative Click-through Rate Prediction with Applications in Sponsored Search Auctions  
- IDProxy: Cold-Start CTR Prediction for Ads and Recommendation
