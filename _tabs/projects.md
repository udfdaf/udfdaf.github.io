---
title: Projects
icon: fas fa-folder
order: 2
---

# 📁 Projects

<style>
/* ====== Section ====== */
.projects-section{
  margin-top: 18px;
}

/* ====== Grid ====== */
.project-grid{
  display:grid;
  grid-template-columns:repeat(3, minmax(0, 1fr));
  gap:20px;
  margin-top: 14px;
}

/* ====== Card ====== */
.project-card{
  position:relative;
  border-radius:18px;
  overflow:hidden;
  background:#fff;
  box-shadow:0 10px 26px rgba(0,0,0,0.08);
  transition: transform .18s ease, box-shadow .18s ease;
  will-change: transform;
}

.project-card:hover{
  transform: translateY(-4px);
  box-shadow:0 16px 40px rgba(0,0,0,0.12);
}

.project-card:active{
  transform: translateY(-1px) scale(0.985);
  box-shadow:0 12px 30px rgba(0,0,0,0.10);
}

.project-link{
  position:absolute;
  inset:0;
  z-index:5;
  border-radius:18px;
  outline:none;
}

.project-link:focus-visible{
  box-shadow: 0 0 0 4px rgba(55, 125, 255, 0.35);
}

/* ====== Thumbnail ====== */
.project-thumb{
  position:relative;
  height:170px;
  overflow:hidden;
}

.project-thumb img{
  width:100%;
  height:100%;
  object-fit:cover;
  display:block;
  transform: scale(1.02);
  transition: transform .35s ease;
}

.project-card:hover .project-thumb img{
  transform: scale(1.08);
}

.project-thumb::after{
  content:"";
  position:absolute;
  inset:0;
  pointer-events:none;
  background:
    radial-gradient(900px 260px at 15% 0%, rgba(255,255,255,0.30), transparent 55%),
    linear-gradient(to bottom, rgba(0,0,0,0.00) 40%, rgba(0,0,0,0.12) 100%);
}

/* ====== Body ====== */
.project-body{
  padding:14px 16px 16px 16px;
  display:flex;
  flex-direction:column;
  gap:8px;
}

.project-title{
  margin:0;
  font-size:1.02rem;
  letter-spacing:-0.2px;
  display:flex;
  align-items:center;
  gap:8px;
}

.project-desc{
  margin:0;
  font-size:0.90rem;
  color:#666;
  line-height:1.45;
}

.project-tag{
  display:inline-flex;
  align-items:center;
  gap:6px;
  font-size:0.78rem;
  padding:6px 10px;
  border-radius:999px;
  border:1px solid rgba(0,0,0,0.10);
  color:#444;
  background: rgba(0,0,0,0.02);
  width: fit-content;
}

/* ====== Responsive ====== */
@media (max-width:1024px){
  .project-grid{ grid-template-columns:repeat(2, minmax(0, 1fr)); }
}
@media (max-width:768px){
  .project-grid{ grid-template-columns:1fr; }
  .project-thumb{ height:190px; }
}
</style>

<div class="projects-section">

## 🚀 Main Projects

<div class="project-grid">

  <!-- YNNECT -->
  <div class="project-card">
    <a class="project-link" href="/projects/ynnect/" aria-label="YNNECT 프로젝트로 이동"></a>
    <div class="project-thumb">
      <img src="/assets/img/projects/ynnect/ynnect-thumb.png" alt="YNNECT thumbnail">
    </div>
    <div class="project-body">
      <div class="project-tag">Backend · Spring Boot</div>
      <h3 class="project-title">🔹 YNNECT</h3>
      <p class="project-desc">위치 · 상태 · 시간표를 공유하는 캠퍼스 기반 소셜 플랫폼</p>
    </div>
  </div>

  <!-- Robot Monitoring -->
  <div class="project-card">
    <a class="project-link" href="/projects/robot/" aria-label="Robot Monitoring System 프로젝트로 이동"></a>
    <div class="project-thumb">
      <img src="/assets/img/projects/robot/robot-thumb.png" alt="Robot Monitoring thumbnail">
    </div>
    <div class="project-body">
      <div class="project-tag">Backend · NestJS</div>
      <h3 class="project-title">🔹 Robot Monitoring System</h3>
      <p class="project-desc">로봇 실시간 관제 및 Telemetry 수집 시스템</p>
    </div>
  </div>

  <!-- AplusMaker -->
  <div class="project-card">
    <a class="project-link" href="/projects/aplusmaker/" aria-label="AplusMaker 프로젝트로 이동"></a>
    <div class="project-thumb">
      <img src="/assets/img/projects/aplusmaker/aplusmaker-thumb.png" alt="AplusMaker thumbnail">
    </div>
    <div class="project-body">
      <div class="project-tag">Backend · Spring</div>
      <h3 class="project-title">🔹 AplusMaker</h3>
      <p class="project-desc">조건 기반 문제 추천 + 커뮤니티를 결합한 학습 플랫폼</p>
    </div>
  </div>

</div>
</div>

<div class="projects-section">

## 🧪 Sub Projects

<div class="project-grid">

  <!-- Robotics / Algorithms -->
  <div class="project-card">
    <a class="project-link" href="/projects/robotics/" aria-label="Robotics Algorithms 프로젝트로 이동"></a>
    <div class="project-thumb">
      <img src="/assets/img/projects/robotics/robotics-thumb.jpg" alt="Robotics thumbnail">
    </div>
    <div class="project-body">
      <div class="project-tag">Robotics · Algorithms</div>
      <h3 class="project-title">🧪 Robotics Algorithms (HW)</h3>
      <p class="project-desc">Bug · Kinematics 등 로보틱스 알고리즘 과제 구현/정리</p>
    </div>
  </div>

</div>
</div>
