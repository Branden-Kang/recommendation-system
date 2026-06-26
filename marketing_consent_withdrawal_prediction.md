# 마케팅 동의 철회 예측 모델링 리서치 및 산업 사례 정리

## 1. 개요

마케팅 동의 철회 예측 모델링은 현재 이메일, SMS, 앱 푸시, 카카오 알림, 광고성 정보 수신 등에 동의한 고객이 향후 특정 기간 안에 수신거부 또는 마케팅 동의 철회를 할 가능성을 예측하는 분석 방법이다.

실무에서는 다음과 같은 이름으로도 사용된다.

- Opt-out Propensity Model
- Unsubscribe Propensity Model
- Marketing Fatigue Model
- Channel Saturation Model
- Engagement Risk Model
- Consent Withdrawal Prediction Model

이 모델의 목적은 고객의 철회를 방해하거나 철회 절차를 어렵게 만드는 것이 아니다. 핵심 목적은 과도한 접촉, 낮은 메시지 관련성, 채널 피로도 등을 조기에 감지해 고객 경험을 보호하고, 마케팅 접촉 전략을 더 정교하게 조정하는 것이다.

---

## 2. 핵심 개념

### 2.1 예측 대상

마케팅 동의 철회 예측 모델은 보통 다음과 같은 이벤트를 예측한다.

| 예측 대상 | 예시 |
|---|---|
| 이메일 수신거부 | 향후 30일 내 이메일 unsubscribe 여부 |
| SMS 수신거부 | 향후 14일 내 광고성 문자 수신거부 여부 |
| 앱 푸시 차단 | 향후 30일 내 앱 알림 권한 off 또는 push opt-out 여부 |
| 카카오/알림톡 수신거부 | 광고성 메시지 수신 차단 여부 |
| 전체 마케팅 동의 철회 | 모든 마케팅 채널에 대한 동의 철회 여부 |
| 관심 주제 구독 해지 | 특정 카테고리, 브랜드, 콘텐츠 구독 해지 여부 |

### 2.2 문제 유형

| 모델링 유형 | 설명 | 활용 상황 |
|---|---|---|
| 이진 분류 모델 | 특정 기간 내 철회 여부를 0/1로 예측 | PoC, 캠페인 운영 자동화 |
| 생존분석 | 철회까지 걸리는 시간을 예측 | 동의 이후 생애주기 분석 |
| 시계열 모델 | 고객의 접촉·반응 흐름을 순차적으로 분석 | 고빈도 캠페인, 앱/커머스 |
| Uplift 모델 | 특정 마케팅 접촉이 철회 위험을 증가시키는지 추정 | 발송 여부 최적화 |
| Multi-task 모델 | 구매 가능성, 이탈 가능성, 철회 가능성을 함께 예측 | CRM 통합 스코어링 |

---

## 3. 왜 중요한가

고객이 마케팅 동의를 철회하는 주된 이유는 대체로 다음과 같다.

1. 너무 자주 연락을 받음
2. 관심 없는 콘텐츠를 받음
3. 구매 의도가 낮은 시점에 반복적으로 노출됨
4. 혜택보다 방해감이 큼
5. 채널별 피로도가 누적됨
6. 개인정보 활용에 대한 불신이 생김

따라서 동의 철회 예측 모델은 단순히 “누가 철회할 것인가”를 맞히는 모델이 아니라, 다음 의사결정에 활용되어야 한다.

- 누구에게 덜 보낼 것인가
- 어떤 고객에게 선호도 설정을 안내할 것인가
- 어떤 고객에게 다른 채널을 사용할 것인가
- 어떤 캠페인을 제외할 것인가
- 어떤 고객에게는 세일즈 메시지보다 관계 회복 메시지를 보낼 것인가
- 전체 발송량을 줄이면서 전환 효율을 유지할 수 있는가

---

## 4. 주요 논문 및 리서치 정리

