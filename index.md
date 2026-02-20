---
layout: page
title: "Backend Developer 김태훈"
toc: false
---

# 👋 Backend Developer 김태훈
**빠르게 배우고, 복잡한 요구를 구조로 정리해 구현하는 백엔드 개발자**입니다.

---

## 🚀 Featured Projects

<style>
/* ===== Layout ===== */
.project-grid{
  display:grid;
  grid-template-columns:repeat(3,1fr);
  gap:20px;
  margin-top:20px;
}

/* ===== Card ===== */
.project-card{
  position:relative;            /* ✅ a 태그가 카드 전체를 덮기 위해 필요 */
  border-radius:16px;
  overflow:hidden;
  background:#fff;
  box-shadow:0 8px 24px rgba(0,0,0,0.08);
  transition:transform .18s ease, box-shadow .18s ease;
  will-change: transform;
}

/* 카드 hover(고급스럽게 살짝 뜨는 느낌) */
.project-card:hover{
  transform:translateY(-4px);
  box-shadow:0 14px 34px rgba(0,0,0,0.12);
}

/* ✅ 카드 전체 클릭 영역 */
.project-link{
  position:absolute;
  inset:0;
  z-index:3;
  border-radius:16px;
  /* 접근성: 포커스 표시를 위해 기본 outline은 유지하고 싶으면 아래 제거 */
  outline:none;
}

/* 키보드 포커스(접근성) */
.project-link:focus-visible{
  box-shadow:0 0 0 4px rgba(55, 125, 255, 0.35);
}

/* ✅ 눌림 효과: 클릭 순간 카드가 살짝 눌림 */
.project-card:active{
  transform:translateY(-1px) scale(0.985);
  box-shadow:0 10px 26px rgba(0,0,0,0.10);
}

/* ===== Content ===== */
.project-content{
  position:relative;
  z-index:2; /* 링크(a)는 z=3이라 클릭은 위에서 처리, 콘텐츠는 아래에서 보여줌 */
  display:flex;
  flex-direction:column;
}

.project-thumb-wrap{
  position:relative;
  height:170px;
  overflow:hidden;
}

/* 이미지 */
.project-thumb{
  width:100%;
  height:100%;
  object-fit:cover;
  display:block;
  transform:scale(1.02);
  transition:transform .35s ease;
}

/* hover 시 이미지 살짝 확대 */
.project-card:hover .project-thumb{
  transform:scale(1.07);
}

/* 고급스러운 오버레이(그라데이션 + 미세한 하이라이트) */
.project-thumb-wrap::after{
  content:"";
  position:absolute;
  inset:0;
  background:
    radial-gradient(800px 220px at 10% 0%, rgba(255,255,255,0.28), transparent 55%),
    linear-gradient(to bottom, rgba(0,0,0,0.00) 40%, rgba(0,0,0,0.10) 100%);
  pointer-events:none;
}

/* 본문 영역 */
.project-body{
  padding:14px 16px 16px 16px;
  display:flex;
  flex-direction:column;
  gap:8px;
}

/* 제목 */
.project-title{
  margin:0;
  font-size:1.05rem;
  letter-spacing:-0.2px;
}

/* 설명 */
.project-desc{
  margin:0;
  font-size:0.9rem;
  color:#666;
  line-height:1.45;
}

/* 상단 얇은 라인 느낌 */
.project-divider{
  height:1px;
  background:rgba(0,0,0,0.06);
  margin:0 16px;
}

/* ===== More button ===== */
.more-wrap{
  margin-top:22px;
  display:flex;
  justify-content:center;
}

.more-btn{
  border:1px solid rgba(0,0,0,0.14);
  background:#fff;
  border-radius:12px;
  padding:10px 16px;
  font-size:0.9rem;
  cursor:pointer;
  transition:transform .15s ease, box-shadow .15s ease;
}

.more-btn:hover{
  transform:translateY(-1px);
  box-shadow:0 10px 22px rgba(0,0,0,0.08);
}

.more-btn:active{
  transform:translateY(0px) scale(0.98);
}

.more-projects{
  display:none;
  margin-top:18px;
}

