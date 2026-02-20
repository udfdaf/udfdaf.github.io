---
layout: page
title: Robotics Algorithms
permalink: /projects/robotics/
image: /assets/img/projects/robotics/robotics-thumb.jpg
---

<div class="custom-toc" markdown="1">
* TOC
{:toc}
</div>

<style>
/* =========================
   읽기 편한 포폴 타이포 세팅
   ========================= */
.custom-toc {
  position: fixed;
  top: 140px;
  right: 60px;
  width: 260px;
  max-height: 70vh;
  overflow-y: auto;
  padding: 18px 20px;
  border-radius: 14px;
  background: rgba(0, 0, 0, 0.04);
  font-size: 0.9rem;
  line-height: 1.6;
}

.custom-toc > p:first-child { display: none; }

.custom-toc a {
  text-decoration: none;
  color: inherit;
}

.custom-toc a:hover { color: #3b82f6; }

.custom-toc ul { padding-left: 14px; }

@media (max-width: 1200px) {
  .custom-toc { display: none; }
}

.custom-toc li { margin-bottom: 6px; }

.custom-toc ul ul { display: none; }

.page__content,
.page-content {
  line-height: 1.9;
  font-size: 1.05rem;
  max-width: 780px;
}

.page__content h2,
.page-content h2 {
  font-size: 1.85rem;
  font-weight: 800;
  margin-top: 92px;
  margin-bottom: 34px;
  padding-bottom: 12px;
  border-bottom: 2px solid rgba(0,0,0,0.08);
}

.page__content h3,
.page-content h3 {
  font-size: 1.32rem;
  font-weight: 750;
  margin-top: 58px;
  margin-bottom: 22px;
}

.page__content p { margin-bottom: 24px; }
.page__content ul { margin-bottom: 30px; padding-left: 20px; }
.page__content li { margin-bottom: 10px; }
.page__content hr { margin: 70px 0; opacity: 0.25; }
</style>

# Robotics Algorithms & Planning Study

서비스 프로젝트와 달리, 이 페이지는 로보틱스 수업 과제를 기반으로  
**이론 → 구현 포인트 → 인사이트**만 남긴 학술형 정리입니다.

---

## 1. Reactive Navigation – Bug Algorithm (HW1)

Bug 알고리즘은 전역 지도 없이도 목표를 향해 이동하다가  
장애물을 만나면 경계를 따라가며 우회하는 **Reactive Navigation** 계열 접근입니다.  

이번 과제에서는 **Bug1, Bug2, TangentBug**를 Unity 3D 환경에서 구현하고  
전환 조건과 경계 추종 전략의 차이를 비교했습니다.

### 핵심 이론 요약

- **Bug1**  
  최초 충돌점(qHit) 기록 → 경계를 한 바퀴 완주 →  
  목표에 가장 가까운 지점(bestQ)에서 이탈

- **Bug2**  
  시작점~목표 직선(M-line)을 정의 →  
  경계 추종 중 M-line 재진입 & qHit보다 목표에 더 가까우면 즉시 이탈

- **TangentBug**  
  센서 기반 가시 정보 활용 →  
  목표가 보이면 직진, 보이지 않으면 endpoint 후보 중 유리한 방향 선택

### 구현 포인트

- 3D 환경이지만 **XZ 평면 거리 기반 판정**으로 단순화
- 전환 직후 재충돌 방지를 위한 cooldown 적용
- M-line 판정 시 tolerance 설정으로 떨림 최소화
- TangentBug에서 endpoint를 HIT↔MISS 전이 지점만 정의해 노이즈 감소

### 회고

이론 자체는 단순하지만, 실제 엔진 환경에서는  
수치 오차와 충돌 이벤트로 인해 전환 조건이 흔들리기 쉽다.  

결국 핵심은 **알고리즘 수식보다 상태 전환 안정화**였다.

> Demo Video: (YouTube 링크 추가 예정)

---

## 2. Robot Motion Modeling – Kinematics (HW2)

이 과제에서는 로봇의 운동을 힘이 아닌  
**좌표 변환과 행렬 연쇄 곱(Transformation Chaining)**으로 모델링했습니다.  

TwoLinkManipulator, OpenManipulator-X, UAV를 구현하며  
관절 기반 로봇과 자유강체 모델의 차이를 비교했습니다.

### 핵심 이론 요약

- **Forward Kinematics (FK)**  
  관절 각도(q) → 말단 위치/자세 계산

- 링크-조인트 체인 구조  
- 변환 행렬의 순차 곱으로 전체 pose 계산

### 구현 구성

- **TwoLinkManipulator**  
  2-DOF 링크 구조에서 H0₁, H1₂를 구성하고  
  H0₂ = H0₁ · H1₂ 방식으로 체인 곱 적용

- **OpenManipulator-X**  
  DH 기반 변환 행렬 Aᵢ를 구성하고  
  T = A0 · A1 · A2 · … 형태로 누적

- **UAV**  
  조인트 체인이 아닌 자유강체 변환 T를 직접 누적  
  로컬 이동 벡터를 월드 좌표로 변환해 적용

- **Grasp 로직**  
  말단 프레임 기준으로 오브젝트를 부모로 재설정하고  
  스케일 왜곡을 방지하는 보정 처리 포함

### 구현에서 중요했던 점

- 로컬 좌표계 vs 월드 좌표계 구분이 가장 중요
- 행렬 곱 순서가 곧 로봇 구조를 의미
- 매니퓰레이터와 UAV는 운동 표현 방식이 근본적으로 다름

### 회고

Kinematics는 단순 계산 문제가 아니라  
**프레임 정의와 모델링 일관성의 문제**라는 걸 체감했다.

> Demo Video: (YouTube 링크 추가 예정)

---

## 3. Potential-Field Based Planning – APBP (HW3)

HW3에서는 기존 Bug 계열처럼 “상태 전환”으로 우회하는 방식이 아니라,  
**연속적인 힘(Force) 기반의 계획(Planning)** 을 구현했다.

핵심 아이디어는 간단하다.

- 목표로 끌어당기는 **인력(Attractive)**
- 장애물에서 밀어내는 **반발력(Repulsive)**

이 두 힘을 합쳐서 매 프레임 이동하면, 별도의 전역 경로 없이도 목표로 수렴할 수 있다.

다만 APF(Artificial Potential Field)의 고질적인 문제인 **Local Minimum(국소 최소점)** 이 존재하고,  
이를 완화하기 위해 **APBP를 “포인트 1개” → “멀티 포인트 + Jacobian 기반”** 으로 확장했다.

> Demo Video: (YouTube 링크 추가 예정)

---

### 3.1 Local Minimum APBP (Point)

가장 기본 형태는 “로봇을 하나의 점(point)”으로 보고 힘을 계산하는 방식이다.  
코드(APBP1.cs)에서는 아래 구조로 구현되어 있다.

#### 구현 구조 (APBP1.cs)

- 현재 위치 `x`에서
  - `F_att(x)` : 목표 방향 인력
  - `F_rep(x)` : 장애물 반발력(영향 반경 `delta` 내에서만 작동)
- `F = F_att + F_rep`
- `x ← x + stepSize * F`

인력은 목표와 가까워질수록 선형(또는 conic/quad 전환)으로 줄어드는 구조를 사용했고,  
반발력은 거리 `r`이 가까울수록 급격히 커지는 형태(1/r 기반)로 구현했다.

#### 한계 (Local Minimum)

현실에서는 “힘의 합이 0에 가까워지는 지점”이 종종 생긴다.

- 목표로 가는 인력과
- 장애물 반발력이

딱 균형을 이루면, 로봇이 **멈추거나 떨리면서 빠져나오지 못하는** 상황이 발생한다.

HW3 PPT에서도 이 Local Minimum 현상을 별도로 강조했고,
실제 Unity 환경에서도 장애물 배치에 따라 쉽게 재현됐다.

#### 회고

Bug 알고리즘은 우회가 “정책(policy)”로 보장되는 반면,  
APF 계열은 “힘의 형태”에 따라 성공/실패가 갈린다.

즉, 이 과제의 핵심은 힘 공식을 외우는 게 아니라  
**Local Minimum을 줄이도록 모델/구성을 바꾸는 것**이었다.

---

### 3.2 Autonomous Vehicle APBP (3 Points)

Local Minimum을 완화하기 위해, 차량을 “점 1개”가 아니라  
**차체 위의 로컬 제어점 3개(Rear/Front/Front-left)** 로 확장했다. (CarAPBP.cs)

PPT에 나온 흐름은 다음과 같다.

1) 현재 차량 자세 `(x, y, θ)` 로부터 각 로컬 제어점의 전역 위치를 계산  
2) 각 제어점에서 목표 인력 + 장애물 반발력을 계산  
3) 제어점의 힘을 **Jacobian Transpose** 로 차량 구성공간 `(x, y, θ)` 변화량으로 변환해 누적

