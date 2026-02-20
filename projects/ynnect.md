---
layout: page
title: YNNECT
permalink: /projects/ynnect/
image: /assets/img/projects/ynnect/ynnect-thumb.png
---
<div class="custom-toc" markdown="1">
* TOC
{:toc}
</div>

<style>
/* =========================
   AplusMaker / Robot 동일 포맷
   - 우측 고정 TOC
   - H2만 TOC 노출
   - 본문 가독성 세팅
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

/* TOC 상단 제목 숨김 */
.custom-toc > p:first-child {
  display: none;
}

/* 링크 스타일 */
.custom-toc a {
  text-decoration: none;
  color: inherit;
}

.custom-toc a:hover {
  color: #3b82f6;
}

/* 들여쓰기 */
.custom-toc ul {
  padding-left: 14px;
}

.custom-toc li {
  margin-bottom: 6px;
}

/* ✅ H2만 보이게: (H3 이하 숨김) */
.custom-toc ul ul {
  display: none;
}

/* 모바일 숨김 */
@media (max-width: 1200px) {
  .custom-toc {
    display: none;
  }
}

.page__content,
.page-content,
.page__inner-wrap,
.page__inner-wrap .page__content {
  line-height: 1.9;
  font-size: 1.05rem;
  letter-spacing: 0.15px;
}

/* 본문 폭 */
.page__content,
.page-content {
  max-width: 820px;
}

/* H2 */
.page__content h2,
.page-content h2 {
  font-size: 1.90rem;
  font-weight: 850;
  margin-top: 88px;
  margin-bottom: 28px;
  padding-bottom: 12px;
  border-bottom: 2px solid rgba(0,0,0,0.08);
}

/* H3 */
.page__content h3,
.page-content h3 {
  font-size: 1.30rem;
  font-weight: 780;
  margin-top: 54px;
  margin-bottom: 18px;
}

/* 문단/리스트 */
.page__content p,
.page-content p { margin: 0 0 22px 0; }

.page__content ul,
.page-content ul {
  margin: 0 0 28px 0;
  padding-left: 20px;
}

.page__content li,
.page-content li { margin-bottom: 10px; }

/* 이미지 */
.page__content img,
.page-content img {
  margin: 14px 0 34px 0;
  border-radius: 14px;
}

/* 테이블 */
.page__content table,
.page-content table {
  margin: 18px 0 42px 0;
  font-size: 0.95rem;
}

.page__content thead,
.page-content thead {
  font-weight: 800;
  background: rgba(0,0,0,0.04);
}

/* 강조 */
.page__content strong,
.page-content strong { font-weight: 800; }

/* 코드 */
.page__content pre,
.page-content pre {
  font-size: 0.92rem;
  border-radius: 12px;
  padding: 18px;
  overflow-x: auto;
}

.page__content code,
.page-content code { font-size: 0.92rem; }

/* 구분선 */
.page__content hr,
.page-content hr {
  margin: 70px 0;
  opacity: 0.25;
}
</style>

---

## 1. 프로젝트 소개
위치 · 시간표 · 상태를 공유하는 **캠퍼스 기반 소셜 플랫폼**입니다.  
단순 CRUD가 아니라 **JWT 인증 흐름 표준화**와 **공개 정책(UserSetting) 기반 접근 제어**를 중심으로 백엔드 구조를 설계·구현했습니다.

---

## 2. Tech Stack
![Tools](/assets/img/projects/ynnect/backendTools.png)

### Backend
Java 17 · Spring Boot 3.5.3 · Spring Security · JPA (Hibernate) · JWT 0.11.5

### Database
MariaDB · MySQL Connector/J 8.0.33

### Documentation
OpenAPI 2.2.0

### DevOps & Collaboration
Gradle · Git · GitHub

---

## 3. 주요 화면

### 3-1. 회원가입
![Register](/assets/img/projects/ynnect/register.png)

### 3-2. 메인 지도
![Main](/assets/img/projects/ynnect/main.png)

### 3-3. 친구 관리
![Friend](/assets/img/projects/ynnect/friend.png)

### 3-4. 시간표 관리
![Planner](/assets/img/projects/ynnect/planner.png)

### 3-5. 환경 설정
![Settings](/assets/img/projects/ynnect/settings.png)