.more-projects.is-open{
  display:block;
}

/* 빈 상태 문구 */
.empty-box{
  text-align:center;
  padding:40px 0;
  color:#888;
  font-size:0.9rem;
  border:1px dashed rgba(0,0,0,0.15);
  border-radius:14px;
  background:rgba(0,0,0,0.015);
}

/* ===== Responsive ===== */
@media (max-width:1024px){
  .project-grid{ grid-template-columns:repeat(2,1fr); }
}
@media (max-width:768px){
  .project-grid{ grid-template-columns:1fr; }
  .project-thumb-wrap{ height:190px; }
}
</style>

<div class="project-grid">

  <!-- YNNECT -->
  <div class="project-card">
    <a class="project-link" href="/projects/ynnect/" aria-label="YNNECT 프로젝트로 이동"></a>

    <div class="project-content">
      <div class="project-thumb-wrap">
        <img class="project-thumb" src="/assets/img/projects/ynnect/ynnect-thumb.png" alt="YNNECT thumbnail">
      </div>

      <div class="project-body">
        <h3 class="project-title">🔹 YNNECT</h3>
        <p class="project-desc">위치 · 상태 · 시간표를 공유하는 캠퍼스 기반 소셜 플랫폼</p>
      </div>

      <div class="project-divider"></div>
    </div>
  </div>

  <!-- AplusMaker -->
  <div class="project-card">
    <a class="project-link" href="/projects/aplusmaker/" aria-label="AplusMaker 프로젝트로 이동"></a>

    <div class="project-content">
      <div class="project-thumb-wrap">
        <img class="project-thumb" src="/assets/img/projects/aplusmaker/aplusmaker-thumb.png" alt="AplusMaker thumbnail">
      </div>

      <div class="project-body">
        <h3 class="project-title">🔹 AplusMaker</h3>
        <p class="project-desc">조건 기반 문제 추천 + 커뮤니티를 결합한 학습 플랫폼</p>
      </div>

      <div class="project-divider"></div>
    </div>
  </div>

  <!-- Robot Monitoring System -->
  <div class="project-card">
    <a class="project-link" href="/projects/robot/" aria-label="Robot Monitoring System 프로젝트로 이동"></a>

    <div class="project-content">
      <div class="project-thumb-wrap">
        <img class="project-thumb" src="/assets/img/projects/robot/robot-thumb.png" alt="Robot Monitoring thumbnail">
      </div>

      <div class="project-body">
        <h3 class="project-title">🔹 Robot Monitoring System</h3>
        <p class="project-desc">로봇 실시간 관제 및 Telemetry 수집 시스템</p>
      </div>

      <div class="project-divider"></div>
    </div>
  </div>

</div>

<div class="more-wrap">
  <button class="more-btn" id="toggleMore" type="button">+ More Projects</button>
</div>

<div class="more-projects" id="moreProjects">
  <div class="project-grid">

    <!-- Robotics / Algorithms -->
    <div class="project-card">
      <a class="project-link" href="/projects/robotics/" aria-label="Robotics Algorithms 프로젝트로 이동"></a>

      <div class="project-content">
        <div class="project-thumb-wrap">
          <img class="project-thumb" src="/assets/img/projects/robotics/robotics-thumb.png" alt="Robotics thumbnail">
        </div>

        <div class="project-body">
          <div class="project-tag">Robotics · Algorithms</div>
          <h3 class="project-title">🧪 Robotics Algorithms (HW)</h3>
          <p class="project-desc">Bug · Kinematics 등 로보틱스 알고리즘 과제 구현/정리</p>
        </div>

        <div class="project-divider"></div>
      </div>
    </div>

  </div>

</div>

<script>
(function () {
  var btn = document.getElementById('toggleMore');
  var box = document.getElementById('moreProjects');
  if (!btn || !box) return;

  btn.addEventListener('click', function () {
    var open = box.classList.toggle('is-open');
    btn.textContent = open ? '− Close' : '+ More Projects';
  });
})();
</script>

---

## 📞 Contact
- GitHub: https://github.com/udfdaf  
- Email: 00kimhun@naver.com