이 섹션은 예측 모델링 관점에서 직접적으로 참고할 수 있는 논문과 리서치 중심으로 재정리했다. 기존에 포함되어 있던 이메일 마케팅 해설, 개인정보 규제 문서, 동의 배너 UX 연구는 완전히 무관하지는 않지만, 예측 모델링 방법론 문헌으로 보기는 어렵다. 따라서 4장에서는 제외하고, 규제·윤리 자료는 12장으로 이동하는 것이 더 적절하다.

| 구분 | 제목 / 주제 | 저자 / 기관 | 핵심 내용 | 실무 시사점 | 링크 |
|---|---|---|---|---|---|
| 직접 관련 / opt-out 모델링 | Modeling Customer Opt-In and Opt-Out in a Permission-Based Marketing Context | Kumar, Zhang, Luo / Journal of Marketing Research | 고객의 opt-in 시점, opt-out 시점, 거래금액을 함께 모델링. 마케팅 강도와 고객 특성이 동의 유지 및 철회 시점에 영향을 준다는 점을 분석 | 마케팅 동의 철회는 단순 unsubscribe 이벤트가 아니라 고객 생애주기, 구매 행동, 접촉 강도와 함께 봐야 함 | https://journals.sagepub.com/doi/10.1509/jmr.13.0169 |
| churn prediction / 분류 모델 | Bagging and Boosting Classification Trees to Predict Churn | Lemmens, Croux / Journal of Marketing Research | 통신 고객 데이터를 대상으로 bagging, boosting 기반 분류 모델이 churn 예측 성능을 높이는지 분석 | 동의 철회 예측에서도 단순 로지스틱 회귀 외에 tree ensemble 계열 모델을 baseline으로 검토할 수 있음 | https://doi.org/10.1509/jmkr.43.2.276 |
| churn prediction / 설명가능성 | Building Comprehensible Customer Churn Prediction Models with Advanced Rule Induction Techniques | Verbeke, Martens, Mues, Baesens / Expert Systems with Applications | churn 예측에서 정확도뿐 아니라 모델 해석 가능성, 비즈니스 설명 가능성, 규칙 기반 접근의 중요성을 논의 | 캠페인 담당자가 “왜 이 고객의 철회 위험이 높은가”를 이해해야 하므로 설명 가능한 규칙, SHAP, feature importance가 중요 | https://doi.org/10.1016/j.eswa.2010.08.023 |
| churn prediction / 대용량 데이터 | Customer Churn Prediction in Telecom Using Machine Learning in Big Data Platform | Ahmad, Jafar, Aljoumaa / Journal of Big Data | 통신사 대규모 데이터와 머신러닝, feature engineering, social network analysis를 활용해 churn 예측 모델을 구축 | 마케팅 접촉 로그, 고객 관계 데이터, 반응 로그를 결합하면 opt-out risk 예측 성능을 높일 수 있음 | https://link.springer.com/article/10.1186/s40537-019-0191-6 |
| churn prediction / rule-based 모델 | Customer Churn Prediction in the Telecommunication Sector Using a Rough Set Approach | Amin et al. / Neurocomputing | Rough Set Theory 기반의 rule-based churn 예측 접근을 제안하고 churn·non-churn 의사결정 규칙을 추출 | 설명 가능한 세그먼트 규칙을 만들어 “어떤 조건에서 철회 위험이 높은가”를 정책화하는 데 참고 가능 | https://doi.org/10.1016/j.neucom.2016.12.009 |
| churn prediction / hybrid 모델 | A New Hybrid Classification Algorithm for Customer Churn Prediction Based on Logistic Regression and Decision Trees | De Caigny, Coussement, De Bock / European Journal of Operational Research | Decision Tree로 고객을 세분화한 뒤 각 leaf에서 Logistic Regression을 적용하는 Logit Leaf Model 제안 | 고객군별로 철회 원인이 다를 수 있으므로 세그먼트별 모델 또는 hierarchical rule + score 구조를 검토할 수 있음 | https://doi.org/10.1016/j.ejor.2018.02.009 |
| churn prediction / uplift 모델 | Uplift Modeling and Its Implications for B2B Customer Churn Prediction: A Segmentation-Based Modeling Approach | De Caigny, Coussement, Verbeke, Idbenjra, Phan / Industrial Marketing Management | 단순 churn 가능성 예측이 아니라 retention action의 개별 효과를 추정하는 uplift modeling을 churn 문제에 적용 | 마케팅 동의 철회 예측에서는 “원래 철회할 고객”과 “발송 때문에 철회 위험이 증가하는 고객”을 구분하는 데 중요 | https://doi.org/10.1016/j.indmarman.2021.10.001 |
| churn prediction / uplift benchmark | A Churn Prediction Dataset from the Telecom Sector: A New Benchmark for Uplift Modeling | Verhelst, Mercier, Shrestha, Bontempi / arXiv | 통신사 churn 문제에서 uplift modeling을 평가할 수 있는 공개 benchmark dataset을 제시 | 발송 축소, 빈도 조정, preference center 안내 등 개입 정책의 효과 검증 설계에 참고 가능 | https://arxiv.org/abs/2312.07206 |
| churn prediction / 생존분석 | Modelling Customer Churn for the Retail Industry in a Deep Learning Based Sequential Framework | Equihua, Nordmark, Ali, Lausen / arXiv | 비계약형 retail 환경에서 고객 구매 시퀀스를 활용한 deep survival framework로 churn risk를 예측 | 동의 철회까지의 시간, 장기 미반응 이후 철회 가능성처럼 time-to-event 문제로 확장할 때 참고 가능 | https://arxiv.org/abs/2304.00575 |
| 리뷰 / 최신 동향 | Customer Churn Prediction: A Systematic Review of Recent Advances, Trends, and Challenges in Machine Learning and Deep Learning | Imani et al. / AI | 2020~2024년 churn prediction 연구를 대상으로 ML/DL 접근, 활용 산업, 성능 평가, 과제와 한계를 정리 | PoC 설계 시 최신 모델링 접근, 평가 지표, class imbalance, 설명가능성, 운영 적용 이슈를 검토하는 데 유용 | https://www.mdpi.com/2504-4990/7/3/105 |