#### 구현 포인트 (CarAPBP.cs)

- **Forward Kinematics**
  - 로컬 제어점 `a_i` 를 차량 자세 `(x,y,θ)` 로 월드 좌표 `p_i`로 변환
- **Workspace Force**
  - 제어점별 목표(g1,g2,g3)로 인력 계산
  - 장애물 리스트로부터 반발력 합산
- **Jacobian Transpose Mapping**
  - 각 제어점의 Jacobian `J_i` 를 구성하고,
  - `F_q += J_i^T * F_w_i` 로 차량 전체의 `(x,y,θ)` 업데이트

결과적으로 차량은 “한 점이 끌리는” 느낌이 아니라,  
**차체의 여러 지점이 동시에 목표에 정렬되려는 힘**을 만들게 되고,  
이게 Local Minimum에 대한 저항성을 높여준다.

#### 회고

이 파트에서 재미있었던 점은,
“APF”가 단순히 이동 벡터가 아니라 **제어(controls) 문제**로 확장된다는 점이었다.

- 포인트 힘(Workspace)을
- 구성공간(차량 pose)로 바꾸는 순간

그냥 경로 계획이 아니라, 로봇 제어 쪽 감각이 섞이기 시작했다.

---

### 3.3 UAV APBP (3 Points)

UAV는 차량과 달리, 몸체가 3D로 회전하며 로컬 포인트가 함께 움직인다.  
UAVAPBP.cs에서는 UAV 본체에 대해 **로컬 포인트 3개를 바디 프레임 기준으로 두고**,  
이를 월드로 변환해서 힘을 계산하는 구조를 사용했다.