---

## 4. 기술 설계 및 문제 해결

### 4-1. 인증 객체 타입 혼재로 발생한 Principal 캐스팅 오류를 JwtUserDetails 단일 흐름으로 통일

**문제**  
컨트롤러마다 Principal 타입이 섞여 들어오면서 캐스팅 오류가 발생했고,  
getId() / getUserId()가 혼용되어 인증 흐름이 흔들렸습니다.

**해결**  
JwtUserDetails를 인증 객체 표준으로 확정하고, 컨트롤러는  
`@AuthenticationPrincipal JwtUserDetails`만 받도록 구조를 통일했습니다.  
JwtAuthenticationFilter에서 토큰 검증 → userId claim 추출 → SecurityContext 주입 흐름을 단일화했습니다.

**결과**  
인증 처리 방식이 전 구간에서 통일되어 캐스팅 오류가 제거되었고,  
기능 확장 시 인증 관련 사이드 이펙트가 크게 줄었습니다.

---

### 4-2. 엔티티 변경이 DTO 응답에 반영되지 않아 발생한 시간표 title undefined 문제를 응답 구조 정비로 해결

**문제**  
시간표 title 필드 추가 이후 프론트에서 undefined가 표시되었고,  
엔티티/DTO 매핑 불일치로 응답 정합성이 깨졌습니다.

**해결**  
UserTimetable.title 필드를 엔티티에 반영하고,  
TimetableResponse DTO에도 동일 필드를 매핑하도록 응답 구조를 정리했습니다.

**결과**  
프론트-백엔드 응답 정합성이 복구되어 시간표 조회/생성 플로우가 안정화되었습니다.

---

### 4-3. 친구 시간표가 무분별하게 조회될 수 있는 구조를 친구 관계 + 공개 설정 조건 분기로 제한

**문제**  
친구 관계만으로 시간표 조회가 가능하면 민감 정보 노출 위험이 있었고,  
공개 설정이 저장 값으로만 남아 정책으로 작동하지 않았습니다.

**해결**  
조회 시점에 친구 여부를 먼저 확인하고,  
`UserSetting.timetablePublic == true`일 때만 조회를 허용하도록 서비스 레벨에서 분기했습니다.

**결과**  
공개 정책이 실제 조회 로직에 반영되며 접근 제어 기준이 명확해졌습니다.

---

### 4-4. 사용자 입력 기반 학기 설정의 오류 가능성을 서버 날짜 기반 자동 계산으로 전환

**문제**  
학기를 사용자 입력에 맡기면 오타/불일치로 데이터 정합성이 깨질 수 있었습니다.

**해결**  
서버에서 날짜 기준으로 학기를 자동 계산하도록 설계했습니다.

- 3~6월: 1학기  
- 6~9월: 여름학기  
- 9~12월: 2학기  
- 12~3월: 겨울학기  

시간표 생성 시 사용자는 **제목만 입력**, 학기는 서버가 결정하도록 흐름을 단순화했습니다.

**결과**  
입력 부담이 줄고 학기 데이터가 일관되게 유지되어 조회/집계 로직이 안정화되었습니다.

---

### 4-5. 가입 직후 연관 엔티티 부재로 발생할 수 있는 Null 분기 문제를 User 중심 1:1 자동 생성 구조로 방지

**문제**  
회원가입 직후 설정/상태/위치 정보가 없으면 조회 로직에서 Null 분기가 발생했고,  
초기 공개 정책/상태 값이 불명확해 서비스 전반의 예외 처리가 늘어날 수 있었습니다.

**해결**  
회원가입 시 `UserSetting`, `UserStatus`, `UserLocation`을 함께 생성해  
초기 공개 정책과 상태 값을 표준화했습니다.  
이를 User 중심 1:1 구조로 묶어 “가입 후 즉시 사용 가능한 상태”를 보장했습니다.

**결과**  
초기 상태가 일관되게 유지되고, 서비스 전반에서 Null 예외 및 방어 코드가 감소했습니다.

---

## 5. Code Samples