### 4.1 기존 항목 관련성 판단

| 기존 항목 유형 | 판단 | 조정 방향 |
|---|---|---|
| 이메일 마케팅 소비자 조사 / 해설 | 이메일 수신거부 원인과 실무 운영에는 관련이 있지만, 예측 모델링 논문으로 보기에는 약함 | 4장에서는 제외하고, 필요하면 산업 사례 또는 실무 배경 자료로 분리 |
| 개인정보 규제 문서 | 마케팅 동의 철회 예측 모델의 사용 한계와 준수 조건에는 매우 중요하지만, churn/opt-out 예측 방법론 문헌은 아님 | 12장 규제 및 윤리적 주의사항으로 이동 |
| 동의 배너 / dark pattern UX 연구 | 동의·철회 UX 설계 윤리에는 관련이 있지만, 예측 모델 구축 방법론과는 거리가 있음 | 12장 윤리 참고 문헌으로 이동 |
| Propensity modeling 일반 해설 | 예측 모델 운영 관점에서는 참고 가능하지만, 학술 논문·실증 연구보다 근거 수준이 낮음 | 4장 핵심 문헌에서는 제외하고, PoC 설명 자료나 실무 참고 자료로만 활용 |

---

## 5. 산업 사례 정리

| 기업 / 솔루션 | 관련 기능 / 사례 | 핵심 내용 | 마케팅 동의 철회 예측과의 관련성 | 링크 |
|---|---|---|---|---|
| Salesforce Marketing Cloud | Einstein Engagement Frequency | 개별 contact별 최적 발송 빈도를 찾아 engagement를 높이고 unsubscribe를 줄이는 기능 | 동의 철회 예측을 직접 명시하지 않아도, 발송 빈도 최적화를 통해 수신거부 위험을 관리 | https://help.salesforce.com/s/articleView?id=mktg.mc_anb_eef.htm&type=5 |
| Adobe Journey Optimizer | Consent & Opt-out Management | 이메일, SMS, push 등 채널별 opt-out 및 consent policy를 관리. opt-out profile은 향후 마케팅 audience에서 제외 | 예측 모델 적용 전 consent 상태를 정확히 저장하고 집행하는 기반 시스템이 필요함 | https://experienceleague.adobe.com/en/docs/journey-optimizer/using/privacy/consent/opt-out |
| Braze | Predictive Churn | 사용자 행동 기반 머신러닝으로 이탈 가능성이 높은 고객을 예측하고 journey에 활용 | 동의 철회는 고객 이탈의 전조 또는 부분 이탈로 볼 수 있어 churn risk 모델과 결합 가능 | https://www.braze.com/resources/articles/churn-prediction |
| Batch | AI Predict / Propensity Score Use Case | 관심도 propensity score를 활용해 전체 발송 대신 고관심 고객을 타깃팅. 수신거부율 감소 및 클릭률 개선 사례 공개 | 철회 위험이 높은 고객에게 무차별 발송하지 않고, 관심 가능성이 높은 고객 중심으로 발송량을 최적화 | https://batch.com/blog/posts/marketing-pressure-halve-unsubscribes-propensity-score |
| Acquia | Predictive Analytics Models for Marketing | propensity to unsubscribe, propensity to purchase 등 다양한 마케팅 예측 모델 설명 | CRM/CDP 환경에서 수신거부 위험 점수를 캠페인 조건으로 활용 가능 | https://www.acquia.com/blog/definitive-guide-predictive-analytics-models-marketing |
| FulcrumTech | Email Marketing Predictive Models | 이메일 마케팅에서 unsubscribe propensity 모델을 소개 | 이메일 발송 빈도 조정, 캠페인 제외, 선호도 업데이트 유도에 활용 가능 | https://fulcrumtech.net/resources/3-predictive-models-email-marketers-need-to-know-about/ |
| Iterable / 일반 CRM 업계 | Frequency Management, Send Time Optimization, Channel Preference | 고객별 발송 시간, 빈도, 채널 선호도를 활용한 campaign orchestration | 동의 철회 예측 모델은 단독 모델보다 frequency cap, channel preference, engagement model과 함께 운영하는 것이 효과적 | 관련 제품 문서 및 CRM 업계 일반 사례 |
| CDP / Marketing Automation 일반 | Suppression List, Preference Center, Consent Ledger | 수신거부 고객 제외, 선호 채널 관리, 수신 주제 선택 기능 제공 | 예측 모델의 결과를 실제 발송 정책에 반영하기 위한 실행 레이어 | CDP, CRM, MA 제품군 공통 기능 |