#### 구현 핵심 (UAVAPBP.cs)

- `R(roll,pitch,yaw)` 회전 행렬로
  - `worldPt_i = pos + R * localPt_i` 로 월드 포인트 갱신
- 각 포인트에서 인력/반발력 계산
- 포인트 힘을 합쳐 **6DOF twist(선속도+각속도)** 로 변환

여기서 핵심은 “Jacobian을 직접 만들고 DLS(damped least squares)로 풀었다”는 점이다.

- `J` : (포인트 3개 × 3차원) → 총 9차원 workspace
- `twist` : 6차원 (vx,vy,vz, wx,wy,wz)
- DLS 형태:
  - `twist = (J^T J + λ^2 I)^(-1) J^T V`

또한 코드에는 **localMinima 감지**가 들어가 있다.

- 힘 합 `|Fsum|`이 아주 작아지면 local minimum으로 판단
- 일정 시간 restoration(탈출) 모드로 바꿔서 빠져나오는 로직을 시도

#### 회고

차량 파트가 “(x,y,θ) 업데이트”였다면,  
UAV 파트는 “(선속도+각속도)로 포인트들을 동시에 만족시키기”라서  
수학적으로 훨씬 제어 느낌이 강했다.

개인적으로는 이 부분이 HW3에서 가장 “로봇스러웠다”.

---

### 3.4 MyOwnRobot APBP (UAV 3 Points + OpenManipulator-X Multi Points)

마지막은 내가 만든 “통합 로봇”에 APBP를 적용한 파트다.

- UAV 본체에 로컬 포인트 3개
- OpenManipulator-X에 조인트/말단 기준 포인트들을 추가해
- 전체를 한 번에 움직여 목표를 만족시키는 형태를 시도했다. (MyOwnRobotAPBP.cs)

#### 구현 구성 (MyOwnRobotAPBP.cs / MyOwnRobot.cs / GoalGenerator.cs)

- MyOwnRobot.cs
  - UAV 글로벌 transform + Manipulator 로컬 FK를 통합한 “기준 구조”
- MyOwnRobot_GoalGenerator.cs
  - 목표 조인트/링크(시각화용) 생성 및 정렬
- MyOwnRobotAPBP.cs
  - UAV(6DOF) + Arm(4DOF)를 하나의 구성공간으로 두고
  - Jacobian 기반으로 각 포인트 힘을 조인트 변화량으로 맵핑

즉, 목표는 다음이었다.

- 여러 포인트(몸체 + 팔)가 동시에
  - 목표로 끌리고,
  - 장애물을 피하면서
  - 자연스럽게 수렴하는 “연속 제어” 만들기

#### 어려웠던 점 
  
UAV 3 + Arm 4(or 5) 포인트 통합은 “완전히 안정적이진 않았다”.

원인은 구현 관점에서 딱 3가지로 정리된다.

1) **프레임 기준이 섞이면 Jacobian이 무너진다**  
   - UAV는 월드/바디 프레임을 왔다 갔다 하고
   - Arm은 로컬 FK 체인으로 pose가 누적된다  
   → 포인트별 Jacobian 기준을 통일하지 않으면 힘 전달이 비정상적으로 튄다.