### 5-1. User 중심 1:1 연관 엔티티 생명주기 묶음
    @OneToOne(mappedBy = "user", cascade = CascadeType.ALL, orphanRemoval = true)
    private UserLocation locations;

    @OneToOne(mappedBy = "user", cascade = CascadeType.ALL, orphanRemoval = true)
    private UserStatus statuses;

    @OneToOne(mappedBy = "user", cascade = CascadeType.ALL, orphanRemoval = true)
    private UserSetting settings;

User 중심으로 연관 엔티티 생명주기를 묶어  
**가입 직후 Null 분기**를 구조적으로 제거했습니다.

---

### 5-2. 약관 동의 시각 자동 기록(@PrePersist)
    @Column(name = "consented_at", nullable = false)
    private LocalDateTime consentedAt;

    @PrePersist
    protected void onCreate() {
        this.consentedAt = LocalDateTime.now();
    }

서비스 로직 누락 가능성을 줄이기 위해  
엔티티 저장 시점에 **동의 시각이 자동 기록**되도록 설계했습니다.

---

### 5-3. 친구 시간표 조회 접근 제어 분기(친구 여부 + 공개 설정)
    if (!friendshipService.isFriend(meId, friendId)) {
        throw new AccessDeniedException("not friends");
    }

    UserSetting setting = userSettingRepository.findByUserId(friendId)
            .orElseThrow();

    if (!setting.getIsTimetablePublic()) {
        throw new AccessDeniedException("private timetable");
    }

친구 관계 + 공개 설정을 동시에 만족해야 조회 가능하도록  
접근 제어 정책을 서비스 레벨에서 강제했습니다.

---

## 6. 기여도
- 사용자 도메인 및 1:1 연관 구조 설계
- JWT 인증 필터 및 SecurityContext 주입 구조 구현
- 위치/상태/설정/친구/시간표/약관 API 전반 구현
- 공개 정책 기반 접근 제어 로직 설계 및 적용
- DTO 응답 정합성 문제 해결 및 프론트 연동

---

## 7. API Specification (요약)

### 7-1. Auth

| Method | URL | Description |
|--------|-----|-------------|
| POST | /auth/kakao | 카카오 로그인 · JWT 발급 |

### 7-2. Users

| Method | URL | Description |
|--------|-----|-------------|
| GET | /users/me | 내 정보 조회 |
| PATCH | /users/me/nickname | 닉네임 변경 |

### 7-3. Location

| Method | URL | Description |
|--------|-----|-------------|
| POST/PATCH | /locations/me | 내 위치 업데이트(위경도) |

### 7-4. Status

| Method | URL | Description |
|--------|-----|-------------|
| PATCH | /status/message | 상태 메시지 변경 |
| PATCH | /status/public | 상태 공개 여부 변경 |

### 7-5. Settings (Privacy)

| Method | URL | Description |
|--------|-----|-------------|
| GET | /settings/privacy | 공개 설정 조회 |
| PATCH | /settings/privacy | 공개 설정 변경 |

### 7-6. Friends

| Method | URL | Description |
|--------|-----|-------------|
| POST | /friends | 친구 추가 |
| DELETE | /friends/{id} | 친구 삭제 |
| GET | /friends | 친구 목록 조회 |

### 7-7. Timetables

| Method | URL | Description |
|--------|-----|-------------|
| POST | /timetables | 시간표 생성 |
| GET | /timetables | 시간표 목록/조회 |
| DELETE | /timetables/{id} | 시간표 삭제 |

### 7-8. Schedule Items

| Method | URL | Description |
|--------|-----|-------------|
| POST | /schedule-items | 과목 추가 |
| PATCH | /schedule-items/{id} | 과목 수정 |
| DELETE | /schedule-items/{id} | 과목 삭제 |

### 7-9. Terms / Consents

| Method | URL | Description |
|--------|-----|-------------|
| GET | /terms | 약관 목록 조회 |
| POST | /consents | 약관 동의 저장 |

---

## 8. 회고
인증 흐름을 **JwtUserDetails 단일 규칙**으로 고정한 것이 가장 큰 수확이었습니다.  
기능이 늘어도 인증 구조가 흔들리지 않는 기반을 만들 수 있었습니다.

또한 공개 설정을 단순 저장값이 아닌 조회 정책 기준으로 연결하면서  
서비스 정책을 구조적으로 설계하는 경험을 얻을 수 있었습니다.