---

## 6. 주요 데이터 및 피처

### 6.1 동의 이력 피처

| 피처 | 설명 |
|---|---|
| 최초 마케팅 동의일 | 고객이 처음 마케팅 수신에 동의한 일시 |
| 최근 동의 변경일 | 수신 채널, 주제, 목적 변경 시점 |
| 동의 채널 | 웹, 앱, 오프라인, 이벤트, 제휴 등 |
| 동의 문구 버전 | 고객이 동의한 약관 또는 동의 문구 버전 |
| 동의 후 경과일 | 동의 이후 며칠이 지났는지 |
| 과거 부분 철회 여부 | 이메일만 철회, SMS만 철회 등 |
| preference center 방문 여부 | 수신 설정 페이지 방문 여부 |

### 6.2 마케팅 압력 피처

| 피처 | 설명 |
|---|---|
| 최근 7일 발송 수 | 단기 마케팅 접촉 강도 |
| 최근 14일 발송 수 | 중기 접촉 강도 |
| 최근 30일 발송 수 | 누적 피로도 |
| 채널별 발송 수 | 이메일, SMS, push, 카카오 등 |
| 캠페인 간 평균 간격 | 접촉 간격이 너무 짧은지 확인 |
| 동일 주제 반복 노출 수 | 같은 카테고리/상품/혜택의 반복 여부 |
| 할인 메시지 비중 | 가격 프로모션 중심 메시지 비율 |
| 야간/휴일 발송 여부 | 불편한 시간대 발송 여부 |