2) **스케일/게인(zeta, eta) 밸런싱이 훨씬 어렵다**  
   - 몸체 포인트는 큰 스케일로 움직이고
   - 조인트는 작은 각 변화로 큰 위치 변화가 생긴다  
   → 같은 힘을 걸면 “팔이 폭주”하거나 “몸체만 움직임” 같은 편향이 생긴다.

3) **Local minimum이 ‘조인트 공간’에서도 생긴다**  
   - 단순히 장애물 배치 문제가 아니라,
   - 조인트 제약(가동범위/특이점) 때문에
   - 힘이 상쇄되는 구간이 나타난다.

#### 회고

HW1(Bug)에서는 “상태 전환을 안정화하는 게 핵심”이었다면,  
HW3(APBP)에서는 “수학이 아니라 모델링 일관성”이 핵심이었다.

특히 통합 로봇 파트는,
APBP가 단순 경로계획이 아니라 **전신 제어(whole-body control) 문제**로 커진다는 걸 체감했다.

> Demo Video: (YouTube 링크 추가 예정)

---

## 4. Sampling-Based Motion Planning – PRM / RRT / EST (HW4)

HW4에서는 APF처럼 힘을 합산해 이동하는 방식이 아니라,  
**구성공간(Configuration Space)을 샘플링하여 경로를 생성하는 Planning 알고리즘**을 구현했다.

이번 과제에서 사용한 구성공간은 6차원이다.

- Base 위치: (x, y, z)
- 관절 각도: (θ0, θ1, θ2)

즉 하나의 노드는 `[x, y, z, θ0, θ1, θ2]` 로 표현되며,  
계획의 핵심은 “샘플 생성”보다도 아래 두 검증이었다.

- 노드 충돌 검사 (IsInCollision)
- 엣지 충돌 검사 (IsEdgeInCollision)

점은 안전해도, 그 사이 이동이 안전하다는 보장은 없기 때문에  
엣지 보간 검증이 실제 구현의 핵심이었다.

---

### 핵심 이론 요약

#### PRM (Probabilistic Roadmap)

- 랜덤 샘플을 공간에 뿌려 그래프를 먼저 구성
- k-nearest로 연결 시도
- 충돌 없는 간선만 유지
- 그래프 탐색으로 경로 산출

→ 다중 질의에 유리한 “그래프 기반” 접근

#### RRT (Rapidly-exploring Random Tree)

- start에서 트리를 확장
- 랜덤 샘플을 향해 한 스텝씩 전진
- goalTolerance 도달 시 종료

→ 단일 질의에 강한 “트리 확장” 접근

#### EST (Expansive Space Trees)

- RRT와 유사한 트리 구조
- 하지만 확장 노드를 “희소 지역 우선”으로 선택
- local density 기반 가중 확장

→ 복잡한 공간에서 더 균형 잡힌 탐색 시도

---

### 구현 구성

#### 1) Config 표현

- 하나의 노드를 6D 벡터로 정의
- 위치 + 관절 각도를 하나의 구성공간으로 통합

#### 2) 충돌 판정

- 노드 단위 충돌 검사
- 두 config 사이를 일정 step으로 보간하며 엣지 충돌 검사
- posStep / jointStepDeg 파라미터로 샘플 간격 제어

#### 3) PRM 구조

- 랜덤 샘플 numSamples 생성
- kNeighbors 기반 연결 시도
- 인접 리스트 그래프 구성
- start/goal을 그래프에 연결 후 탐색

#### 4) RRT 구조

- goalBias 확률 적용
- nearest 탐색
- steer 한 스텝 확장
- parent 포인터 기반 경로 복원

#### 5) EST 구조

- neighborRadius 내 이웃 수 계산
- weight = 1 / (1 + count)
- 가중 확률 기반 확장 노드 선택
- RRT와 동일한 steer/검증 흐름 유지

#### 6) PlanningManager 흐름

1. Start / Goal config 설정
2. 선택된 Planner 실행
3. 경로 생성 후 Final Path 렌더링
4. speed 기반 보간으로 실제 로봇 추종

---

### 구현에서 중요했던 점

- **엣지 충돌 검증이 핵심이었다**  
  노드만 안전해도 경로가 안전하다는 보장은 없다.

- **구성공간 통합이 중요했다**  
  위치와 관절을 분리하지 않고 하나의 6D 공간으로 다뤄야 자연스럽다.

- **Goal Bias가 수렴 속도에 큰 영향**  
  너무 크면 편향, 너무 작으면 비효율적 확장.

