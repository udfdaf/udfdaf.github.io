---
layout: page
title: AplusMaker
permalink: /projects/aplusmaker/
image: /assets/img/projects/aplusmaker/aplusmaker-thumb.png
---
<div class="custom-toc" markdown="1">
* TOC
{:toc}
</div>

<style>
/* =========================
   읽기 편한 포폴 타이포 세팅
   (내용은 그대로, 보이기만 개선)
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

/* 제목 숨기기 (원하면 유지 가능) */
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

/* 중첩 레벨 들여쓰기 */
.custom-toc ul {
  padding-left: 14px;
}

/* 모바일에서는 숨김 */
@media (max-width: 1200px) {
  .custom-toc {
    display: none;
  }
}
.custom-toc li {
  margin-bottom: 6px;
}
.custom-toc ul ul {
  display: none;
}
.page__content,
.page-content,
.page__inner-wrap,
.page__inner-wrap .page__content {
  line-height: 1.9;
  font-size: 1.05rem;
  letter-spacing: 0.2px;
}

/* 본문 폭 제한: 너무 넓으면 눈 피로 */
.page__content,
.page-content {
  max-width: 780px;
}

/* H2: 섹션 단위가 한눈에 들어오게 */
.page__content h2,
.page-content h2 {
  font-size: 1.85rem;
  font-weight: 800;
  margin-top: 92px;
  margin-bottom: 34px;
  padding-bottom: 12px;
  border-bottom: 2px solid rgba(0,0,0,0.08);
}

/* H3: 문제/해결 단위 강조 */
.page__content h3,
.page-content h3 {
  font-size: 1.32rem;
  font-weight: 750;
  margin-top: 58px;
  margin-bottom: 22px;
}

/* 문단/리스트/테이블 여백 */
.page__content p,
.page-content p { margin-bottom: 24px; }

.page__content ul,
.page-content ul {
  margin-bottom: 30px;
  padding-left: 20px;
}

.page__content li,
.page-content li { margin-bottom: 10px; }

.page__content table,
.page-content table {
  margin-bottom: 40px;
  font-size: 0.95rem;
}

.page__content thead,
.page-content thead {
  font-weight: 700;
  background: rgba(0,0,0,0.04);
}

/* 강조 텍스트 더 선명하게 */
.page__content strong,
.page-content strong { font-weight: 750; }

/* 코드 블록 가독성 */
.page__content pre,
.page-content pre {
  font-size: 0.92rem;
  border-radius: 12px;
  padding: 18px;
  overflow-x: auto;
}

/* 인라인 코드 */
.page__content code,
.page-content code {
  font-size: 0.92rem;
}

/* 구분선 여백 */
.page__content hr,
.page-content hr {
  margin: 70px 0;
  opacity: 0.25;
}
</style>

## 1. 프로젝트 소개
문제 유형 · 키워드 · 과목 조건을 기반으로 맞춤형 문제를 추천하고,  
추천 이력 저장과 커뮤니티 확장을 통해 학습 데이터를 축적하도록 설계한 문제은행 플랫폼입니다.

단순 CRUD가 아니라,  
**추천 요청 단위 데이터 설계 · 커뮤니티 확장 흐름 · 활동 통계 집계 구조**까지 고려하여  
“학습 → 기록 → 공유 → 데이터 축적”이 이어지는 구조를 설계했습니다.

---

## 2. Tech Stack

### 2-1. Backend
`Java` · `Spring Boot` · `Spring Security` · `JPA (Hibernate)`

### 2-2. Database
`MariaDB` · `MySQL`

### 2-3. View
`Thymeleaf`

### 2-4. Documentation
`OpenAPI (Swagger)`

### 2-5. DevOps & Collaboration
`Gradle` · `Git` · `GitHub`

---

## 3. Information Architecture

![IA](/assets/img/projects/aplusmaker/aplusmaker_ia.png)

### 3-1. 구조 설계 의도

- 문제 추천 / 커뮤니티 / 마이페이지 기능을 도메인 단위로 분리
- 추천 결과를 게시글 작성과 연결하여 서비스 흐름 확장
- 사용자 활동 데이터를 누적 가능한 구조로 설계

---

## 4. ERD

![ERD](/assets/img/projects/aplusmaker/erd.png)

### 4-1. 핵심 설계 포인트

- 추천 요청 1회 = `ProblemRecordGroup`
- 추천된 각 문제 = `ProblemRecord`
- `Member 1:N Group`
- `Group 1:N Record`
- `Record N:1 Problem`

추천 “결과”가 아니라 추천 “행위 단위”를 저장하도록 구조를 설계했습니다.

이를 통해:

- 추천 기록 복원
- 통계 집계
- 학습 데이터 분석 확장

이 가능한 구조를 확보했습니다.

---

## 5. 기술 설계 및 문제 해결

---

