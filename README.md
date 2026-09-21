# Ninja-Game_PancasilaFXWELLY
<!DOCTYPE html>
<html lang="id">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1, user-scalable=no, viewport-fit=cover">
<meta name="theme-color" content="#141a3d">
<meta name="apple-mobile-web-app-capable" content="yes">
<meta name="mobile-web-app-capable" content="yes">
<title>🥷 Ninja Pancasila — Petualangan Lompat</title>
<style>
  *{margin:0;padding:0;box-sizing:border-box;-webkit-tap-highlight-color:transparent;}
  html,body{width:100%;height:100%;overflow:hidden;background:#0d1020;
    font-family:-apple-system,BlinkMacSystemFont,'Segoe UI',system-ui,sans-serif;
    user-select:none;-webkit-user-select:none;touch-action:none;}
  canvas{display:block;position:absolute;inset:0;}

  /* ---------- HUD ---------- */
  #hud{position:absolute;top:0;left:0;right:0;padding:14px 16px;
    padding-top:max(14px,env(safe-area-inset-top));
    display:flex;justify-content:space-between;align-items:flex-start;
    pointer-events:none;z-index:5;}
  .hud-box{background:rgba(10,14,30,.6);border:1px solid rgba(255,207,107,.4);
    border-radius:11px;padding:8px 13px;color:#ffe9b8;font-weight:700;
    font-size:14px;backdrop-filter:blur(6px);-webkit-backdrop-filter:blur(6px);
    text-shadow:0 2px 6px #000;white-space:nowrap;}
  #lives{font-size:17px;letter-spacing:1.5px;}
  #hudRight{display:flex;flex-direction:column;gap:7px;align-items:flex-end;}

  /* ---------- Modal ---------- */
  #modal{position:absolute;inset:0;display:flex;align-items:center;justify-content:center;
    background:rgba(4,7,18,.82);backdrop-filter:blur(6px);-webkit-backdrop-filter:blur(6px);
    z-index:20;padding:14px;animation:fadeIn .22s ease;}
  #modal.hidden{display:none;}
  @keyframes fadeIn{from{opacity:0}to{opacity:1}}
  @keyframes pop{from{transform:scale(.92);opacity:0}to{transform:scale(1);opacity:1}}

  .card{background:linear-gradient(160deg,#1d2440,#2b3460);
    border:2px solid #ffcf6b;border-radius:18px;padding:20px 20px;
    max-width:520px;width:100%;
    box-shadow:0 24px 70px rgba(0,0,0,.75),inset 0 0 0 1px rgba(255,207,107,.14);
    animation:pop .28s cubic-bezier(.2,1.3,.5,1);
    max-height:88vh;overflow-y:auto;-webkit-overflow-scrolling:touch;}

  .qnum{display:inline-block;background:#ffcf6b;color:#241a05;font-weight:800;
    font-size:11px;letter-spacing:1px;padding:5px 12px;border-radius:20px;
    margin-bottom:12px;}
  .qtext{color:#fff;font-size:17px;font-weight:700;line-height:1.5;
    margin-bottom:16px;}

  .opt{display:block;width:100%;text-align:left;
    background:rgba(255,255,255,.07);
    border:1.5px solid rgba(255,255,255,.18);color:#e7ecff;
    font-size:14.5px;font-weight:600;padding:12px 14px;border-radius:11px;
    margin-bottom:9px;cursor:pointer;transition:.15s;font-family:inherit;
    line-height:1.4;}
  .opt:active:not(:disabled){background:rgba(255,207,107,.2);border-color:#ffcf6b;}
  .opt:disabled{cursor:default;opacity:.9;}
  .opt.correct{background:rgba(64,210,120,.3);border-color:#40d278;color:#c9ffe0;}
  .opt.wrong{background:rgba(230,70,70,.3);border-color:#e64646;color:#ffd0d0;}

  .feedback{margin-top:6px;font-size:13px;line-height:1.55;font-weight:600;
    padding:11px 13px;border-radius:11px;display:none;}
  .feedback.ok{display:block;background:rgba(64,210,120,.15);color:#9df0bd;
    border-left:4px solid #40d278;}
  .feedback.bad{display:block;background:rgba(230,70,70,.15);color:#ffb3b3;
    border-left:4px solid #e64646;}

  .end{text-align:center;color:#fff;}
  .end h1{font-size:28px;margin-bottom:8px;}
  .end p{font-size:14px;color:#c8d2f0;line-height:1.6;margin-bottom:10px;}
  .end .bigscore{font-size:18px;color:#ffcf6b;font-weight:800;margin:14px 0;
    line-height:1.5;}
  .end .hist{font-size:12px;color:#9aa5c8;margin-top:12px;padding-top:12px;
    border-top:1px solid rgba(255,255,255,.1);text-align:left;line-height:1.7;}
  .end .hist b{color:#ffcf6b;}
  .btn-restart{margin-top:14px;
    background:linear-gradient(160deg,#ffcf6b,#f0a93a);
    color:#241a05;font-weight:800;font-size:16px;border:none;
    padding:13px 34px;border-radius:12px;cursor:pointer;font-family:inherit;
    box-shadow:0 8px 22px rgba(255,180,60,.35);transition:.15s;}
  .btn-restart:active{transform:translateY(1px);}

  /* ---------- Touch controls ---------- */
  #touch{position:absolute;bottom:0;left:0;right:0;display:none;
    justify-content:space-between;padding:0 18px 20px;
    padding-bottom:max(20px,env(safe-area-inset-bottom));
    z-index:10;pointer-events:none;}
  #touch .side{display:flex;gap:12px;pointer-events:auto;}
  .tbtn{width:64px;height:64px;border-radius:50%;
    background:rgba(255,255,255,.13);
    border:2px solid rgba(255,207,107,.5);
    color:#ffe9b8;font-size:24px;font-weight:800;
    display:flex;align-items:center;justify-content:center;
    backdrop-filter:blur(6px);-webkit-backdrop-filter:blur(6px);
    font-family:inherit;transition:.1s;}
  .tbtn:active{background:rgba(255,207,107,.4);transform:scale(.95);}
  @media (hover:none) and (pointer:coarse){#touch{display:flex;}}

  /* ---------- Loading ---------- */
  #loading{position:absolute;inset:0;display:flex;align-items:center;
    justify-content:center;background:#141a3d;color:#ffcf6b;font-weight:700;
    font-size:18px;z-index:50;text-align:center;padding:20px;}
  #loading.hidden{display:none;}
</style>
</head>
<body>

<div id="loading">🥷 Memuat Petualangan Ninja Pancasila...</div>

<div id="hud">
  <div class="hud-box" id="lives">❤️❤️❤️</div>
  <div id="hudRight">
    <div class="hud-box" id="score">Skor: 0</div>
    <div class="hud-box" id="progress">Soal: 0/10</div>
  </div>
</div>

<div id="touch">
  <div class="side">
    <button class="tbtn" id="tLeft" aria-label="Kiri">◀</button>
    <button class="tbtn" id="tRight" aria-label="Kanan">▶</button>
  </div>
  <div class="side">
    <button class="tbtn" id="tJump" aria-label="Lompat">⤒</button>
  </div>
</div>

<div id="modal" class="hidden">
  <div class="card" id="card"></div>
</div>

<script src="https://cdn.jsdelivr.net/npm/three@0.128.0/build/three.min.js"></script>
<script>
/* =========================================================================
   🥷 NINJA PANCASILA — WebGL (Three.js) + JSON Storage
   ========================================================================= */

/* -------------------------------------------------------------------------
   1) SOAL PANCASILA — 10 Soal Tingkat SMP
   ------------------------------------------------------------------------- */
const QUESTIONS = [
  { q:"Bunyi sila pertama Pancasila adalah...",
    options:["Ketuhanan Yang Maha Esa",
             "Kemanusiaan yang adil dan beradab",
             "Persatuan Indonesia",
             "Keadilan sosial bagi seluruh rakyat Indonesia"],
    answer:0,
    explain:"Sila ke-1 menegaskan pengakuan bangsa Indonesia terhadap Tuhan Yang Maha Esa." },

  { q:"Lambang sila kedua Pancasila adalah...",
    options:["Bintang","Rantai","Pohon Beringin","Padi dan Kapas"],
    answer:1,
    explain:"Rantai melambangkan ikatan manusia yang saling membutuhkan satu sama lain." },

  { q:"Sila ketiga \"Persatuan Indonesia\" dilambangkan dengan...",
    options:["Kepala Banteng","Pohon Beringin","Rantai","Bintang"],
    answer:1,
    explain:"Pohon Beringin menggambarkan tempat berteduh dan bersatunya seluruh rakyat Indonesia." },

  { q:"Musyawarah untuk mencapai mufakat merupakan pengamalan sila ke...",
    options:["Pertama","Kedua","Ketiga","Keempat"],
    answer:3,
    explain:"Sila ke-4: Kerakyatan yang dipimpin oleh hikmat kebijaksanaan dalam permusyawaratan/perwakilan." },

  { q:"Pancasila disahkan sebagai dasar negara pada tanggal...",
    options:["1 Juni 1945","17 Agustus 1945","18 Agustus 1945","1 Oktober 1965"],
    answer:2,
    explain:"Pancasila disahkan bersama UUD 1945 pada 18 Agustus 1945 oleh PPKI." },

  { q:"Tokoh yang pertama kali mengusulkan nama \"Pancasila\" adalah...",
    options:["Moh. Yamin","Ir. Soekarno","Mr. Soepomo","Drs. Moh. Hatta"],
    answer:1,
    explain:"Ir. Soekarno menyampaikan usulan nama Pancasila pada sidang BPUPKI 1 Juni 1945." },

  { q:"Arti semboyan \"Bhinneka Tunggal Ika\" adalah...",
    options:["Berbeda-beda tetapi tetap satu",
             "Bersatu kita teguh, bercerai kita runtuh",
             "Satu untuk semua, semua untuk satu",
             "Bekerja sama tanpa pamrih"],
    answer:0,
    explain:"Bhinneka Tunggal Ika berarti berbeda-beda tetapi tetap satu jua." },

  { q:"Lambang sila kelima Pancasila adalah...",
    options:["Kepala Banteng","Pohon Beringin","Padi dan Kapas","Bintang"],
    answer:2,
    explain:"Padi dan Kapas melambangkan kemakmuran serta keadilan sosial bagi seluruh rakyat." },

  { q:"Sikap yang mencerminkan sila pertama Pancasila adalah...",
    options:["Rajin beribadah sesuai agamanya",
             "Membeli produk dalam negeri",
             "Mengikuti pemilihan ketua kelas",
             "Menolong tetangga yang kesulitan"],
    answer:0,
    explain:"Sila ke-1 berkaitan dengan kehidupan beragama dan ketakwaan kepada Tuhan." },

  { q:"Membantu korban bencana tanpa membedakan suku, agama, dan ras merupakan pengamalan sila ke...",
    options:["Pertama","Kedua","Ketiga","Kelima"],
    answer:1,
    explain:"Sila ke-2: Kemanusiaan yang adil dan beradab — menolong sesama tanpa diskriminasi." }
];

/* -------------------------------------------------------------------------
   2) JSON STORAGE — localStorage
   ------------------------------------------------------------------------- */
const STORAGE_KEY = 'ninja_pancasila_save_v1';

function loadSave(){
  try{
    const raw = localStorage.getItem(STORAGE_KEY);
    if(!raw) return { highScore:0, bestCorrect:0, plays:0, history:[] };
    const obj = JSON.parse(raw);
    return Object.assign({ highScore:0, bestCorrect:0, plays:0, history:[] }, obj);
  }catch(e){
    return { highScore:0, bestCorrect:0, plays:0, history:[] };
  }
}

function writeSave(data){
  try{ localStorage.setItem(STORAGE_KEY, JSON.stringify(data)); }catch(e){}
}

function recordGame(score, correct){
  const save = loadSave();
  save.plays = (save.plays||0) + 1;
  save.highScore  = Math.max(save.highScore||0, score);
  save.bestCorrect= Math.max(save.bestCorrect||0, correct);
  save.lastScore  = score;
  save.lastCorrect= correct;
  save.lastPlayed = new Date().toISOString();
  save.history = (save.history||[]).slice(-4);
  save.history.push({ score, correct, at: save.lastPlayed });
  writeSave(save);
  return save;
}

/* -------------------------------------------------------------------------
   3) THREE.JS SETUP
   ------------------------------------------------------------------------- */
const VIRTUAL_H = 800;          // tinggi virtual dunia
let VIRTUAL_W = 400;
let W, H;

let scene, camera, renderer, camX = 0;

// Kontainer layer parallax
const layers = { sky:null, stars:null, sun:null, moon:null,
                 mountainFar:null, mountainMid:null, mountainNear:null,
                 flyers:null, world:null };

// -------------------------------------------------------------------------
function initThree(){
  W = window.innerWidth;
  H = window.innerHeight;
  VIRTUAL_W = VIRTUAL_H * (W/H);

  scene = new THREE.Scene();

  const halfW = VIRTUAL_W/2, halfH = VIRTUAL_H/2;
  camera = new THREE.OrthographicCamera(-halfW, halfW, halfH, -halfH, 0.1, 2000);
  camera.position.set(0, 0, 100);

  renderer = new THREE.WebGLRenderer({
    antialias:true, alpha:false, powerPreference:'high-performance'
  });
  renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));
  renderer.setSize(W, H);
  renderer.setClearColor(0x141a3d, 1);
  document.body.appendChild(renderer.domElement);
}

function onResize(){
  W = window.innerWidth;
  H = window.innerHeight;
  VIRTUAL_W = VIRTUAL_H * (W/H);
  camera.left   = -VIRTUAL_W/2;
  camera.right  =  VIRTUAL_W/2;
  camera.top    =  VIRTUAL_H/2;
  camera.bottom = -VIRTUAL_H/2;
  camera.updateProjectionMatrix();
  renderer.setSize(W, H);
  renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));

  // reposisi layer parallax di frame berikutnya
  layoutParallax();
}

/* -------------------------------------------------------------------------
   4) TEKSTUR CANVAS (glow, langit, angka, ikon)
   ------------------------------------------------------------------------- */
function makeCanvasTexture(drawFn, size=128){
  const c = document.createElement('canvas');
  c.width = c.height = size;
  const ctx = c.getContext('2d');
  drawFn(ctx, size);
  const t = new THREE.CanvasTexture(c);
  t.minFilter = THREE.LinearFilter;
  return t;
}

function skyTexture(){
  const c = document.createElement('canvas');
  c.width = 32; c.height = 512;
  const ctx = c.getContext('2d');
  const g = ctx.createLinearGradient(0,0,0,512);
  g.addColorStop(0.00,'#0c1030');
  g.addColorStop(0.22,'#1e2450');
  g.addColorStop(0.42,'#4a3f78');
  g.addColorStop(0.62,'#8f5a78');
  g.addColorStop(0.78,'#cf6f5a');
  g.addColorStop(0.90,'#ea9052');
  g.addColorStop(1.00,'#f6bb7e');
  ctx.fillStyle = g;
  ctx.fillRect(0,0,32,512);
  const t = new THREE.CanvasTexture(c);
  t.minFilter = THREE.LinearFilter;
  return t;
}

function glowTexture(inner, mid, outer){
  return makeCanvasTexture((ctx,s)=>{
    const g = ctx.createRadialGradient(s/2,s/2,0,s/2,s/2,s/2);
    g.addColorStop(0, inner);
    g.addColorStop(0.45, mid);
    g.addColorStop(1, outer);
    ctx.fillStyle = g;
    ctx.fillRect(0,0,s,s);
  }, 256);
}

function numberBadgeTexture(n){
  return makeCanvasTexture((ctx,s)=>{
    ctx.clearRect(0,0,s,s);
    // lingkaran emas
    ctx.beginPath();
    ctx.arc(s/2, s/2, s*0.42, 0, Math.PI*2);
    ctx.fillStyle = '#ffd76b';
    ctx.fill();
    ctx.lineWidth = s*0.06;
    ctx.strokeStyle = '#8a5c00';
    ctx.stroke();
    // angka
    ctx.fillStyle = '#3b2a00';
    ctx.font = `bold ${s*0.55}px "Segoe UI", sans-serif`;
    ctx.textAlign = 'center';
    ctx.textBaseline = 'middle';
    ctx.fillText(String(n), s/2, s/2 + s*0.03);
  }, 128);
}

function checkBadgeTexture(ok){
  return makeCanvasTexture((ctx,s)=>{
    ctx.clearRect(0,0,s,s);
    ctx.beginPath();
    ctx.arc(s/2, s/2, s*0.42, 0, Math.PI*2);
    ctx.fillStyle = ok ? 'rgba(64,210,120,.92)' : 'rgba(230,70,70,.92)';
    ctx.fill();
    ctx.lineWidth = s*0.05;
    ctx.strokeStyle = ok ? '#1d5c39' : '#5e2222';
    ctx.stroke();
    ctx.strokeStyle = '#ffffff';
    ctx.lineWidth = s*0.09;
    ctx.lineCap = 'round';
    ctx.beginPath();
    if(ok){
      ctx.moveTo(s*0.30, s*0.52);
      ctx.lineTo(s*0.44, s*0.66);
      ctx.lineTo(s*0.72, s*0.36);
    }else{
      ctx.moveTo(s*0.34, s*0.34);
      ctx.lineTo(s*0.66, s*0.66);
      ctx.moveTo(s*0.66, s*0.34);
      ctx.lineTo(s*0.34, s*0.66);
    }
    ctx.stroke();
  }, 128);
}

/* -------------------------------------------------------------------------
   5) MEMBUAT DUNIA
   ------------------------------------------------------------------------- */
let groundY = -VIRTUAL_H/2 + 70;   // Y permukaan "tanah" (dalam koordinat dunia)

function buildSky(){
  const g = new THREE.Group();
  const mat = new THREE.MeshBasicMaterial({ map: skyTexture(), depthWrite:false });
  const plane = new THREE.Mesh(new THREE.PlaneGeometry(VIRTUAL_W*3, VIRTUAL_H*1.3), mat);
  plane.position.set(0, VIRTUAL_H*0.15, -1000);
  g.add(plane);
  g.userData.parallax = 0;
  g.userData.wide = true;
  g.userData.baseW = VIRTUAL_W*3;
  layers.sky = g;
  scene.add(g);
}

function buildStars(){
  const g = new THREE.Group();
  const geo = new THREE.CircleGeometry(1.6, 6);
  const mat = new THREE.MeshBasicMaterial({ color:0xffffff, transparent:true, opacity:0.9 });
  const spread = VIRTUAL_W * 2.5;
  for(let i=0;i<80;i++){
    const m = new THREE.Mesh(geo, mat.clone());
    m.position.set(
      (Math.random()-0.5)*spread,
      VIRTUAL_H*0.05 + Math.random()*VIRTUAL_H*0.42,
      -900
    );
    m.material.opacity = 0.35 + Math.random()*0.6;
    m.userData.tw = Math.random()*Math.PI*2;
    m.userData.baseOp = m.material.opacity;
    g.add(m);
  }
  g.userData.parallax = 0.03;
  layers.stars = g;
  scene.add(g);
}

function buildSunMoon(){
  // MATAHARI
  const sunG = new THREE.Group();
  const glow = new THREE.Mesh(
    new THREE.PlaneGeometry(520, 520),
    new THREE.MeshBasicMaterial({
      map: glowTexture('rgba(255,220,140,.95)',
                       'rgba(255,170,80,.35)',
                       'rgba(255,140,60,0)'),
      transparent:true, depthWrite:false, blending:THREE.AdditiveBlending
    })
  );
  glow.position.z = -5;
  sunG.add(glow);
  const disk = new THREE.Mesh(
    new THREE.CircleGeometry(46, 32),
    new THREE.MeshBasicMaterial({ color:0xffeeaa })
  );
  sunG.add(disk);
  sunG.position.set(-VIRTUAL_W*0.20, VIRTUAL_H*0.24, -800);
  sunG.userData.parallax = 0.02;
  layers.sun = sunG;
  scene.add(sunG);

  // BULAN
  const moonG = new THREE.Group();
  const mglow = new THREE.Mesh(
    new THREE.PlaneGeometry(340, 340),
    new THREE.MeshBasicMaterial({
      map: glowTexture('rgba(220,230,255,.55)',
                       'rgba(180,200,255,.18)',
                       'rgba(140,160,220,0)'),
      transparent:true, depthWrite:false, blending:THREE.AdditiveBlending
    })
  );
  mglow.position.z = -5;
  moonG.add(mglow);
  const mdisk = new THREE.Mesh(
    new THREE.CircleGeometry(30, 32),
    new THREE.MeshBasicMaterial({ color:0xeef2ff })
  );
  moonG.add(mdisk);
  // kawah
  const craterMat = new THREE.MeshBasicMaterial({ color:0xc6cde8 });
  [[-8,6,4],[3,-8,3],[-3,-2,2.4]].forEach(([x,y,r])=>{
    const c = new THREE.Mesh(new THREE.CircleGeometry(r,10), craterMat);
    c.position.set(x,y,0.5);
    moonG.add(c);
  });
  // sisi gelap (fase sabit)
  const shade = new THREE.Mesh(
    new THREE.CircleGeometry(29, 32),
    new THREE.MeshBasicMaterial({ color:0x2b3460, transparent:true, opacity:0.92 })
  );
  shade.position.set(13,-6,1);
  moonG.add(shade);

  moonG.position.set(VIRTUAL_W*0.30, VIRTUAL_H*0.30, -800);
  moonG.userData.parallax = 0.02;
  layers.moon = moonG;
  scene.add(moonG);
}

function buildMountainLayer(color, peakH, parallax, seed, baseOffset){
  const g = new THREE.Group();
  const width = VIRTUAL_W * 3;
  const step  = 130;
  const baseY = groundY - 90;

  const shape = new THREE.Shape();
  shape.moveTo(-width/2, baseY - 300);

  for(let x=-width/2; x<=width/2; x+=step){
    const n1 = Math.sin(x*0.0018 + seed)*0.5 + 0.5;
    const n2 = Math.sin(x*0.0053 + seed*2.1)*0.5 + 0.5;
    const n3 = Math.sin(x*0.011  + seed*0.7)*0.5 + 0.5;
    const h = peakH * (0.28 + 0.42*n1 + 0.22*n2 + 0.10*n3);
    shape.lineTo(x, baseY + h + baseOffset);
  }
  shape.lineTo(width/2, baseY - 300);
  shape.closePath();

  const mesh = new THREE.Mesh(
    new THREE.ShapeGeometry(shape),
    new THREE.MeshBasicMaterial({ color, depthWrite:false })
  );
  g.add(mesh);
  g.userData.parallax = parallax;
  g.userData.baseW = width;
  return g;
}

function buildMountains(){
  layers.mountainFar  = buildMountainLayer(0x5b5fa0, 220, 0.10, 1.7, 0);
  layers.mountainMid  = buildMountainLayer(0x3a3f78, 260, 0.22, 4.3, -10);
  layers.mountainNear = buildMountainLayer(0x232752, 300, 0.38, 8.9, -20);

  // Kabut dasar
  const fog = new THREE.Mesh(
    new THREE.PlaneGeometry(VIRTUAL_W*3, 260),
    new THREE.MeshBasicMaterial({
      map: makeCanvasTexture((ctx,s)=>{
        const g=ctx.createLinearGradient(0,0,0,s);
        g.addColorStop(0,'rgba(255,190,130,0)');
        g.addColorStop(1,'rgba(255,190,130,0.18)');
        ctx.fillStyle=g; ctx.fillRect(0,0,s,s);
      },128),
      transparent:true, depthWrite:false
    })
  );
  fog.position.set(0, groundY - 40, -600);
  layers.mountainNear.add(fog);

  scene.add(layers.mountainFar);
  scene.add(layers.mountainMid);
  scene.add(layers.mountainNear);
}

/* -------------------------------------------------------------------------
   6) PLATFORM + BADGE SOAL
   ------------------------------------------------------------------------- */
const NUM_PLATFORMS = 10;
const PLAT_W = 100;
const PLAT_H = 16;
const SPACING = 190;
const START_X = 140;
const PLAT_OFFSETS = [170, 235, 165, 245, 195, 255, 205, 235, 180, 260];

const platforms = [];

function buildPlatforms(){
  layers.world = new THREE.Group();
  scene.add(layers.world);

  for(let i=0;i<NUM_PLATFORMS;i++){
    const x = START_X + i * SPACING;
    const topY = groundY + PLAT_OFFSETS[i];

    const grp = new THREE.Group();
    grp.position.set(x + PLAT_W/2, topY - PLAT_H/2 - 20, 0);

    // bayangan
    const shadow = new THREE.Mesh(
      new THREE.PlaneGeometry(PLAT_W+10, PLAT_H+40),
      new THREE.MeshBasicMaterial({ color:0x000000, transparent:true, opacity:0.24, depthWrite:false })
    );
    shadow.position.set(4,-6,-0.1);
    grp.add(shadow);

    // badan batu (gradient via 2 kotak)
    const bodyBot = new THREE.Mesh(
      new THREE.PlaneGeometry(PLAT_W, PLAT_H+30),
      new THREE.MeshBasicMaterial({ color:0x2c3055 })
    );
    bodyBot.position.y = -18;
    grp.add(bodyBot);

    const bodyTop = new THREE.Mesh(
      new THREE.PlaneGeometry(PLAT_W, PLAT_H),
      new THREE.MeshBasicMaterial({ color:0x5a5f8f })
    );
    bodyTop.position.y = 0;
    grp.add(bodyTop);

    // permukaan rumput/batu terang
    const grass = new THREE.Mesh(
      new THREE.PlaneGeometry(PLAT_W, 6),
      new THREE.MeshBasicMaterial({ color:0x9aa0e8 })
    );
    grass.position.set(0, PLAT_H/2 - 3, 0.15);
    grp.add(grass);

    // detail batu
    const detMat = new THREE.MeshBasicMaterial({ color:0x000000, transparent:true, opacity:0.2 });
    const d1 = new THREE.Mesh(new THREE.PlaneGeometry(22,4), detMat);
    d1.position.set(-20,-12,0.1);
    grp.add(d1);
    const d2 = new THREE.Mesh(new THREE.PlaneGeometry(30,4), detMat);
    d2.position.set(14,-22,0.1);
    grp.add(d2);

    // badge angka
    const badgeTex = numberBadgeTexture(i+1);
    const badge = new THREE.Mesh(
      new THREE.PlaneGeometry(50,50),
      new THREE.MeshBasicMaterial({ map: badgeTex, transparent:true, depthWrite:false })
    );
    badge.position.set(0, PLAT_H/2 + 42, 0.3);
    badge.userData.baseY = PLAT_H/2 + 42;
    grp.add(badge);

    // simpan referensi platform
    platforms.push({
      x, y: topY, w: PLAT_W, h: PLAT_H,
      index: i,
      state: 'idle',           // 'idle' | 'correct' | 'wrong'
      answered: false,
      group: grp,
      badge,
      bodyTop, bodyBot, grass
    });

    layers.world.add(grp);
  }
}

function updatePlatformVisual(pf){
  const topColor   = pf.state==='correct' ? 0x3ea86a :
                     pf.state==='wrong'   ? 0xb04a4a : 0x5a5f8f;
  const grassColor = pf.state==='correct' ? 0x7bf0a5 :
                     pf.state==='wrong'   ? 0xf08a8a : 0x9aa0e8;

  pf.bodyTop.material.color.setHex(topColor);
  pf.grass.material.color.setHex(grassColor);

  // ganti badge
  pf.group.remove(pf.badge);
  let tex;
  if(pf.state === 'correct') tex = checkBadgeTexture(true);
  else if(pf.state === 'wrong') tex = checkBadgeTexture(false);
  else tex = numberBadgeTexture(pf.index+1);

  const badge = new THREE.Mesh(
    new THREE.PlaneGeometry(50,50),
    new THREE.MeshBasicMaterial({ map:tex, transparent:true, depthWrite:false })
  );
  badge.position.set(0, PLAT_H/2 + 42, 0.3);
  badge.userData.baseY = PLAT_H/2 + 42;
  pf.group.add(badge);
  pf.badge = badge;
}

/* -------------------------------------------------------------------------
   7) NINJA (karakter)
   ------------------------------------------------------------------------- */
let ninja = null;

function buildNinja(){
  const g = new THREE.Group();

  const mDark   = new THREE.MeshBasicMaterial({ color:0x2a2a4d });
  const mDarker = new THREE.MeshBasicMaterial({ color:0x1c1c36 });
  const mSkin   = new THREE.MeshBasicMaterial({ color:0xf0d4b0 });
  const mEye    = new THREE.MeshBasicMaterial({ color:0x0f0f18 });
  const mRed    = new THREE.MeshBasicMaterial({ color:0xd9433f });
  const mGold   = new THREE.MeshBasicMaterial({ color:0xffcf6b });

  // ----- BADAN -----
  const body = new THREE.Mesh(new THREE.PlaneGeometry(22, 24), mDark);
  body.position.set(0, -2, 0);
  g.add(body);

  // sabuk merah
  const belt = new THREE.Mesh(new THREE.PlaneGeometry(23, 5), mRed);
  belt.position.set(0, -10, 0.12);
  g.add(belt);

  // gesper emas
  const buckle = new THREE.Mesh(new THREE.PlaneGeometry(5, 5), mGold);
  buckle.position.set(0, -10, 0.2);
  g.add(buckle);

  // ----- KEPALA -----
  const head = new THREE.Mesh(new THREE.CircleGeometry(11, 24), mDark);
  head.position.set(0, 15, 0);
  g.add(head);

  // area wajah (masker terbuka)
  const face = new THREE.Mesh(new THREE.PlaneGeometry(19, 6), mSkin);
  face.position.set(0, 15, 0.1);
  g.add(face);

  // mata
  const eyeL = new THREE.Mesh(new THREE.CircleGeometry(1.9, 12), mEye);
  eyeL.position.set(-3.6, 15, 0.2);
  g.add(eyeL);
  const eyeR = new THREE.Mesh(new THREE.CircleGeometry(1.9, 12), mEye);
  eyeR.position.set(3.6, 15, 0.2);
  g.add(eyeR);

  // ikat kepala
  const band = new THREE.Mesh(new THREE.PlaneGeometry(23, 5), mRed);
  band.position.set(0, 20.5, 0.15);
  g.add(band);

  // ekor ikat kepala
  const tail = new THREE.Mesh(new THREE.PlaneGeometry(18, 4.5), mRed);
  tail.position.set(-19, 18, 0.1);
  tail.rotation.z = 0.15;
  g.add(tail);

  // ----- TANGAN -----
  const armL = new THREE.Mesh(new THREE.PlaneGeometry(5, 18), mDarker);
  armL.position.set(-14, 0, 0);
  g.add(armL);
  const armR = new THREE.Mesh(new THREE.PlaneGeometry(5, 18), mDarker);
  armR.position.set(14, 0, 0);
  g.add(armR);

  // ----- KAKI -----
  const legL = new THREE.Mesh(new THREE.PlaneGeometry(7.5, 20), mDarker);
  legL.position.set(-5, -23, 0);
  g.add(legL);
  const legR = new THREE.Mesh(new THREE.PlaneGeometry(7.5, 20), mDarker);
  legR.position.set(5, -23, 0);
  g.add(legR);

  g.userData = { body, head, face, band, tail, armL, armR, legL, legR,
                 belt, buckle, eyeL, eyeR };
  return g;
}

/* -------------------------------------------------------------------------
   8) MAKHLUK TERBANG — Burung, Kelelawar, Kupu-kupu, Capung
   ------------------------------------------------------------------------- */
const flyers = [];

function createBird(color){
  const g = new THREE.Group();
  const mat = new THREE.MeshBasicMaterial({ color });
  // sayap kiri (pivot di ujung dalam)
  const wLgeo = new THREE.PlaneGeometry(18, 5).translate(-9, 0, 0);
  const wL = new THREE.Mesh(wLgeo, mat);
  wL.position.set(-2, 0, 0);
  g.add(wL);
  const wRgeo = new THREE.PlaneGeometry(18, 5).translate(9, 0, 0);
  const wR = new THREE.Mesh(wRgeo, mat);
  wR.position.set(2, 0, 0);
  g.add(wR);
  // badan
  const body = new THREE.Mesh(new THREE.CircleGeometry(3.5, 10), mat);
  body.scale.set(1.6, 0.8, 1);
  g.add(body);
  g.userData.parts = { wL, wR };
  return g;
}

function createBat(){
  const g = new THREE.Group();
  const mat = new THREE.MeshBasicMaterial({ color:0x14121f });
  const wLgeo = new THREE.PlaneGeometry(22, 8).translate(-11, 0, 0);
  const wL = new THREE.Mesh(wLgeo, mat);
  wL.position.set(-3, 0, 0);
  g.add(wL);
  const wRgeo = new THREE.PlaneGeometry(22, 8).translate(11, 0, 0);
  const wR = new THREE.Mesh(wRgeo, mat);
  wR.position.set(3, 0, 0);
  g.add(wR);
  // badan
  const body = new THREE.Mesh(new THREE.CircleGeometry(4, 10), mat);
  body.scale.set(0.9, 1.2, 1);
  g.add(body);
  // telinga
  const earMat = mat;
  const earL = new THREE.Mesh(new THREE.PlaneGeometry(3, 6), earMat);
  earL.position.set(-2.5, 5, 0);
  earL.rotation.z = 0.35;
  g.add(earL);
  const earR = new THREE.Mesh(new THREE.PlaneGeometry(3, 6), earMat);
  earR.position.set(2.5, 5, 0);
  earR.rotation.z = -0.35;
  g.add(earR);
  g.userData.parts = { wL, wR };
  return g;
}

function createButterfly(){
  const g = new THREE.Group();
  const hue = Math.floor(Math.random()*360);
  const wingMat = new THREE.MeshBasicMaterial({
    color: new THREE.Color(`hsl(${hue}, 82%, 66%)`)
  });
  const wingMat2 = new THREE.MeshBasicMaterial({
    color: new THREE.Color(`hsl(${(hue+40)%360}, 82%, 70%)`)
  });
  const bodyMat = new THREE.MeshBasicMaterial({ color:0x2a1c1c });

  // sayap atas
  const wUL = new THREE.Mesh(new THREE.CircleGeometry(8, 12), wingMat);
  wUL.position.set(-6, 3, 0);
  wUL.scale.set(1.1, 0.8, 1);
  g.add(wUL);
  const wUR = new THREE.Mesh(new THREE.CircleGeometry(8, 12), wingMat);
  wUR.position.set(6, 3, 0);
  wUR.scale.set(1.1, 0.8, 1);
  g.add(wUR);

  // sayap bawah
  const wDL = new THREE.Mesh(new THREE.CircleGeometry(6.5, 12), wingMat2);
  wDL.position.set(-5, -4, 0);
  wDL.scale.set(1, 0.85, 1);
  g.add(wDL);
  const wDR = new THREE.Mesh(new THREE.CircleGeometry(6.5, 12), wingMat2);
  wDR.position.set(5, -4, 0);
  wDR.scale.set(1, 0.85, 1);
  g.add(wDR);

  // badan
  const body = new THREE.Mesh(new THREE.PlaneGeometry(2.4, 15), bodyMat);
  body.position.y = 1;
  g.add(body);

  // antena
  const ant1 = new THREE.Mesh(new THREE.PlaneGeometry(1, 7), bodyMat);
  ant1.position.set(-2.2, 11, 0);
  ant1.rotation.z = 0.32;
  g.add(ant1);
  const ant2 = new THREE.Mesh(new THREE.PlaneGeometry(1, 7), bodyMat);
  ant2.position.set(2.2, 11, 0);
  ant2.rotation.z = -0.32;
  g.add(ant2);

  g.userData.parts = { wUL, wUR, wDL, wDR };
  return g;
}

function createDragonfly(){
  const g = new THREE.Group();
  const bodyMat = new THREE.MeshBasicMaterial({ color:0x3aa08c });
  const eyeMat  = new THREE.MeshBasicMaterial({ color:0x2b7d6d });
  const wingMat = new THREE.MeshBasicMaterial({
    color:0xaeeeff, transparent:true, opacity:0.62
  });

  // badan panjang
  const body = new THREE.Mesh(new THREE.PlaneGeometry(3.4, 22), bodyMat);
  g.add(body);

  // mata besar
  const eyeL = new THREE.Mesh(new THREE.CircleGeometry(3, 10), eyeMat);
  eyeL.position.set(-2.3, 10, 0.1);
  g.add(eyeL);
  const eyeR = new THREE.Mesh(new THREE.CircleGeometry(3, 10), eyeMat);
  eyeR.position.set(2.3, 10, 0.1);
  g.add(eyeR);

  // highlight mata
  const hl = new THREE.MeshBasicMaterial({ color:0xffffff });
  const h1 = new THREE.Mesh(new THREE.CircleGeometry(1, 8), hl);
  h1.position.set(-2.8, 10.5, 0.2);
  g.add(h1);
  const h2 = new THREE.Mesh(new THREE.CircleGeometry(1, 8), hl);
  h2.position.set(1.8, 10.5, 0.2);
  g.add(h2);

  // 4 sayap
  const w1 = new THREE.Mesh(new THREE.PlaneGeometry(15, 3.6), wingMat);
  w1.position.set(-9, 3, 0);
  g.add(w1);
  const w2 = new THREE.Mesh(new THREE.PlaneGeometry(15, 3.6), wingMat);
  w2.position.set(9, 3, 0);
  g.add(w2);
  const w3 = new THREE.Mesh(new THREE.PlaneGeometry(13, 3), wingMat);
  w3.position.set(-8, -3, 0);
  g.add(w3);
  const w4 = new THREE.Mesh(new THREE.PlaneGeometry(13, 3), wingMat);
  w4.position.set(8, -3, 0);
  g.add(w4);

  g.userData.parts = { w1, w2, w3, w4 };
  return g;
}

function buildFlyers(){
  const container = new THREE.Group();
  container.userData.parallax = 0;   // ditangani per-flyer
  layers.flyers = container;
  scene.add(container);

  const spread = VIRTUAL_W * 2.4;
  const types = ['bird','bat','butterfly','dragonfly'];

  for(let i=0;i<20;i++){
    const type = types[i % 4];
    let mesh;
    if(type==='bird')      mesh = createBird(i%3===0 ? 0x1b1b30 : 0x2b2b4a);
    else if(type==='bat')  mesh = createBat();
    else if(type==='butterfly') mesh = createButterfly();
    else                   mesh = createDragonfly();

    const f = {
      type, mesh,
      worldX: (Math.random()-0.5)*spread,
      baseY : VIRTUAL_H*0.05 + Math.random() * VIRTUAL_H * 0.45,
      bob   : Math.random()*Math.PI*2,
      phase : Math.random()*Math.PI*2,
      speed : 0.5 + Math.random()*1.4,
      dir   : Math.random() < 0.5 ? -1 : 1,
      parallax: 0.20 + Math.random()*0.55,
      scale : 0.7 + Math.random()*0.7
    };
    mesh.scale.setScalar(f.scale);
    if(f.dir < 0) mesh.scale.x *= -1;
    mesh.position.set(f.worldX, f.baseY, -300);
    container.add(mesh);
    flyers.push(f);
  }
}

/* -------------------------------------------------------------------------
   9) PLAYER (logika)
   ------------------------------------------------------------------------- */
const GRAVITY   = 0.78;
const JUMP_V    = 16.0;
const MOVE_ACC  = 1.05;
const MAX_SPEED = 5.6;
const FRICTION  = 0.80;

const player = {
  x: 0, y: 0, vx: 0, vy: 0,
  w: 26, h: 46,
  onGround: false,
  facing: 1,
  runPhase: 0
};

const state = {
  score: 0, lives: 3, answered: 0, correct: 0,
  paused: true, finished: false,
  time: 0, lastSafeIdx: 0
};

/* -------------------------------------------------------------------------
   10) KONTROL INPUT
   ------------------------------------------------------------------------- */
const keys = {};
window.addEventListener('keydown', e=>{
  keys[e.code] = true;
  if(['ArrowUp','ArrowDown','ArrowLeft','ArrowRight','Space'].includes(e.code)){
    e.preventDefault();
  }
});
window.addEventListener('keyup', e=>{ keys[e.code] = false; });

const touch = { left:false, right:false, jump:false };
function bindTouch(id, prop){
  const el = document.getElementById(id);
  const on  = e=>{ e.preventDefault(); touch[prop] = true; el.classList.add('active'); };
  const off = e=>{ e.preventDefault(); touch[prop] = false; el.classList.remove('active'); };
  el.addEventListener('touchstart', on, {passive:false});
  el.addEventListener('touchend',   off,{passive:false});
  el.addEventListener('touchcancel',off,{passive:false});
  el.addEventListener('mousedown',  on);
  el.addEventListener('mouseup',    off);
  el.addEventListener('mouseleave', off);
}
bindTouch('tLeft','left'); bindTouch('tRight','right'); bindTouch('tJump','jump');

/* -------------------------------------------------------------------------
   11) LAYOUT PARALLAX
   ------------------------------------------------------------------------- */
function layoutParallax(){
  // dipanggil saat resize; hanya perlu mereposisi layer lebar
  if(layers.sky) layers.sky.children[0].geometry.dispose();
  // cara sederhana: cukup biarkan (kamera ortografis otomatis menyesuaikan)
}

/* -------------------------------------------------------------------------
   12) GAME LOOP
   ------------------------------------------------------------------------- */
let lastTime = performance.now();

function loop(now){
  let dt = (now - lastTime) / 16.667;
  lastTime = now;
  dt = Math.min(dt, 2.4);

  state.time += dt;

  // Update visual selalu (agar animasi tetap hidup saat modal terbuka)
  animateFlyers(dt);
  animateStars(dt);
  animateBadges(dt);

  if(!state.paused && !state.finished){
    updatePlayer(dt);
  }

  updateCamera(dt);
  updateParallax();

  renderer.render(scene, camera);
  requestAnimationFrame(loop);
}

/* -------------------------------------------------------------------------
   13) UPDATE PLAYER & TABRAKAN
   ------------------------------------------------------------------------- */
function updatePlayer(dt){
  const p = player;

  const goLeft  = keys['ArrowLeft']  || keys['KeyA'] || touch.left;
  const goRight = keys['ArrowRight'] || keys['KeyD'] || touch.right;
  const goJump  = keys['ArrowUp'] || keys['KeyW'] || keys['Space'] || touch.jump;

  let ax = 0;
  if(goLeft)  ax -= 1;
  if(goRight) ax += 1;

  if(ax !== 0){
    p.vx += ax * MOVE_ACC * dt;
    p.facing = ax;
    p.runPhase += 0.36 * dt;
  }else{
    p.vx *= Math.pow(FRICTION, dt);
    if(Math.abs(p.vx) < 0.05) p.vx = 0;
    p.runPhase += 0.05 * dt;
  }
  p.vx = Math.max(-MAX_SPEED, Math.min(MAX_SPEED, p.vx));

  if(goJump && p.onGround){
    p.vy = JUMP_V;
    p.onGround = false;
  }

  p.vy -= GRAVITY * dt;

  const prevBottom = p.y - p.h/2;
  p.x += p.vx * dt;
  p.y += p.vy * dt;

  if(p.x < 20){ p.x = 20; p.vx = 0; }

  // Tabrakan platform (satu arah, hanya saat jatuh)
  p.onGround = false;
  const bottom = p.y - p.h/2;
  for(let i=0;i<platforms.length;i++){
    const pf = platforms[i];
    const hOverlap = (p.x + p.w/2 > pf.x + 6) && (p.x - p.w/2 < pf.x + pf.w - 6);
    if(!hOverlap) continue;
    if(p.vy <= 0 && prevBottom >= pf.y - 2 && bottom <= pf.y){
      p.y = pf.y + p.h/2;
      p.vy = 0;
      p.onGround = true;
      state.lastSafeIdx = i;

      if(!pf.answered && pf.state === 'idle' && !state.paused && !state.finished){
        triggerQuestion(i);
      }
      break;
    }
  }

  // Jatuh ke jurang
  if(p.y - p.h/2 < groundY - 6){
    loseLife('Jatuh ke jurang!');
    return;
  }

  updateNinjaVisual(dt);
}

function respawnPlayer(){
  const pf = platforms[state.lastSafeIdx];
  player.x = pf.x + pf.w/2;
  player.y = pf.y + player.h/2 + 4;
  player.vx = 0; player.vy = 0;
}

function loseLife(msg){
  state.lives--;
  updateHUD();
  flashMessage(msg, '#ff8f8f');
  if(state.lives <= 0){
    endGame(false);
    return;
  }
  respawnPlayer();
}

/* -------------------------------------------------------------------------
   14) VISUAL NINJA (animasi)
   ------------------------------------------------------------------------- */
function updateNinjaVisual(dt){
  if(!ninja) return;
  ninja.position.set(player.x, player.y, 0);

  const air = !player.onGround;
  const run = Math.sin(player.runPhase * 2.6);
  const parts = ninja.userData;

  // Badan sedikit membungkuk saat berlari
  const bob = air ? 0 : Math.abs(Math.sin(player.runPhase * 2.6)) * 1.5;
  parts.body.position.y = -2 - bob * 0.5;
  parts.head.position.y = 15 - bob * 0.5;
  parts.face.position.y = 15 - bob * 0.5;
  parts.band.position.y = 20.5 - bob * 0.5;
  parts.eyeL.position.y = 15 - bob * 0.5;
  parts.eyeR.position.y = 15 - bob * 0.5;
  parts.tail.position.y = 18 - bob * 0.5;
  parts.tail.rotation.z = 0.15 + Math.sin(state.time*0.18) * 0.15;

  // Kaki
  if(air){
    parts.legL.rotation.z =  0.35;
    parts.legR.rotation.z = -0.25;
    parts.legL.position.y = -24;
    parts.legR.position.y = -22;
  }else{
    parts.legL.rotation.z =  run * 0.45;
    parts.legR.rotation.z = -run * 0.45;
    parts.legL.position.y = -23;
    parts.legR.position.y = -23;
  }

  // Tangan
  if(air){
    parts.armL.rotation.z =  0.6;
    parts.armR.rotation.z = -0.6;
    parts.armL.position.y = 3;
    parts.armR.position.y = 3;
  }else{
    parts.armL.rotation.z = -run * 0.55;
    parts.armR.rotation.z =  run * 0.55;
    parts.armL.position.y = 0;
    parts.armR.position.y = 0;
  }

  // Arah hadap
  ninja.scale.x = player.facing >= 0 ? 1 : -1;
}

/* -------------------------------------------------------------------------
   15) ANIMASI MAKHLUK, BINTANG, BADGE
   ------------------------------------------------------------------------- */
function animateFlyers(dt){
  for(const f of flyers){
    // posisi horizontal dengan parallax
    f.worldX += f.speed * f.dir * dt * 0.9;
    const span = VIRTUAL_W * 2.4;
    if(f.worldX >  span/2) f.worldX = -span/2;
    if(f.worldX < -span/2) f.worldX =  span/2;

    const px = f.worldX + camX * (1 - f.parallax);
    const py = f.baseY + Math.sin(state.time*0.03 + f.bob) * 12;
    f.mesh.position.set(px, py, -300);

    // animasi sayap
    const ph = state.time * 0.25 + f.phase;
    const parts = f.mesh.userData.parts;

    if(f.type === 'bird'){
      const fl = Math.sin(ph * 3) * 0.7;
      parts.wL.rotation.z =  fl;
      parts.wR.rotation.z = -fl;
    }else if(f.type === 'bat'){
      const fl = Math.sin(ph * 4) * 0.9;
      parts.wL.rotation.z =  fl;
      parts.wR.rotation.z = -fl;
    }else if(f.type === 'butterfly'){
      const fl = 0.4 + 0.6 * Math.abs(Math.sin(ph * 3));
      parts.wUL.position.x = -6 * fl;
      parts.wUR.position.x =  6 * fl;
      parts.wDL.position.x = -5 * fl;
      parts.wDR.position.x =  5 * fl;
      parts.wUL.scale.x = 1.1 * fl;
      parts.wUR.scale.x = 1.1 * fl;
    }else if(f.type === 'dragonfly'){
      const fl = Math.sin(ph * 5) * 0.35;
      parts.w1.rotation.z =  fl;
      parts.w2.rotation.z = -fl;
      parts.w3.rotation.z =  fl * 0.7;
      parts.w4.rotation.z = -fl * 0.7;
    }
  }
}

function animateStars(dt){
  if(!layers.stars) return;
  layers.stars.children.forEach(s=>{
    const tw = 0.5 + 0.5 * Math.sin(state.time * 0.04 + s.userData.tw);
    s.material.opacity = s.userData.baseOp * (0.4 + 0.6 * tw);
  });
}

function animateBadges(dt){
  for(const pf of platforms){
    if(pf.badge && pf.state === 'idle'){
      const b = pf.badge;
      b.position.y = b.userData.baseY + Math.sin(state.time*0.06 + pf.index) * 5;
    }
  }
}

/* -------------------------------------------------------------------------
   16) KAMERA & PARALLAX
   ------------------------------------------------------------------------- */
function updateCamera(dt){
  const targetX = player.x - VIRTUAL_W * 0.32;
  camX += (targetX - camX) * 0.10 * dt;
  camera.position.x = camX;
}

function updateParallax(){
  const apply = (layer)=>{
    if(!layer) return;
    layer.position.x = camX * (1 - layer.userData.parallax);
  };
  apply(layers.sky);
  apply(layers.stars);
  apply(layers.sun);
  apply(layers.moon);
  apply(layers.mountainFar);
  apply(layers.mountainMid);
  apply(layers.mountainNear);
  // flyers ditangani per objek
}

/* -------------------------------------------------------------------------
   17) HUD & FLASH MESSAGE
   ------------------------------------------------------------------------- */
function updateHUD(){
  document.getElementById('lives').textContent =
    '❤️'.repeat(Math.max(0, state.lives)) +
    '🖤'.repeat(Math.max(0, 3 - state.lives));
  document.getElementById('score').textContent = 'Skor: ' + state.score;
  document.getElementById('progress').textContent =
    'Soal: ' + state.answered + '/10';
}

let flashEl = null, flashTimer = 0;
function flashMessage(text, color){
  if(!flashEl){
    flashEl = document.createElement('div');
    flashEl.style.cssText = `
      position:absolute;left:50%;top:38%;transform:translate(-50%,-50%);
      background:rgba(10,14,30,.85);color:#fff;padding:14px 22px;
      border-radius:12px;font-weight:800;font-size:17px;z-index:15;
      border:2px solid rgba(255,207,107,.5);pointer-events:none;
      transition:opacity .3s;text-align:center;`;
    document.body.appendChild(flashEl);
  }
  flashEl.textContent = text;
  flashEl.style.color = color || '#fff';
  flashEl.style.opacity = 1;
  clearTimeout(flashTimer);
  flashTimer = setTimeout(()=>{ flashEl.style.opacity = 0; }, 1100);
}

/* -------------------------------------------------------------------------
   18) MODAL SOAL
   ------------------------------------------------------------------------- */
const modal     = document.getElementById('modal');
const card      = document.getElementById('card');
let modalOriginalHTML = card.innerHTML;

function triggerQuestion(idx){
  state.paused = true;
  const q = QUESTIONS[idx];
  const pf = platforms[idx];
  pf.answered = true;

  card.innerHTML = `
    <div class="qnum">Soal ${idx+1} dari 10</div>
    <div class="qtext">${q.q}</div>
    <div id="options"></div>
    <div class="feedback" id="feedback"></div>
  `;

  const optWrap = card.querySelector('#options');
  const fbEl    = card.querySelector('#feedback');

  q.options.forEach((opt, i)=>{
    const btn = document.createElement('button');
    btn.className = 'opt';
    btn.textContent = String.fromCharCode(65+i) + '.  ' + opt;
    btn.addEventListener('click', ()=> handleAnswer(idx, i, btn, optWrap, fbEl));
    optWrap.appendChild(btn);
  });

  modal.classList.remove('hidden');
}

function handleAnswer(idx, choice, btn, optWrap, fbEl){
  const q = QUESTIONS[idx];
  const pf = platforms[idx];
  const allBtns = optWrap.querySelectorAll('.opt');
  allBtns.forEach(b => b.disabled = true);

  const isCorrect = (choice === q.answer);

  if(isCorrect){
    btn.classList.add('correct');
    pf.state = 'correct';
    state.score += 10;
    state.correct++;
    fbEl.className = 'feedback ok';
    fbEl.textContent = '✅ Benar! ' + q.explain;
  }else{
    btn.classList.add('wrong');
    allBtns[q.answer].classList.add('correct');
    pf.state = 'wrong';
    state.lives--;
    fbEl.className = 'feedback bad';
    fbEl.textContent = '❌ Kurang tepat. Jawaban benar: ' +
      q.options[q.answer] + '. ' + q.explain;
  }

  state.answered++;
  updatePlatformVisual(pf);
  updateHUD();

  if(state.lives <= 0){
    setTimeout(()=>{ modal.classList.add('hidden'); endGame(false); },
               isCorrect ? 1400 : 2000);
    return;
  }

  if(state.answered >= 10){
    setTimeout(()=>{ modal.classList.add('hidden'); endGame(true); },
               isCorrect ? 1400 : 2000);
    return;
  }

  setTimeout(()=>{
    modal.classList.add('hidden');
    state.paused = false;
    lastTime = performance.now();
  }, isCorrect ? 1400 : 2100);
}

/* -------------------------------------------------------------------------
   19) END GAME (dengan JSON storage)
   ------------------------------------------------------------------------- */
function endGame(win){
  state.finished = true;
  state.paused = true;

  // Simpan ke localStorage (JSON)
  const save = recordGame(state.score, state.correct);

  const histRows = (save.history||[]).slice().reverse().map((h,i)=>{
    const d = new Date(h.at);
   