- **Density 기반 확장의 체감 효과**  
  EST는 복잡한 공간에서 RRT보다 덜 편향된 탐색 경향을 보였다.

- **시각화가 디버깅에 결정적이었다**  
  1프레임 1노드 확장 구조 덕분에 실패 원인을 눈으로 확인 가능했다.

---

### 회고

Sampling-based planning은 알고리즘 수식보다도

- 구성공간 정의
- 엣지 검증 정확도
- 확장 전략 균형

이 더 중요했다.

특히 “점 충돌이 아니라 엣지 충돌이 진짜 문제였다”는 점이  
이번 과제의 가장 큰 깨달음이었다.

> Demo Video: (YouTube 링크 추가 예정)

---

---

## 5. Probabilistic State Estimation – Bayes Filter & Kalman Filter (HW5)

HW5는 “경로를 만드는 문제”가 아니라,  
**내가 지금 어디에 있는지(상태)를 확률적으로 추정하는 문제**였다.

Planning(PRM/RRT/EST)이 “길을 뽑는 것”이라면,  
Filtering(Bayes/Kalman)은 “지금 믿고 있는 위치 분포를 매 순간 갱신하는 것”에 가깝다.

이번 과제에서 내가 다룬 핵심은 결국 이 한 줄로 정리된다.

- **Predict(예측):** 움직였으니 belief가 퍼진다  
- **Update(갱신):** 관측했으니 belief가 좁아진다

이 두 단계를 반복하면, “처음에는 아무데나 있을 수 있다”에서 시작해  
점점 실제 위치 근처로 확률이 수렴해간다.

> Demo Video: (추가 예정)

---

### 핵심 이론 요약

#### Bayes Filter (Discrete)
- 상태 공간을 격자(1D/2D cell)로 나누고  
  각 칸에 “내가 거기 있을 확률”을 저장한다.
- 예측은 **전이(transition)** 혹은 **convolution**으로 퍼지고,
- 갱신은 **관측 likelihood p(z|x)** 로 곱해서 강조된다.
- 마지막은 항상 **정규화(normalization)** 로 확률합을 1로 만든다.

#### Kalman Filter (Gaussian / Linear)
- belief 자체를 “전체 분포 배열”로 들고 가는 게 아니라,
  **평균(μ)과 분산/공분산(P)** 만 들고 간다.
- 가정은 강력하다:
  - 시스템이 선형(linear)
  - 노이즈가 가우시안(Gaussian)
- 그래서 업데이트는 “확률 배열 곱”이 아니라,
  **Kalman Gain(K)** 으로 측정값과 예측값을 섞는 방식으로 된다.

---

## 5.1 Bayes Filter – 1D (Filter_1D.py)

### 구현 구성
- 상태 공간: `num_positions = 200` (0~199)
- 랜드마크: `[30, 60, 100, 190]`
- belief: 처음엔 균일분포(아무데나 있을 수 있음)
- motion model:
  - `motion_range = [-3..+3]`
  - 커널은 “대부분 +1로 이동”하는 확률 구조 (편향 이동)
- sensor model:
  - 랜드마크 근처일수록 likelihood가 커지는 형태
  - (여러 landmark에 대한 가우시안들을 합친 맵)

### 코드 흐름 (Predict → Update)
1) **예측 (Predict)**
- `bel_hat = belief @ T`
- 여기서 중요한 건 `T`를 “전이 행렬”로 직접 만든 점이다.  
  즉, kernel을 그냥 더하는 게 아니라 “상태 전이 확률표”로 확장해서 곱으로 처리한다.

2) **센서 감지 판단 (sensed)**
- 실제 위치(true_pos)가 랜드마크 반경(sensor_range) 안이면 sensed = True

3) **갱신 (Update)**
- sensed면:
  - `bel = bel_hat * p_z_given_x`
  - `bel /= bel.sum()` (정규화)
- 아니면:
  - `bel = bel_hat` (센서가 없으면 퍼진 상태로 유지)

4) **실제 위치 이동 (true_pos update)**
- motion_kernel 기반으로 실제 이동을 샘플링해서 true_pos를 움직인다.  
  즉 belief만 움직이는 게 아니라, “진짜 로봇도 확률적으로 움직인다”를 같이 시뮬레이션했다.

### 구현에서 중요했던 점
- **belief는 “예측만 하면 무조건 퍼진다”**  
  motion kernel이 퍼짐을 만드는 순간, 분포는 계속 넓어지는 게 정상이다.
- **Update는 “곱”이고, 곱은 “강조”다**  
  bel_hat에 p(z|x)를 곱하는 순간, “센서가 말해주는 위치 주변”이 확률적으로 살아남는다.