### 6.3 고객 반응 피처

| 피처 | 설명 |
|---|---|
| 이메일 오픈율 | 최근 캠페인 오픈 비율 |
| 클릭률 | 메시지 내 링크 클릭 비율 |
| 전환율 | 클릭 후 구매 또는 목표 행동 수행 여부 |
| 미오픈 연속 횟수 | 연속으로 메시지를 열지 않은 횟수 |
| 마지막 클릭 후 경과일 | 최근 관심 시점 |
| spam complaint 여부 | 스팸 신고 이력 |
| bounce 여부 | 이메일 도달 실패 여부 |
| 앱 푸시 클릭률 | push 메시지 반응도 |
| SMS 링크 클릭률 | 문자 내 URL 클릭 여부 |

### 6.4 고객 가치 및 관계 피처

| 피처 | 설명 |
|---|---|
| 가입 후 경과일 | 고객 생애주기 단계 |
| 최근 구매일 | recency |
| 구매 빈도 | frequency |
| 객단가 | monetary value |
| 멤버십 등급 | 고객 가치 및 충성도 |
| 반품/환불 이력 | 부정 경험 가능성 |
| CS 문의 수 | 고객 불만 또는 문의 강도 |
| 앱 방문 빈도 | 최근 engagement 수준 |
| 웹 방문 빈도 | 관심도 및 탐색 행동 |

### 6.5 콘텐츠 적합도 피처

| 피처 | 설명 |
|---|---|
| 고객 관심 카테고리 | 과거 조회, 클릭, 구매 기반 관심사 |
| 캠페인 카테고리 유사도 | 고객 관심사와 메시지 주제의 적합도 |
| 선호 혜택 유형 | 할인, 포인트, 신상품, 콘텐츠 등 |
| 선호 채널 | 이메일, SMS, push, 앱 내 메시지 등 |
| 선호 시간대 | 고객이 자주 반응하는 시간대 |
| 가격 민감도 | 할인 메시지에 대한 반응 정도 |

---

## 7. 모델링 접근법

### 7.1 이진 분류 모델

가장 일반적인 접근이다.

예시 라벨:

```text
기준일 이후 30일 내 이메일 수신거부 발생 = 1
기준일 이후 30일 내 이메일 수신거부 미발생 = 0
```

활용 가능한 모델:

- Logistic Regression
- Random Forest
- XGBoost
- LightGBM
- CatBoost
- Neural Network

장점:

- 구현이 쉽다.
- 캠페인 시스템에 score를 적재하기 쉽다.
- feature importance나 SHAP으로 설명 가능하다.

단점:

- “언제 철회할 것인가”를 직접 설명하기 어렵다.
- 고객별 관측 기간 차이를 충분히 반영하기 어렵다.

### 7.2 생존분석

동의 이후 철회까지 걸리는 시간을 모델링한다.

활용 가능한 방법:

- Cox Proportional Hazards Model
- Random Survival Forest
- Gradient Boosted Survival Model
- DeepSurv

장점:

- 아직 철회하지 않은 고객의 censoring을 반영할 수 있다.
- 동의 이후 어느 시점에서 위험이 높아지는지 볼 수 있다.

단점:

- 마케팅 조직에서 이해하기 상대적으로 어렵다.
- 캠페인 자동화 시스템과의 연결이 이진 분류보다 복잡할 수 있다.

### 7.3 Uplift / Causal 모델

단순히 철회 위험이 높은 고객을 찾는 것이 아니라, 특정 캠페인을 발송했을 때 철회 위험이 증가하는 고객을 찾는다.

예시 질문:

이 고객에게 이번 할인 캠페인을 보내면 수신거부 확률이 실제로 증가하는가?

장점:

- 불필요한 발송을 줄이는 데 매우 유용하다.
- “원래 철회할 고객”과 “마케팅 접촉 때문에 철회하는 고객”을 구분할 수 있다.

단점:

- 실험군/대조군 설계가 필요하다.
- 데이터 품질과 실험 설계가 중요하다.

---

## 8. 평가 지표

마케팅 동의 철회는 보통 희소 이벤트이므로 accuracy만 보면 안 된다.

| 지표 | 설명 |
|---|---|
| PR-AUC | 철회 이벤트가 적을 때 유용한 분류 성능 지표 |
| ROC-AUC | 전체적인 순위화 성능 확인 |
| Recall@TopK | 위험 점수 상위 K% 안에 실제 철회자가 얼마나 포함되는지 |
| Lift@Decile | 상위 decile의 철회율이 평균 대비 얼마나 높은지 |
| Calibration | 예측 확률이 실제 확률과 얼마나 일치하는지 |
| Opt-out Rate | 실제 수신거부율 |
| Spam Complaint Rate | 스팸 신고율 |
| CTR / CVR | 클릭률 및 전환율 |
| Revenue Impact | 발송 축소로 인한 매출 영향 |
| Long-term LTV | 장기 고객 가치 변화 |

---

## 9. 실무 운영 세그먼트

동의 철회 예측 모델은 단독으로 쓰기보다 구매 가능성, 고객 가치, 채널 선호도와 결합하는 것이 좋다.

| 세그먼트 | 특성 | 권장 액션 |
|---|---|---|
| 철회 위험 높음 + 구매 가능성 낮음 | 피로도 높고 전환 가능성 낮음 | 발송 제외, 빈도 제한, 휴면 보호 |
| 철회 위험 높음 + 고객 가치 높음 | 중요한 고객이지만 피로도 높음 | 수신 주제/빈도 선택 안내, 관계 회복 메시지, 멤버십 케어 |
| 철회 위험 낮음 + 구매 가능성 높음 | 캠페인 반응 가능성 높음 | 개인화 캠페인 발송 |
| 철회 위험 낮음 + 반응 낮음 | 즉각 전환은 낮지만 거부감도 낮음 | 저빈도 nurture, 콘텐츠 테스트 |
| 특정 채널 위험 높음 + 다른 채널 선호 | 이메일 피로도는 높지만 앱 방문은 활발 | 적법한 동의가 있는 다른 채널 또는 앱 내 메시지 활용 |
| 전체 동의 철회 가능성 높음 | 전 채널 접촉에 피로도 누적 | 일정 기간 suppression, preference center 안내 |

---

## 10. 권장 아키텍처

```text
[Consent Data]
- 채널별 동의 상태
- 동의 일시
- 철회 일시
- 동의 문구 버전
- 수신 주제

        ↓

[Marketing Interaction Data]
- 캠페인 발송 로그
- 채널별 노출 횟수
- 메시지 주제
- 발송 시간
- 반응 로그

        ↓

[Customer Data]
- 구매 이력
- 방문 이력
- 멤버십 정보
- CS/불만 이력
- 고객 가치 지표

        ↓

[Feature Store]
- 동의 이력 피처
- 마케팅 압력 피처
- 반응 피처
- 고객 가치 피처
- 콘텐츠 적합도 피처

        ↓

[Prediction Model]
- Opt-out Risk Score
- Channel Fatigue Score
- Frequency Sensitivity Score

        ↓

[Activation Layer]
- CRM/CDP
- Campaign Management System
- Journey Builder
- Marketing Automation
- Preference Center

        ↓

[Action]
- 발송 제외
- 빈도 제한
- 채널 변경
- 선호도 설정 유도
- 콘텐츠 개인화
```