### 5-1. 추천 요청 단위 추적이 불가능한 구조를 그룹 기반 기록 설계로 개선하여 추천 이력 복원 가능성을 확보했습니다

**문제**  
추천 1회 요청에서 여러 문제가 생성되지만, 문제 단위로만 저장하면 추천 맥락을 추적할 수 없었습니다.

**해결**  
추천 요청 단위를 `ProblemRecordGroup`으로 분리하고,  
추천된 각 문제를 `ProblemRecord`로 1:N 관계로 저장하도록 재설계했습니다.

**결과**  
추천 기록을 그룹 단위로 복원할 수 있게 되었고,  
사용자 학습 데이터 축적 기반을 확보했습니다.

---

### 5-2. 단순 랜덤 조회의 한계를 조건 기반 필터 로직으로 개선하여 사용자 의도 반영도를 높였습니다

**문제**  
무작위 문제 조회는 사용자 조건(유형, 키워드)을 반영하지 못했습니다.

**해결**  
`question_type` 필터 + `LIKE` 기반 키워드 조건을 결합한 추천 로직을 구현했습니다.  
Native Query를 사용하여 조건과 랜덤을 동시에 처리했습니다.

**결과**  
사용자 조건에 맞는 문제 추천이 가능해졌고,  
추천 품질과 사용 만족도를 개선할 수 있었습니다.

---

### 5-3. 추천 결과 소비로 끝나는 구조를 게시판 자동 확장 흐름으로 연결하여 학습-공유-토론 구조를 확보했습니다

**문제**  
추천 결과가 화면 소비로 끝나 서비스 확장성이 부족했습니다.

**해결**  
추천 결과를 게시글 작성 폼(`ArticleFormDTO`)에 자동 주입하도록 설계했습니다.  
`/exam/to-post/{groupId}`를 통해 추천 결과를 커뮤니티로 확장했습니다.

**결과**  
학습 → 공유 → 토론으로 이어지는 사용자 흐름을 구축했고,  
추천 기능이 커뮤니티 성장과 연결되도록 설계했습니다.

---

### 5-4. 게시글 삭제 시 데이터 무결성 문제를 선삭제 정책으로 정리하여 참조 무결성을 확보했습니다

**문제**  
게시글 삭제 시 댓글/좋아요가 남아있으면 무결성 문제가 발생했습니다.

**해결**  
게시글 삭제 시:
1) 댓글 삭제  
2) 좋아요 삭제  
3) 게시글 삭제  
순으로 처리하도록 정책을 명확히 설계했습니다.

**결과**  
외래키 제약과 무결성을 안정적으로 유지할 수 있게 되었습니다.

---

### 5-5. 마이페이지 전체 조회 부담을 count 기반 집계 분리 구조로 개선하여 조회 비용을 최소화했습니다

**문제**  
마이페이지에서 모든 활동 데이터를 join으로 조회하면 성능 부담이 발생할 수 있었습니다.

**해결**  
- 문제 풀이 수: `countByUserId`
- 게시글 수: `countByUser_Id`
- 댓글 수: `countByUser_Id`
- 좋아요 수: `countByUser_Id`

각 기능별 count 쿼리로 분리 집계했습니다.

또한 favorites는 요청 시에만 Top10 제한 조회하도록 설계했습니다.

**결과**  
페이지 로딩 부담을 줄였고,  
활동 통계를 효율적으로 시각화할 수 있게 되었습니다.

---

## 6. Code Samples

---

### 6-1. 추천 기록 그룹 저장 로직 (ProblemRecordGroup + ProblemRecord)

**의도**  
추천 요청 단위를 그룹으로 저장하고, 추천된 문제를 레코드로 분리하여  
추천 이력을 복원 가능한 구조로 만들었습니다.

```java
// 추천 요청 처리 흐름 일부

ProblemRecordGroup group = ProblemRecordGroup.builder()
        .member(member)
        .keyword(keyword)
        .subject(subject)
        .questionType(type)
        .build();

problemRecordGroupRepository.save(group);

for (Problems problem : problems) {
    ProblemRecord record = ProblemRecord.builder()
            .group(group)
            .problem(problem)
            .build();

    problemRecordRepository.save(record);
}
```

> 추천 요청 단위를 데이터로 남기는 구조가 이 프로젝트의 핵심 설계 포인트입니다.

---

### 6-2. 조건 기반 추천 로직 (유형 + 키워드 필터)

**의도**  
사용자 조건을 반영한 추천을 위해 유형과 키워드를 결합한 쿼리를 설계했습니다.

```java
@Query(value = """
    SELECT * FROM problems
    WHERE question_type = :type
    AND keywords LIKE %:keyword%
    ORDER BY RAND()
    LIMIT :limit
""", nativeQuery = true)
List<Problems> findRandomByQuestionTypeAndKeywords(
        @Param("type") int type,
        @Param("keyword") String keyword,
        @Param("limit") int limit
);
```

