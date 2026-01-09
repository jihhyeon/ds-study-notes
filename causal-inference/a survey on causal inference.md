## **논문이 말하고자 하는 바**

> **관측 데이터**에서 인과효과를 추정하기 위한 많은 수많은 방법들이 흩어져 있는 상황에서
> **잠재적 결과 프레임워크(Potential Outcome Framework)** 관점에서
> 이 방법들을 체계적으로 정리, 분류, 이해 가능하게 만드는 것

---

## **잠재적 결과 프레임워크란?**
- 인과적 질문의 형태
    - "이 사람이 다른 선택을 했다면 결과는 어떻게 달라졌을까?"
- 현실의 문제
    - 한 사람은 한 번에 하나의 선택만 할 수 있음
    - 다른 선택을 했을 때의 결과는 영원히 관측되지 않는 반사실(counterfactual)임
- 따라서,
    - "보이지 않은 결과(반사실)"을 명시적으로 정의하고, 이를 어떻게 추정할지를 체계적으로 다루는 프레임워크

---

## **논문 요약**
### 1. Introduction
- 상관관계 vs 인과관계
    - 상관관계:두 변수가 함께 움직이는 경향
    - 인과관계:한 변수가 다른 변수의 원인
- 상관관계는 인과관계가 아님
- 이 논문은 **"관측 데이터에서 진짜 인과효과를 어떻게 추정할 것인가?"**라는 질문 다룸

- 관측 데이터가 중요한 이유?
    - Randomized controlled trial (RCT)
        - 장점:인과추론의 골드 스탠다드
        - 단점:비싸고 오래걸림 / 윤리적 문제 / 현실 데이터 규모 반영 어려움
    - Observational data
        - 처치 없이 관찰된 데이터 (ex.의료기록, 광고 클릭 로그..)
- 문제
    - treatment가 무작위가 아님
    - confounder(교란변수) 때문에 편향 발생
- 주요 프레임워크
    - potential outcome framework
    - Structural Causal Model(SCM)
    - &rarr; scm이나 인과구조 학습이 아닌 **잠재적 결과 프레임워크 하의 인과효과 추정**

### 2. Basic of Causal Inference
인과추론의 문법을 정의함
- 핵심 용어 정의
    1. unit (단위)
        - 연구 대상의 최소 단위
        - 인과효과는 단위에 처치(treatment)했을 때 결과가 어떻게 바뀌는가를 비교
    2. treatment (처치, W)
        - 처치, 행동, 정책
        - 보통 이진 : W=1(처치), W=0(비처치)
        - "원인"에 해당하는 변수
    3. potential outcome (잠재 결과)
        - 같은 개인이 다른 처치을 받았을 때의 가상 결과
        - e.g.
            - Y(1):약을 먹었을 때 결과
            - Y(0):약을 안먹었을 때 결과
        - 핵심 문제 = 한 사람에게서는 Y(1)과 Y(0)을 동시에 볼 수 없음
    4. observed outcome (관측 결과)
        - 실제로 관측된 결과
        - Yf = Y(W)
    5. counterfactual outcome (반사실 결과)
        - 실제로는 일어나지 않았지만 "만약~였다면"의 결과
        - 인과추론의 목표 : 관측되지 않은 반사실을 추정하는 것
    6. pre-treatment variables (사전변수, X)
        - 처치 이전에 이미 결정된 변수
        - e.g.나이, 성별, 과거병력
        - confounder 후보
    7. post-treatment variables
        - 처치 이후에 영향을 받는 변수
        - e.g.치료 후 검사 수치
        - 잘못 사용하면 인과 왜곡
- Treatment Effect의 종류
    - treatment effect의 종류를 나누는 이유
        - 인과효과의 정의: Y(1)-Y(0)
        - 이 값을 **누구 기준으로, 어떻게 평균내느냐**에 따라 종류가 달라짐
    - ITE
        - 개인 i 한명의 인과효과
        - e.g. 이 사람 한명에게 처치했을 때 결과가 얼마나 달라지는가?
        - 현실 문제
            - 한 사람에게 두가지 처치를 동시에 할 수 없으니 반사실이 누락됨
            - 직접 관측 가능한 값은 아님
    - ATE
        - 전체 집단 평균 인과효과
        - e.g. 이 처치을 모든 사람에게 적용했을 때 평균적으로 얼마나 효과가 있는가?
    - ATT
        - 실제로 처치을 받은 사람들만의 평균 효과
        - e.g. 이미 이 처치를 선택한 사람들에게 이 선택은 실제로 도움이 되었는가?
    - CATE
        - 특정 조건 X=x를 만족하는 집단의 평균 효과
        - e.g. 이런 특성을 가진 사람들에게는 처치효과가 얼마나 되는가?
        - 중요한 이유
            - 현실에서는 효과가 사람마다 다름
            - Heterogeneous Treatement Effect라 부름 -> 이질적 효과를 집단단위로 포착