---

## 11. PoC 추진 로드맵

### 1단계: 데이터 정의

먼저 다음 데이터를 통합한다.

- 고객 ID
- 채널별 마케팅 동의 상태
- 동의 일시 및 철회 일시
- 캠페인 발송 이력
- 캠페인 반응 이력
- 구매 및 방문 이력
- 고객 등급 및 가치 정보
- CS/불만/환불 이력

### 2단계: 라벨 정의

예시:

- 기준일 이후 30일 내 이메일 수신거부 여부
- 기준일 이후 14일 내 SMS 수신거부 여부
- 기준일 이후 30일 내 앱 푸시 opt-out 여부
- 캠페인 발송 후 7일 내 전체 마케팅 동의 철회 여부

### 3단계: 베이스라인 모델 구축

초기에는 복잡한 모델보다 다음 조합을 권장한다.

- Logistic Regression
- LightGBM
- XGBoost
- SHAP 기반 설명

초기 목표는 완벽한 예측이 아니라, 상위 위험군을 안정적으로 구분하는 것이다.

### 4단계: 캠페인 정책 실험

예시 실험:

| 그룹 | 처리 |
|---|---|
| Control | 기존 발송 정책 유지 |
| Treatment A | 고위험군 발송 제외 |
| Treatment B | 고위험군 발송 빈도 50% 축소 |
| Treatment C | 고위험군 preference center 안내 |
| Treatment D | 고위험군에게 세일즈 메시지 대신 관계형 메시지 발송 |

측정 지표:

- 수신거부율
- 클릭률
- 전환율
- 매출
- spam complaint
- 장기 재방문율
- 고객 생애가치

### 5단계: 운영 자동화

모델 score를 CRM/CDP에 적재하고 캠페인 조건으로 활용한다.

예시:

```text
if opt_out_risk_score >= 0.8 and purchase_propensity < 0.3:
    campaign_suppression = True

if email_fatigue_score >= 0.7:
    reduce_email_frequency = True

if opt_out_risk_score >= 0.8 and customer_value_high = True:
    send_preference_center_message = True
```

---

## 12. 규제 및 윤리적 주의사항

마케팅 동의 철회 예측 모델은 반드시 고객의 선택권을 보호하는 방향으로 사용해야 한다.

예측 모델은 고객의 동의 철회권을 제한하거나 지연시키는 데 사용하면 안 된다. 규제 문서는 예측 방법론의 핵심 근거라기보다는, 모델을 어떤 방식으로 사용하면 안 되는지 정하는 운영 가드레일로 보는 것이 적절하다.

### 12.1 참고 규제 및 윤리 문헌