- **정규화가 없으면 분포가 분포가 아니다**  
  곱을 하면 합이 1이 깨지니까, 매번 sum으로 나눠서 다시 확률로 만들어야 한다.

### 내가 고민했던 포인트 (직관)
- 이 구조를 돌리면 느낌이 딱 이렇다:
  - **Predict:** “움직였으니까 대충 이쯤일 것 같아” (흐려짐)
  - **Update:** “근데 센서가 랜드마크 근처래” (그쪽으로 다시 모임)
- 즉, **예측은 확산이고, 관측은 수렴**이다.

> 개인적으로 Filter는 수식보다도  
> “퍼지는 단계”와 “좁아지는 단계”가 싸우면서 균형 잡는 느낌이 더 직관적이었다.

---

## 5.2 Bayes Filter – 2D (Filter_2D.py)

### 구현 구성
- 상태 공간: `50 x 50` grid
- goal: (48, 48) 근처면 정지하도록 설정
- motion model:
  - 3x3 kernel (주로 (1,1) 방향으로 이동하는 편향)
  - 예측 단계에서 `convolve2d`로 belief를 확산시킨다.
- sensor model:
  - 랜드마크 여러 개를 두고, 그 근처일수록 p(z|x)가 큰 likelihood 맵을 미리 계산해둔다.

### 코드 흐름 (Predict → Update)
1) **실제 이동 샘플링**
- goal에 가까우면 정지, 아니면 motion_kernel 확률로 이동 방향을 뽑는다.
- 여기서부터 이미 “로봇이 대충 goal 방향으로 가지만 흔들린다”는 느낌이 생긴다.

2) **예측 (Predict)**
- `bel_hat = convolve2d(belief, motion_kernel, mode='same')`
- 1D에서 행렬곱으로 하던 걸, 2D에선 convolution으로 처리한 게 포인트다.  
  결과적으로 의미는 동일하다: “움직임 모델로 belief를 퍼뜨린다.”

3) **관측 판단 & 갱신 (Update)**
- true_pos가 랜드마크 반경 안이면 sensed=True
- sensed면 bel_hat에 p(z|x) 곱해서 업데이트 + 정규화

4) **시각화**
- bel_hat / p(z|x) / updated belief를 한 화면에서 나란히 보여준다.
- 이게 진짜 디버깅에 도움이 된다:
  - “확산이 잘 되고 있는지”
  - “센서 맵이 어디를 누르는지”
  - “업데이트 후 어디가 살아남는지”
  를 한 번에 눈으로 확인 가능하다.

### 구현에서 중요했던 점
- **2D에서는 예측이 곧 convolution이다**  
  “내 belief를 kernel로 블러 처리하는 느낌”이 곧 motion update다.
- **센서가 들어오면 belief가 급격히 좁아진다**  
  2D에서 이게 더 드라마틱하다. heatmap이 확 줄어든다.
- **grid 기반 Bayes는 ‘가능성 지도’를 직접 들고 간다**  
  대신 상태 수가 커지면 계산이 바로 무거워진다 (2D만 가도 체감된다).

### 회고 (직관)
2D로 확장하니까 Bayes Filter의 느낌이 더 선명해졌다.

- Predict는 “연기처럼 퍼짐”
- Update는 “스포트라이트처럼 특정 영역만 남김”

결국 필터링은 “내가 믿는 지도”를 계속 다시 그리는 작업이었다.

---

## 5.3 Kalman Filter – 1D (Filter_1D.py)

Bayes가 “확률 배열 전체”를 다루는 방식이라면,  
Kalman은 훨씬 압축적이다.

- 분포를 통째로 들고 다니지 않고,
- **평균(x̂)과 분산(P)** 만 들고 간다.

### 핵심 이론 요약
- 예측:
  - `x_pred = A x̂ + B u`
  - `P_pred = A P A^T + Q`
- 갱신:
  - `K = P_pred C^T (C P_pred C^T + R)^-1`
  - `x̂ = x_pred + K (z - C x_pred)`
  - `P = (I - K C) P_pred`

### 구현 구성
- A=B=C=1 (1차원 선형 시스템)
- 실제값은 프로세스 노이즈(Q)로 흔들리고,
- 측정값은 측정 노이즈(R)로 크게 흔들린다.

### 구현에서 중요했던 점 (직관)
- **K(칼만 이득)는 “측정을 얼마나 믿을지”를 숫자로 만든 것**
  - R이 크면(측정이 더럽다면) K가 작아지고 예측을 더 믿는다.
  - P가 크면(내 추정이 불확실하면) K가 커지고 측정을 더 받아들인다.
