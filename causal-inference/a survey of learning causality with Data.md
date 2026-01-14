## **논문이 말하고자 하는 바**
> 인과는 데이터만으로 자동으로 학습되지 않음  
> 그러나 올바른 인과가정 + 대규모 데이터 + 머신러닝을 결합하면 기존보다 강력한 인과분석 가능  
> 인과는 예측문제가 아님, 가정이 없으면 인과는 불가능함, 데이터가 커질수록 새로운 문제 생김,  
> 머신러닝은 인과의 대체물이 아니라 도구임  

## **논문 요약**
### 1. Introduction
- 대규모 관측데이터 환경에서 인과효과와 인과관계를 학습하는 방법을 정리한 서베이 논문임
- 전통적으로 인과추론은 실험(RCT)와 강한 사전지식에 의존했으나, 최근에는 대규모 데이터로부터 인과를 학습하려는 시도 증가
- 인과 문제를 두가지로 구분
  - Causal Inference: 개입했을 때 결과가 얼마나 변하는가?
  - Causal Discovery: 어떤 변수들이 원인-결과 관계를 갖는가?
- 본 논문의 목표:
  1. 대규모 데이터에서 인과효과를 학습하는 방법 정리
  2. 인과관계(그래프)를 학습하는 방법 정리
  3. 인과추론과 머신러닝의 연결고리 제시
  - 인과추론, 인과효과, 인과관계 정리
  
    | 구분 | 인과추론            | 인과효과 | 인과관계 |
    | --- | --- | --- | --- |
    | 성격 | 학문/과정           | 결과값 | 구조 |
    | 질문 | 개입하면 무슨일이 일어나는가 | 원인을 바꿨을 떄 결과가 얼마나 변하는지 | 어떤 변수가 다른 변수의 원인인지, 구조적 관계 |
    | 출력 | 추론 결과           | 숫자/함수 | 그래프 |
    | 핵심 도구 | do(), 반사실       | ATE / CATE | DAG |
    | 논문 위치 | 전체              | 3장 | 4장 |
### 2. Preliminaries (기초개념)
1. Structural Causal Models (SCM)
   - 기본 아이디어
     - 세상이 어떻게 생성되는가를 직접 모델링 함
     - 변수들 사이의 원인 &rarr; 결과 구조, 각 변수는 구조 방정식으로 정의됨
   - do-연산자
     - 관측
       - $$P(y|t)$$
       - 자연스럽게 t를 선택한 집단에서의 y분포 (선택 이유가 섞여있음)
       - e.g. 이미 평점이 t인 식당들의 평균 고객 수
     - 개입
       - $$P(y|do(t))$$
       - 모든 개체에 대해 t를 외부에서 강제로 t로 설정했을 때의 y분포 (선택 과정 전무 무시)
       - e.g. 외부에서 평점을 **강제로 t**로 바꿨다면 고객 수는?
     - 차이점:
       - `do(t)` 는 t로 들어오는 모든 화살표를 끊어버림 즉, 원래 t를 결정하던 요인들을 무시
   - confounding이 왜 생기는가
     - 인과 그래프가 다음가 같다면
       - ```
         # 관측상태
         x -> t -> y
         x -> y
         
         # 개입상태
         x    t -> y # 관측상태와 다르게 x-> t 화살표 제거
         x -> y
         ```
       - x: 식당 위치
       - t: 평점
       - y: 고객 수
       - 이때, $P(y|t)$는 x의 영향이 섞여있음
       - 그래서, $P(y|t)$ 와 $P(y|do(t))$는 같지 않음
   - 장점
     - 개입, 반사실까지 명확히 정의 가능
     - 인과 그래프를 통해 "왜 안되는지" 설명 가능
   - 단점
     - 인과 구조에 대한 가정 필요
     - 실무에서 그래프를 완벽히 알기 어려움