- 세가지 핵심 가정
    1. SUTVA
        - 다른사람의 처치가 내 결과에 영향을 주지 않고, 같은 처치는 항상 동일한 의미를 갖는다는 가정
        - SUTVA는 두가지 가정의 묶음
            - 개인의 결과는 다른 개인이 어떤 처치를 받았는지와 무관
            - 처치는 항상 같은 내용이어야함
    2. Ignorability (Unconfoundedness)
        - 관측한 변수 x만 알고있으면, 누가 처치를 받았는지는 사실상 무작위와 같음
        - 즉, 숨겨진 confounder가 없음, 모든 중요한 confounder을 다 측정함
    3. positivity
        - 모든 특성 X=x에 대해 모든 처치가 발생할 확률이 0이 아님
        - 즉, 어떤 집단도 "항상 처치를 받거나, 절대 받지않는" 경우가 없어야함
- confounder와 문제점
    - confounder
        - 처치와 결과에 모두 영향을 미치는 변수
    - 문제
        - spurious effect
        - simpson's paradox
        - selection bias
### 3. Methods Relying on Three Assumptions
위의 3가지 가정을 모두 믿을 수 있다면, 관측 데이터만으로도 인과효과를 추정할 수 있다.
그때 사용할 수 있는 방법들은 무엇인가?

- Re-weighting Methods (재가중치 방법)
    - 처치군과 대조군의 분포를 인위적으로 같게 만듦
        - 그냥 평균 비교시, 편향 발생 (처치군과 대조군이 다르기 때문)
    - 방법
        - IPW, Doubly Robust
- Stratification (층화)
    - 비슷한 사람끼리 먼저 묶고, 그 안에서 처치효과를 비교
- Matching Methods (매칭)
    - 나와 가장 비슷한 다른 사람을 찾아 비교
    - 방법
        - Nearest Neighbor, Caliper, Kernel Matching, CEM
- Tree-based Methods
    - 의사결정트리를 이용해 처치효과가 이질적인 하위집단을 자동으로 발견
- Representation-based Methods
    - 공변량X를 균형잡힌 잠재공간으로 변환하여 처치편향을 제거
- Multi-task Learning Methods
    - 처치별 outcome을 동시에 학습하여 데이터 효율성과 일반화 향상
- Meta-learning Methods
    - 기본예측모델 + 인과적 보정 하여 개인 수준 처치효과 추정
    - T-learner, S-learner, X-learner
### 4. Relaxing Assumptions
잠재적 결과 프레임워크의 핵심 가정 중 postivity 또는 ignorability가 성립하지 않는 경우에
인과 추론을 수행하기 위한 방법
- Positivity 위반
    - overlap weight : 처치군과 대조군이 충분히 겹치는 영역에 가중치 집중 -> 효과 추정
- Unobserved confounder
    - 관찰되지 않은 confounder가 존재할 경우, 조건부 독립 가정이 성립하지 않음
    - IV, proxy, sensitivity analysis
### 5. Experimental Guidelines
인과추론 방법을 공정하게 비교 및 평가하기 위한 **실험설계 원칙과 평가지표** 제시.
인과추론은 반사실이 관측되지 않는 문제이므로 일반적인 에측성능 평가(ML 평가)와는 다른 접근이 필요.

- 평가의 어려움
    - 개인수준 인과효과는 관측 불가
    - 예측 정확도와 인과 정확도는 다름 
      - 즉, mse 낮아도 인과효과는 틀릴 수 있음
    - 방법마다 목표 estimand(추정대상모수)가 상이함
      - ATE, CATE, ITE 혼재
    - 즉, 정답이 있는 인과데이터는 존재하지 않음
- 사용 데이터
  - semi-synthetic dataset
    - 실제 데이터를 사용하되, outcome 또는 treatment assignment를 인위적으로 생성하여 
    - selection bias와 confounding을 모사한 데이터셋
    - ..다양한 데이터셋은 논문에서 확인하기
- 평가지표 (논문 외)
  - 본 논문은 평가지표제시x, 이후 연구에서는 이러한 문제의 정량화를 위한 지표 사용 
    - PEHE:개인 또는 조건부 처치효과 추정의 정확도를 평가함
    - ATE Error:전체 평균 처치효과의 추정 오차를 측정
    - Policy Risk:추정된 효과로 의사결정했을 떄의 손실을 평가함
### 6. Applications
인과추론이 실제로 적용되는 주요 분야 소개
- 주요 응용 분야
  - 온라인 광고 및 마케팅
  - 추천 시스템
  - 의료 및 헬스케어
  - 정책평가
  - 강화학습

---

## 인과추론 프로세스
1. 인과 질문 정의
   - Treatment(처치): 무엇을 했는가?
   - Outcome(결과): 무엇이 변했는가?
   - Population: 누구에게?
- 