- 결국 Kalman은 이 한 줄로 이해되는 느낌이었다:
  - “지금은 센서를 믿을까, 내 모델을 믿을까?”

---

## 5.4 Kalman Filter – 2D (Filter_2D.py)

### 구현 구성
- 상태: 2D 위치 벡터 `x = [x, y]`
- A,B,C 모두 identity
- Q는 작게(모델은 대체로 맞다고 가정)
- R은 크게(측정은 많이 흔들린다고 가정)
- 시각화:
  - true / estimate / measurement 경로
  - det(P): 불확실성 “부피” 느낌
  - ‖K‖: 칼만이득 크기
  - ‖x - x̂‖: 추정 오차

### 구현에서 중요했던 점
- **det(P)는 직관적으로 “불확실성 영역의 크기”처럼 보인다**
  - 시간이 지날수록 det(P)가 줄면,
    “이제 내 위치를 더 확신한다”로 해석할 수 있다.
- **2D로 오면 Kalman이 ‘확률’이라기보다 ‘추정기(estimator)’ 같다**
  - Bayes는 확률지도를 직접 그리는 느낌이라면,
  - Kalman은 센서 데이터를 잘 섞어서 평균을 안정화하는 느낌이 강했다.

### 회고 (Bayes vs Kalman을 실제로 체감한 차이)
- Bayes Filter:
  - 장점: 분포 형태가 마음대로 가능 (멀티모달도 가능)
  - 단점: 상태 수 늘면 계산량이 터진다
- Kalman Filter:
  - 장점: 계산이 가볍고 업데이트가 깔끔하다
  - 단점: 분포가 가우시안/선형 가정에서 벗어나면 표현이 깨진다

이번 과제에서 가장 크게 남은 인사이트는 이거였다.

> “Bayes는 확률 자체를 들고 가는 방식이고,  
> Kalman은 확률을 ‘평균+불확실성’으로 요약해서 들고 가는 방식이다.”

그리고 이 차이는 수식을 몰라도,  
애니메이션을 몇 번만 돌려보면 바로 체감이 됐다.

> Demo Video: (YouTube 링크 추가 예정)

---

## 6. Reinforcement Learning – Cliff Walking (HW6)

HW6에서는 “경로 계획을 직접 짜는” 방식이 아니라,  
에이전트가 **시행착오로 정책(policy)을 학습**하는 강화학습(Reinforcement Learning)을 다뤘다.

환경은 전형적인 **Cliff Walking(GridWorld 4×12)** 이고, 핵심은 간단하다.

- 매 스텝마다 **-1** (빨리 도착할수록 유리)
- 절벽(Cliff)에 떨어지면 **-100** + 시작점으로 리셋 (큰 위험)
- 목표는 시작점에서 목표점까지 **누적 보상(return)을 최대화**하는 것

이번 구현에서는 Action Space를 **4방향 / 8방향**으로 바꿔가며,  
알고리즘이 “위험”을 어떻게 다르게 반영하는지 비교했다.

> Demo Video: (YouTube 링크 추가 예정)

---

### 핵심 이론 요약

#### (1) Monte Carlo Control (MC Control) — “에피소드 평균으로 배운다”
- 한 에피소드(시작→종료)가 **끝난 뒤**에야 업데이트한다.
- 업데이트의 재료는 에피소드 전체의 누적 보상(Return).

직관적으로는,  
“이 행동을 했을 때, 결국 끝까지 갔을 때 평균적으로 얼마나 손해/이득이었나?”를 보고  
좋았던 행동은 올리고, 나빴던 행동은 내리는 방식이다.

---

#### (2) Q-learning — “미래 최댓값 기준으로 배운다 (off-policy)”
- 업데이트는 매 스텝 즉시(Temporal Difference).
- 다음 상태에서의 가치로 **max Q(s', a')** 를 사용한다.  
  즉 “내가 다음에 뭘 하든 상관없이, 최선만 한다면?”을 기준으로 학습한다.

직관적으로는,  
“위험이 있더라도 이론상 최선의 경로가 더 큰 보상을 준다면 그쪽으로 간다.”  
그래서 Cliff Walking에서는 **절벽 가장자리의 가장 짧은 길**을 선호하는 경향이 생긴다.

---

#### (3) SARSA — “내가 실제로 할 행동 기준으로 배운다 (on-policy)”
- 업데이트는 매 스텝 즉시(Temporal Difference).
- 다음 상태에서의 가치로 **Q(s', a')** 를 사용하되, a'는 **현재 정책(ε-greedy)로 실제 선택된 행동**이다.