| 구분 | 제목 / 주제 | 핵심 내용 | 실무 시사점 | 링크 |
|---|---|---|---|---|
| 개인정보 규제 | GDPR Article 7 - Conditions for Consent | 동의 철회는 동의만큼 쉬워야 하며, 철회 이후에는 해당 동의에 근거한 처리를 지속할 수 없음을 명시 | 예측 모델을 철회 방해, 버튼 숨김, 절차 복잡화에 활용하면 안 됨 | https://gdpr-info.eu/art-7-gdpr/ |
| 국내 규제 | 개인정보 보호법 제37조 | 정보주체는 개인정보 처리정지를 요구하거나 개인정보 처리에 대한 동의를 철회할 수 있음 | 국내에서도 철회권 보장과 처리정지 요청 대응이 필수 | https://www.law.go.kr/ |
| 국내 규제 안내 | 개인정보 처리정지 및 동의철회 관련 설명 | 개인정보 처리정지 및 동의철회 방법과 절차는 개인정보 수집 방법보다 쉽게 해야 한다는 설명 제공 | 철회 절차는 간단하고 명확해야 하며, 모델로 회피해서는 안 됨 | https://easylaw.go.kr/ |
| 국내 광고성 정보 안내 | 불법스팸 방지를 위한 정보통신망법 안내서 | 광고성 정보 수신동의, 수신거부, 앱 푸시 광고 수신거부 절차 등에 대한 기준 설명 | 마케팅 동의 관리, 수신거부 반영, 수신동의 확인 절차를 시스템화해야 함 | https://www.kisa.or.kr/402/form?postSeq=2577 |
| 개인정보 / UX 연구 | Dark Patterns after the GDPR: Scraping Consent Pop-ups and Demonstrating Their Influence | 동의 배너의 프레이밍, 기본값, 선택 구조가 사용자의 동의 행동에 영향을 줄 수 있음을 분석 | 동의·철회 UX는 고객 자율성을 해치지 않도록 투명하게 설계해야 함 | https://arxiv.org/abs/1909.02638 |

금지해야 할 활용

- 철회 가능성이 높은 고객에게 수신거부 버튼을 덜 노출
- 수신거부 절차를 복잡하게 변경
- 철회 화면에서 불필요한 단계 추가
- 철회 고객을 다른 채널로 무단 이전
- 동의하지 않은 채널로 대체 발송
- 철회 예측 고객에게 과도한 리텐션 메시지 발송
- dark pattern을 활용해 철회를 지연

권장 활용

- 발송 빈도 축소
- 관심 없는 캠페인 제외
- 선호도 설정 안내
- 수신 주제 세분화
- 고객이 선호하는 채널 중심 운영
- 장기 미반응 고객 보호
- 고피로 고객에 대한 suppression 정책 적용

---

## 13. 실무 체크리스트

| 항목 | 확인 사항 |
|---|---|
| Consent Ledger | 채널별 동의/철회 상태가 정확히 저장되는가 |
| 철회 반영 SLA | 수신거부 후 발송 제외가 즉시 또는 법적 기준 내 반영되는가 |
| 데이터 연결 | 캠페인 발송, 반응, 구매, CS 데이터가 고객 단위로 연결되는가 |
| 라벨 품질 | 철회 이벤트가 정확히 기록되는가 |
| 희소 이벤트 대응 | class imbalance를 고려했는가 |
| 설명 가능성 | 왜 특정 고객의 철회 위험이 높은지 설명 가능한가 |
| 실험 설계 | 발송 축소가 실제로 철회율을 낮추는지 검증했는가 |
| 매출 영향 | opt-out 감소뿐 아니라 매출 손실도 함께 측정하는가 |
| 규제 준수 | 철회 절차가 동의 절차보다 어렵지 않은가 |
| 고객 신뢰 | 모델이 고객 선택권을 제한하지 않는가 |

---

## 14. 핵심 결론

마케팅 동의 철회 예측 모델은 고객의 철회를 막기 위한 모델이 아니라, 고객이 철회하고 싶어질 정도로 피로해지기 전에 접촉 강도와 메시지 관련성을 조정하기 위한 모델이다.

성공적인 적용을 위해서는 다음 세 가지가 중요하다.

1. 정확한 consent 데이터 관리
2. 마케팅 압력과 고객 반응을 반영한 예측 모델
3. 예측 결과를 고객 친화적 발송 정책으로 전환하는 운영 체계

가장 효과적인 방향은 단순히 “철회 위험 고객을 찾는 것”이 아니라, 구매 가능성, 고객 가치, 채널 선호도, 마케팅 피로도를 함께 고려해 고객별 접촉 전략을 조정하는 것이다.

즉, 이 모델은 CRM 성과 모델이면서 동시에 고객 신뢰 관리 모델이다.
