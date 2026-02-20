---
layout: page
title: Robotics Algorithms (HW1~HW2)
permalink: /projects/robotics/
image: /assets/img/projects/robotics/robotics-thumb.png
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

# Robotics Algorithms & Planning Study (HW1~HW2)

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