직관적으로는,  
“내가 다음에도 탐험(실수)을 할 수 있잖아? 그 위험까지 포함해서 생각하자.”  
그래서 Cliff Walking에서는 다소 돌아가더라도 **안전한 경로**를 학습하는 경향이 강해진다.

---

### 구현 구성

#### 1) 환경 (GridWorld4 / GridWorld8)
- 상태: `(x, y)`
- 시작: `(3, 0)`
- 목표: `(3, 11)`
- 절벽: `(3, 1) ~ (3, 10)`
  - 도착 판정 기준으로 Cliff면 **-100** 받고 시작으로 리셋

Action Space 확장:
- **4-action**: →, ←, ↑, ↓
- **8-action**: →, ←, ↑, ↓, ↗, ↙, ↖, ↘  
  (대각 이동은 실제로 “한 번에 x와 y를 같이 바꾸는” 스텝이라서, 경로 길이를 줄일 수 있다.)

---

#### 2) 공통 정책: ε-greedy
- `prob < eps` 이면 **탐험(explore)** : 랜덤 행동
- 아니면 **활용(exploit)** : 현재 Q-table에서 argmax 행동

여기서 구현상 중요한 디테일이 있다.

- 랜덤 행동은 `0 ~ (num_actions-1)` 범위를 뽑아야 한다.  
  파이썬 `random.randint(a, b)` 는 **b를 포함(inclusive)** 하기 때문에,
  `random.randint(0, num_actions-1)` 가 맞다.  
  (num_actions가 8이면 0~7이 액션 인덱스인데, 0~8을 뽑으면 “존재하지 않는 액션”이 생긴다.)

이게 단순한 버그 수정처럼 보이지만,  
실제로는 “탐험 단계에서 선택 가능한 행동 공간”을 결정하는 거라 정책 분포가 달라지고,  
특히 8-action 실험에서는 결과가 민감하게 바뀔 수 있다.

---

#### 3) 알고리즘별 업데이트 흐름

- **MC Control**
  1) 에피소드 전체 history 저장
  2) 뒤에서부터 누적 보상(cum_reward) 계산
  3) `Q(s,a) ← (1-α)Q + α * Return` 형태로 업데이트

- **Q-learning**
  1) 한 스텝 transition `(s,a,r,s')` 관측
  2) `Q(s,a) ← (1-α)Q + α * (r + max_a' Q(s',a'))`

- **SARSA**
  1) 한 스텝 transition `(s,a,r,s')` 관측
  2) 다음 행동 `a'` 를 현재 정책으로 뽑음
  3) `Q(s,a) ← (1-α)Q + α * (r + Q(s',a'))`

---

### 구현에서 중요했던 점

- **“위험을 반영하는 방식”이 업데이트 식에 직접 박혀 있다**
  - Q-learning: 미래는 “최선만 한다” → 위험을 덜 본다 → 절벽 옆 최단 경로
  - SARSA: 미래도 “탐험을 한다” → 실수 위험 포함 → 안전 경로
  - MC: 에피소드 평균으로만 보니까, 절벽 낙하가 반복되면 특정 상태의 값이 크게 흔들릴 수 있다

- **4-action vs 8-action은 ‘표현력’ 자체를 바꾼다**
  - 모든 스텝 보상이 -1이라면, 결국 “더 적은 스텝 수 = 더 좋은 return”이다.
  - 그래서 8-action에서는 대각 이동이 허용되면서, 정책이 더 공격적으로 보이거나
    혹은 더 부드럽게 “짧은” 경로로 수렴할 수 있다.

- **ε-greedy의 ‘탐험 범위’가 실험 결과를 바꾼다**
  - 랜덤 탐험이 4개 행동만을 뽑게 되면, 8-action 환경에서 대각의 학습 기회가 줄어든다.
  - 반대로 8개를 다 탐험하게 하면, 더 다양한 경로를 빨리 발견할 수도 있지만
    절벽 낙하 같은 큰 패널티를 더 자주 경험해 학습이 흔들릴 수도 있다.

---

### 회고

Cliff Walking이 재미있었던 이유는, “같은 환경”인데도

- 어떤 알고리즘은 **절벽 옆을 아슬아슬하게 달리고**
- 어떤 알고리즘은 **멀리 돌아 안전하게 가는**

완전히 다른 성격의 정책을 만든다는 점이었다.

결국 이번 과제에서 남은 한 줄은 이거였다.

**강화학습은 ‘정답 경로’를 외우는 게 아니라, 업데이트 방식이 곧 위험을 정의한다.**