2. Potential Outcome Framework (잠재적 결과 프레임워크)
   - 기본 아이디어
     - 이 프레임워크는 그래프를 직접 그리지 않음
     - 각 개체는 처치를 받았을 때와 안받았을 때 서로 다른 결과를 동시에 잠재적으로 가지고 있음
     - 항상 하나만 관측 가능하고 다른 하나는 counterfactual(반사실)
   - 장점
     - ATE, CATE, ITE 정의가 직관적
     - 통계적 추정에 매우 적합
   - 단점
     - 왜 식별되는지 구조적 설명이 약함
     - 그래프 없이 가정이 암묵적
3. 인과효과의 핵심 개념
   - Interventional Distribution
     - $P(y|do(t))$
     - 관측데이터에는 직접 존재하지 않아 식별 문제가 생김
   - ATE
     - $ATE = E[y(1)] - E[y(0)]$
     - SCM에서 적용한다면, $ATE = E[y|do(1)] - E[y|do(0)]$
   - 왜 $P(y|t)$를 쓰면 안되는가?
     - 관측 평균 차이: $E[y|t=1] - E[y|t=0]$
     - 이건, 인과효과+선택편향임
     - 인과추론은 이 두 분포의 차이를 어떻게 없애느냐의 문제임
### 3. Learning Causal Effects (인과효과 학습)
1. 문제 정의 및 평가지표
   - 관심대상
     - ATE
     - CATE
     - ITE
   - 평가지표
     - ATE: MAE
     - CATE/ITE: PEHE
2. 관측된 교란변수만 존재하는 경우 (unconfoundedness 가정)
   - 핵심 가정: 모든 교란변수가 관측변수 x에 포함됨
   - 대표 방법
     - Regression Adjustment
       - $E[y|x,t]$ 모델링
     - Propensity Score Methods
       - matching, stratification
     - Covariate Balancing
       - IPTW, Doubly robust estimation
3. 잠재적(미관측) 교란변수가 존재하는 경우
   - Instrumental Variable
     - 처치에는 영향을 주지만 결과에는 직접 영향 x
     - 미관측 교란 존재 시에도 인과효과 식별 가능
   - Front-door Criterion
     - 매개변수를 활용해 인과효과 식별
   - Regression Discontinuity Design ()
     - 처치가 임계값 기준으로 결정되는 경우 활용
4. 빅데이터 환경에서의 확장 방법
  - Neural Network 기반 모델
  - Tree/Ensemble 기반(BART, Causal Forest)
  - Representation Learning+balancing
### 4. Learning Causal Relations (인과관계 학습)
1. 문제정의
  - 변수 . 원인-결과 방향성 및 구조(그래프) 학습
  - 결과는 단일 그래프가 아니라 동치류
2. 평가지표
   - Structural Hamming Distance(SHD)
   - Precision/Recall/ROC
3. 전통적 방법
   - constraint-based
     - PC 알고리즘
     - 조건부 독립성 검정 기반
   - Score-based
     - 점수 함수 최적화(BIC 등)
   - Functional Causal Models
     - ANM, LiNGAM
4. 빅데이터 환경에서의 인과 발견
   - 계산 효율 개선
   - 고차원 변수 처리
   - 샘플 수 대비 변수 수가 많은 상황 대응
### 5. Causality and Machine Learning (인과와 머신러닝의 연결)
1. Supervised/Semi-supervised Learing
  - 인과적 불변성을 활용한 예측 안정성
2. Domain Adaptation/Transportability
   - 환경이 바뀌어도 유지되는 인과 구조 활용
3. Reinforcement Learning
### 6. Conclusions and open problems
- 대규모 데이터는 인과학습의 기회를 제공하지만:
  - 반사실 부족
  - 개입 데이터 부족
  - 강한 가정 필요
- 인과추론과 머신러닝의 결합은:
  - 해석 가능성
  - 일반화 성능
  - 이론적 보장 측면에서 유망