> 단순 랜덤이 아니라 “조건 기반 랜덤” 구조를 구현했습니다.

---

### 6-3. 마이페이지 통계 집계 로직

**의도**  
활동 데이터를 join 없이 count 기반으로 집계해 조회 부담을 줄였습니다.

```java
public UserStatsDTO getUserStats(Long userId) {
    long problemCount = problemRecordRepository.countByUserId(userId);
    long postCount = articlesRepository.countByUser_Id(userId);
    long commentCount = commentsRepository.countByUser_Id(userId);
    long likeCount = likesRepository.countByUser_Id(userId);

    return UserStatsDTO.builder()
            .problemCount(problemCount)
            .postCount(postCount)
            .commentCount(commentCount)
            .likeCount(likeCount)
            .build();
}
```

> 기능 확장 시에도 성능 영향을 최소화하도록 집계 구조를 설계했습니다.

---

## 7. API Specification (요약)

### 7-1. 인증 / 회원

| Method | URL | Description |
|--------|-----|-------------|
| GET | /login | 로그인 페이지 |
| POST | /login | 로그인 처리 |
| GET | /register | 회원가입 페이지 |
| POST | /register | 회원가입 처리 |
| POST | /logout | 로그아웃 |

---

### 7-2. 문제 추천

| Method | URL | Description |
|--------|-----|-------------|
| GET | /exam | 추천 페이지 조회 |
| POST | /exam | 조건 기반 문제 추천 |

---

### 7-3. 추천 기록 관리

| Method | URL | Description |
|--------|-----|-------------|
| GET | /exam/view/{groupId} | 추천 기록 조회 |
| GET | /exam/delete/{groupId} | 추천 기록 삭제 |
| GET | /exam/to-post/{groupId} | 추천 결과 게시글 변환 |

---

### 7-3. 게시판

| Method | URL | Description |
|--------|-----|-------------|
| GET | /posts | 게시글 목록 조회 |
| GET | /posts/{articleId} | 게시글 상세 조회 |
| POST | /posts/new | 게시글 작성 |
| POST | /posts/{articleId}/edit | 게시글 수정 |
| POST | /posts/{articleId}/delete | 게시글 삭제 |

---

### 7-4. 댓글

| Method | URL | Description |
|--------|-----|-------------|
| POST | /posts/{articleId}/comments | 댓글 작성 |
| POST | /posts/{articleId}/comments/{commentId}/edit | 댓글 수정 |
| POST | /posts/{articleId}/comments/{commentId}/delete | 댓글 삭제 |

---

### 7-5. 좋아요

| Method | URL | Description |
|--------|-----|-------------|
| POST | /posts/{articleId}/like | 좋아요 토글 |

---

### 7-6. 마이페이지

| Method | URL | Description |
|--------|-----|-------------|
| GET | /mypage | 마이페이지 조회 |
| POST | /mypage | 프로필 수정 |
| POST | /mypage/password | 비밀번호 변경 |

---

## 8. 기여도

### 8-1. 역할
- 팀장
- 서비스 기획 및 전체 기능 흐름 설계
- 추천 / 커뮤니티 / 마이페이지 백엔드 구현 담당

### 8-2. 기획
- 추천 → 기록 → 게시글 확장 → 커뮤니티 토론으로 이어지는 전체 서비스 흐름 설계
- 추천 요청 단위를 데이터로 남기는 구조 기획
- 활동 통계 기반 마이페이지 구조 정의
- 기능 우선순위 및 도메인 분리 설계

### 8-3. 구현 담당 영역
- 추천 도메인 전체 설계 및 구현
- 추천 기록 그룹/레코드 구조 설계
- 조건 기반 추천 로직 구현
- 추천 결과 게시글 자동 확장 흐름 구현
- 게시글/댓글/좋아요 CRUD 로직 구현
- 게시글 삭제 정책 설계(선삭제)
- 마이페이지 통계 집계 로직 구현
- Thymeleaf 기반 화면 흐름 처리
  - 추천 입력 → 결과 → history 한 페이지 구성
  - 추천 결과를 게시글 작성 폼으로 자동 주입

### 8-4. 제외 영역
- 로그인/회원가입 Security 설정 및 인증 처리 부분은 담당하지 않음

---

## 9. 회고

이 프로젝트를 통해  
“기능을 만드는 것”과 “데이터 구조를 설계하는 것”은 완전히 다르다는 걸 배웠습니다.

특히 추천 요청을 단위 데이터로 설계한 것이  
이 프로젝트의 가장 큰 설계적 수확이었습니다.

단순히 기능을 구현하는 수준을 넘어,  
데이터가 축적되고 확장될 수 있는 구조를 고민한 경험이  
백엔드 설계 관점을 한 단계 끌어올려 주었습니다.
