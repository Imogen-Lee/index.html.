<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=yes, viewport-fit=cover">
<title>Paws & Peace · Pet Companion Therapy Station</title>
<meta name="description" content="Paws & Peace – Professional pet companion therapy station. Healing hearts through pet companionship.">
<link href="https://fonts.googleapis.com/css2?family=Playfair+Display:ital,wght@0,500;0,700;1,500&family=Special+Elite&family=Noto+Serif+SC:wght@400;600;700&display=swap" rel="stylesheet">
<link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css" />
<script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"></script>
<style>
:root{
--amber:#C27F3A;
--rust:#9C4A2A;
--cream:#FDF8EE;
--cream-2:#F5ECD7;
--olive:#6B7B4F;
--ink:#3B2A1E;
--ink-soft:#5C4632;
--line:rgba(59,42,30,.18);
--shadow:0 6px 0 rgba(59,42,30,.12), 0 18px 30px -18px rgba(59,42,30,.4);
--paper-noise:url("data:image/svg+xml;utf8,<svg xmlns='http://www.w3.org/2000/svg' width='400' height='400'><filter id='n'><feTurbulence type='fractalNoise' baseFrequency='0.85' numOctaves='2' stitchTiles='stitch'/><feColorMatrix values='0 0 0 0 0.45 0 0 0 0 0.30 0 0 0 0 0.18 0 0 0 0.18 0'/></filter><rect width='100%' height='100%' filter='url(%23n)'/></svg>");
}
*{box-sizing:border-box;margin:0;padding:0}
html{scroll-behavior:smooth}
body{
font-family:"Noto Serif SC","Playfair Display",Georgia,serif;
color:var(--ink);
background:var(--cream);
background-image:
radial-gradient(1200px 600px at 80% -10%, rgba(194,127,58,.18), transparent 60%),
radial-gradient(900px 500px at -10% 110%, rgba(107,123,79,.16), transparent 60%),
var(--paper-noise);
background-size:auto,auto,400px 400px;
line-height:1.7;
cursor:url("data:image/svg+xml;utf8,<svg xmlns='http://www.w3.org/2000/svg' width='28' height='28' viewBox='0 0 64 64'><g fill='%239C4A2A'><circle cx='20' cy='22' r='6'/><circle cx='44' cy='22' r='6'/><circle cx='12' cy='38' r='5'/><circle cx='52' cy='38' r='5'/><ellipse cx='32' cy='46' rx='14' ry='11'/></g></svg>") 14 14, auto;
}
a,button{cursor:url("data:image/svg+xml;utf8,<svg xmlns='http://www.w3.org/2000/svg' width='32' height='32' viewBox='0 0 64 64'><g fill='%23C27F3A'><circle cx='20' cy='22' r='6'/><circle cx='44' cy='22' r='6'/><circle cx='12' cy='38' r='5'/><circle cx='52' cy='38' r='5'/><ellipse cx='32' cy='46' rx='14' ry='11'/></g></svg>") 16 16, pointer;}

/* ================ Sidebar (桌面版) ================ */
.sidebar{
position:fixed;
top:0;
left:0;
width:260px;
height:100%;
background:linear-gradient(180deg,#F2E3C2 0%,#E8D2A6 100%);
border-right:2px dashed var(--line);
padding:32px 24px;
display:flex;
flex-direction:column;
gap:28px;
z-index:1000;
box-shadow:6px 0 24px -16px rgba(59,42,30,.4);
transition: transform 0.3s ease;
}
.sidebar::before{
content:"";position:absolute;inset:0;background:var(--paper-noise);opacity:.35;pointer-events:none;mix-blend-mode:multiply;
}
.brand{display:flex;flex-direction:column;gap:6px;position:relative}
.brand-mark{
width:64px;height:64px;border-radius:50%;
background:radial-gradient(circle at 35% 30%,#E8B97A,var(--rust));
display:grid;place-items:center;color:var(--cream);font-family:"Special Elite";font-size:22px;
box-shadow:inset 0 -4px 0 rgba(0,0,0,.15), var(--shadow);
}
.brand-name{font-family:"Playfair Display",serif;font-weight:700;font-size:18px;color:var(--rust);letter-spacing:.5px;line-height:1.2}
.brand-sub{font-family:"Special Elite";font-size:11px;color:var(--ink-soft);letter-spacing:1px}
.nav{display:flex;flex-direction:column;gap:4px;position:relative}
.nav a{
text-decoration:none;color:var(--ink-soft);font-size:15px;padding:10px 14px;border-radius:8px;
display:flex;align-items:center;gap:10px;border:1px solid transparent;transition:all .25s ease;
font-weight:500;
}
.nav a::before{content:"❦";color:var(--amber);opacity:.55;font-size:12px}
.nav a:hover,.nav a.active{
background:var(--cream);color:var(--rust);border-color:var(--line);
transform:translateX(4px);box-shadow:3px 3px 0 var(--amber);
}
.side-foot{margin-top:auto;font-family:"Special Elite";font-size:11px;color:var(--ink-soft);opacity:.75;line-height:1.6;position:relative}

/* 移动端菜单按钮 */
.mobile-menu-btn{
display: none;
position: fixed;
top: 18px;
left: 18px;
z-index: 1100;
background: var(--rust);
color: var(--cream);
border: none;
padding: 8px 18px;
border-radius: 40px;
font-family: "Special Elite", monospace;
font-size: 15px;
font-weight: bold;
letter-spacing: 1px;
box-shadow: var(--shadow);
cursor: pointer;
transition: all 0.2s ease;
}
.mobile-menu-btn:hover{
background: #b85a34;
transform: scale(1.02);
}

/* 移动端遮罩层 */
.mobile-overlay{
display: none;
position: fixed;
top: 0;
left: 0;
width: 100%;
height: 100%;
background: rgba(0,0,0,0.4);
z-index: 999;
backdrop-filter: blur(2px);
transition: opacity 0.3s ease;
}

/* ================ Main ================ */
main{margin-left:260px;padding:0}
section{
padding:96px 8% 96px;
position:relative;
scroll-margin-top:20px;
}
.section-tag{
display:inline-block;font-family:"Special Elite";letter-spacing:3px;font-size:12px;
color:var(--rust);padding:4px 12px;border:1px solid var(--rust);border-radius:999px;margin-bottom:18px;
background:rgba(253,248,238,.6);
}
.section-title{
font-family:"Playfair Display",serif;font-weight:700;font-size:42px;color:var(--ink);margin-bottom:14px;line-height:1.2;
}
.section-title em{color:var(--rust);font-style:italic}
.section-lead{max-width:680px;color:var(--ink-soft);font-size:16px;margin-bottom:48px}

/* fade in */
.reveal{opacity:0;transform:translateY(24px);transition:opacity .9s ease,transform .9s ease}
.reveal.visible{opacity:1;transform:none}

/* ================ Hero ================ */
#home{
min-height:100vh;display:flex;align-items:center;
background:
radial-gradient(600px 300px at 80% 30%, rgba(156,74,42,.15), transparent 70%),
linear-gradient(180deg, transparent, rgba(245,236,215,.6));
}
.hero-grid{display:grid;grid-template-columns:1.2fr .9fr;gap:60px;align-items:center;width:100%}
.hero h1{
font-family:"Playfair Display",serif;font-weight:700;font-size:64px;line-height:1.05;color:var(--ink);
letter-spacing:-1px;margin-bottom:8px;
}
.hero h1 .stamp{
display:inline-block;color:var(--rust);border:3px double var(--rust);padding:2px 14px;border-radius:6px;
transform:rotate(-2deg);font-size:.55em;vertical-align:middle;margin-left:10px;font-family:"Special Elite";letter-spacing:2px;
}
.hero .slogan{font-family:"Playfair Display",serif;font-style:italic;font-size:24px;color:var(--rust);margin:18px 0 24px}
.hero .intro{color:var(--ink-soft);font-size:17px;max-width:520px;margin-bottom:36px}
.btn-row{display:flex;gap:16px;flex-wrap:wrap}
.btn{
display:inline-flex;align-items:center;gap:10px;padding:14px 26px;
font-family:"Special Elite";letter-spacing:2px;font-size:13px;text-decoration:none;
border:2px solid var(--ink);background:var(--cream);color:var(--ink);
box-shadow:5px 5px 0 var(--ink);transition:all .2s ease;text-transform:uppercase;
}
.btn:hover{transform:translate(-3px,-3px);box-shadow:8px 8px 0 var(--ink)}
.btn.primary{background:var(--rust);color:var(--cream);border-color:var(--rust);box-shadow:5px 5px 0 var(--ink)}
.btn.primary:hover{box-shadow:8px 8px 0 var(--ink)}
.hero-card{
position:relative;background:var(--cream);border:1px solid var(--line);
padding:28px;border-radius:6px;box-shadow:var(--shadow);transform:rotate(2deg);
}
.hero-card::before{content:"";position:absolute;inset:0;background:var(--paper-noise);opacity:.4;mix-blend-mode:multiply;border-radius:6px;pointer-events:none}
.polaroid{
background:#fff;padding:14px 14px 60px;box-shadow:var(--shadow);position:relative;
transform:rotate(-3deg);
}
.polaroid .ph{
width:100%;aspect-ratio:1/1;background:
radial-gradient(circle at 30% 30%, #E8B97A, transparent 50%),
radial-gradient(circle at 70% 60%, #9C4A2A, transparent 50%),
linear-gradient(135deg,#6B7B4F,#3B2A1E);
display:grid;place-items:center;color:var(--cream);font-family:"Special Elite";font-size:14px;letter-spacing:2px
}
.polaroid .cap{position:absolute;bottom:14px;left:0;right:0;text-align:center;font-family:"Special Elite";color:var(--ink-soft);font-size:13px}
.tape{position:absolute;width:100px;height:24px;background:rgba(194,127,58,.5);top:-12px;left:50%;transform:translateX(-50%) rotate(-3deg);box-shadow:0 2px 4px rgba(0,0,0,.1)}

/* ================ Story ================ */
.story-grid{display:grid;grid-template-columns:1.1fr .9fr;gap:60px;align-items:start}
.story-text p{margin-bottom:16px;color:var(--ink-soft);font-size:16px}
.story-text p::first-letter{font-family:"Playfair Display",serif;font-size:54px;float:left;line-height:.9;padding:6px 10px 0 0;color:var(--rust);font-weight:700}
.focus-box{
background:var(--cream);border:1px solid var(--line);padding:28px;position:relative;border-radius:4px;
box-shadow:var(--shadow);
}
.focus-box::before{content:"";position:absolute;inset:0;background:var(--paper-noise);opacity:.35;mix-blend-mode:multiply;border-radius:4px;pointer-events:none}
.focus-box h4{font-family:"Special Elite";letter-spacing:2px;color:var(--rust);font-size:14px;margin-bottom:18px;text-transform:uppercase;border-bottom:1px dashed var(--line);padding-bottom:10px}
.focus-list{display:flex;flex-direction:column;gap:14px}
.focus-list .item{display:flex;gap:12px;align-items:flex-start}
.focus-list .num{font-family:"Playfair Display",serif;font-style:italic;color:var(--amber);font-size:22px;min-width:28px}
.focus-list .item b{display:block;color:var(--ink);font-size:15px;margin-bottom:2px}
.focus-list .item span{color:var(--ink-soft);font-size:14px}

/* ================ Services ================ */
.services-grid{display:grid;grid-template-columns:repeat(3,1fr);gap:28px}
.svc-card{
background:var(--cream);border:1px solid var(--line);padding:32px 26px;border-radius:6px;
box-shadow:var(--shadow);position:relative;transition:transform .35s ease,box-shadow .35s ease;
}
.svc-card::before{content:"";position:absolute;inset:0;background:var(--paper-noise);opacity:.3;mix-blend-mode:multiply;border-radius:6px;pointer-events:none}
.svc-card:hover{transform:translate(-4px,-6px);box-shadow:0 10px 0 rgba(59,42,30,.15), 0 30px 50px -20px rgba(59,42,30,.45)}
.svc-icon{
width:60px;height:60px;border-radius:50%;background:var(--rust);color:var(--cream);
display:grid;place-items:center;font-size:26px;margin-bottom:18px;box-shadow:inset 0 -3px 0 rgba(0,0,0,.2);
}
.svc-card h3{font-family:"Playfair Display",serif;font-size:22px;color:var(--ink);margin-bottom:10px}
.svc-card p{color:var(--ink-soft);font-size:14.5px;margin-bottom:14px}
.svc-card .meta{font-family:"Special Elite";font-size:11px;letter-spacing:2px;color:var(--olive);text-transform:uppercase}
.doing-grid{display:grid;grid-template-columns:1fr 1fr;gap:24px;margin-top:48px}
.doing{
border:1px dashed var(--rust);background:rgba(253,248,238,.7);padding:28px;border-radius:6px;position:relative;
transition:transform .3s ease;
}
.doing:hover{transform:translateY(-4px)}
.doing .stamp-tag{position:absolute;top:-12px;left:24px;background:var(--rust);color:var(--cream);font-family:"Special Elite";font-size:11px;padding:4px 10px;letter-spacing:2px}
.doing h4{font-family:"Playfair Display",serif;font-size:20px;margin-bottom:8px;color:var(--ink)}
.doing p{color:var(--ink-soft);font-size:14.5px}

/* ================ 地图容器样式 ================ */
.map-wrap{
background:var(--cream);
border:6px double #DAB77A;
border-radius:24px;
padding:12px;
box-shadow:6px 6px 0 rgba(59,42,30,.12), 0 18px 40px -20px rgba(59,42,30,.35);
position:relative;
overflow:hidden;
max-width:100%;
box-sizing:border-box;
isolation:isolate;
}
.map-wrap::before{
content:"";
position:absolute;
inset:0;
background:var(--paper-noise);
opacity:.25;
mix-blend-mode:multiply;
pointer-events:none;
border-radius:20px;
z-index:2;
}
#vintageMap{
width:100%;
height:520px;
max-width:100%;
border-radius:14px;
box-shadow:inset 0 0 0 1px var(--line);
background:#efe6d2;
cursor:inherit;
overflow:hidden;
position:relative;
z-index:1;
contain:strict;
}
@media (max-width:720px){
#vintageMap{height:360px}
}
#vintageMap .leaflet-tile-pane{
filter:sepia(.35) saturate(.85) hue-rotate(-8deg) brightness(.98) contrast(.95);
}
.leaflet-container{
font-family:"Noto Serif SC",serif;
background:#f5ecd6;
}
.leaflet-popup-content-wrapper{
background:#FDF8EE;
border:1px solid #9C4A2A;
border-radius:4px;
box-shadow:4px 4px 0 rgba(59,42,30,.18);
padding:4px 6px;
}
.leaflet-popup-tip{
background:#FDF8EE;
border:1px solid #9C4A2A;
}
.leaflet-popup-content{
margin:10px 14px;
color:#3B2A1E;
font-size:13px;
line-height:1.6;
}
.leaflet-popup-content .pop-name{
font-family:"Special Elite",monospace;
color:#9C4A2A;
font-size:13px;
letter-spacing:2px;
margin-bottom:4px;
}
.leaflet-popup-content .pop-desc{
font-weight:600;
color:#3B2A1E;
}
.leaflet-popup-content .pop-addr{
font-size:12px;
color:#5C4632;
margin-top:2px;
}
.leaflet-control-zoom a{
background:#FDF8EE!important;
color:#9C4A2A!important;
border:1px solid #9C4A2A!important;
font-family:"Special Elite",monospace;
}
.leaflet-control-attribution{
background:rgba(253,248,238,.7)!important;
font-family:"Special Elite",monospace;
font-size:10px;
}
.city-tags{
display:flex;
flex-wrap:wrap;
gap:10px;
margin-top:24px;
}
.city-tags .tag{
font-family:"Special Elite";
font-size:12px;
letter-spacing:1.5px;
color:var(--rust);
padding:6px 14px;
border:1px solid var(--rust);
border-radius:999px;
background:var(--cream);
transition:all .2s ease;
}
.city-tags .tag:hover{
background:var(--rust);
color:var(--cream);
transform:translateY(-2px);
}

/* ================ Timeline ================ */
.timeline{position:relative;padding-left:30px}
.timeline::before{content:"";position:absolute;left:8px;top:6px;bottom:6px;width:2px;background:repeating-linear-gradient(180deg,var(--rust) 0 6px,transparent 6px 12px)}
.tl-item{position:relative;margin-bottom:36px;padding:22px 26px;background:var(--cream);border:1px solid var(--line);border-radius:6px;box-shadow:var(--shadow);transition:transform .3s ease}
.tl-item:hover{transform:translateX(6px)}
.tl-item::before{content:"";position:absolute;left:-28px;top:24px;width:14px;height:14px;border-radius:50%;background:var(--amber);border:3px solid var(--cream);box-shadow:0 0 0 2px var(--rust)}
.tl-year{font-family:"Special Elite";color:var(--rust);font-size:13px;letter-spacing:2px;margin-bottom:6px}
.tl-item h4{font-family:"Playfair Display",serif;font-size:20px;margin-bottom:6px;color:var(--ink)}
.tl-item p{color:var(--ink-soft);font-size:14.5px}

/* ================ Skills ================ */
.skill-block{margin-bottom:36px}
.skill-block h4{font-family:"Special Elite";letter-spacing:2px;color:var(--rust);font-size:13px;margin-bottom:14px;text-transform:uppercase}
.chips{display:flex;flex-wrap:wrap;gap:10px}
.chip{
padding:8px 16px;border-radius:4px;background:var(--cream);border:1px solid var(--ink);
font-size:13.5px;color:var(--ink);box-shadow:3px 3px 0 var(--amber);transition:all .2s ease;
font-family:"Noto Serif SC",serif;
}
.chip:hover{transform:translate(-2px,-2px);box-shadow:5px 5px 0 var(--rust)}
.chip.olive{box-shadow:3px 3px 0 var(--olive)}
.chip.olive:hover{box-shadow:5px 5px 0 var(--olive)}

/* ================ Contact ================ */
.envelope{
max-width:760px;margin:0 auto;background:var(--cream);
border:1px solid var(--line);border-radius:6px;padding:48px 56px;position:relative;box-shadow:var(--shadow);
}
.envelope::before{content:"";position:absolute;inset:0;background:var(--paper-noise);opacity:.3;mix-blend-mode:multiply;border-radius:6px;pointer-events:none}
.envelope::after{
content:"";position:absolute;top:-1px;left:-1px;right:-1px;height:30px;
background:repeating-linear-gradient(45deg,var(--rust) 0 12px,var(--cream) 12px 24px,var(--ink) 24px 36px,var(--cream) 36px 48px);
opacity:.85;
}
.env-inner{padding-top:28px}
.stamp-img{
position:absolute;right:40px;top:60px;width:90px;height:110px;background:var(--cream);
border:1px dashed var(--rust);display:grid;place-items:center;text-align:center;
font-family:"Special Elite";color:var(--rust);font-size:11px;letter-spacing:1.5px;line-height:1.3;
transform:rotate(6deg);box-shadow:var(--shadow);padding:8px;
}
.contact-grid{display:grid;grid-template-columns:1fr 1fr;gap:18px 32px;margin-top:24px}
.c-row{border-bottom:1px dashed var(--line);padding:10px 0}
.c-row .lbl{font-family:"Special Elite";font-size:11px;letter-spacing:2px;color:var(--rust);text-transform:uppercase;margin-bottom:4px}
.c-row .val{font-size:15px;color:var(--ink)}
footer{
text-align:center;padding:40px 24px 60px;font-family:"Special Elite";font-size:12px;
letter-spacing:2px;color:var(--ink-soft);border-top:1px dashed var(--line);margin:0 8%;
}

/* paw cursor follower */
.paw-trail{position:fixed;width:24px;height:24px;pointer-events:none;z-index:9999;opacity:0;transition:opacity .6s ease}
.paw-trail svg{width:100%;height:100%}

/* ================ 移动端响应式 - 彻底重写 ================ */
@media (max-width: 900px) {
  .mobile-menu-btn {
    display: block !important;
  }
  .sidebar {
    transform: translateX(-100%);
    transition: transform 0.3s ease;
    z-index: 1050;
  }
  .sidebar.open {
    transform: translateX(0);
  }
  .mobile-overlay {
    display: block;
    opacity: 0;
    visibility: hidden;
    transition: opacity 0.3s ease, visibility 0.3s;
    z-index: 1040;
  }
  .mobile-overlay.show {
    opacity: 1;
    visibility: visible;
  }
  main {
    margin-left: 0;
  }
  section {
    padding: 80px 6% 80px;
  }
  .hero-grid,
  .story-grid {
    grid-template-columns: 1fr;
    gap: 40px;
  }
  .hero h1 {
    font-size: 44px;
  }
  .section-title {
    font-size: 32px;
  }
  .services-grid {
    grid-template-columns: 1fr;
  }
  .doing-grid {
    grid-template-columns: 1fr;
  }
  .contact-grid {
    grid-template-columns: 1fr;
  }
  .stamp-img {
    position: relative;
    right: auto;
    top: auto;
    margin: 0 auto 20px;
  }
  .envelope {
    padding: 48px 28px;
  }
}
</style>
</head>
<body>

<!-- 移动端菜单按钮 + 遮罩层 -->
<button class="mobile-menu-btn" id="mobileMenuBtn">☰ MENU</button>
<div class="mobile-overlay" id="mobileOverlay"></div>

<aside class="sidebar" id="sidebar">
<div class="brand">
<div class="brand-mark">P&P</div>
<div class="brand-name">Paws & Peace<br>Pet Companion Therapy Station</div>
<div class="brand-sub">EST. 2026 · HEALING HEARTS</div>
</div>
<nav class="nav" id="nav">
<a href="#home" class="active">Home</a>
<a href="#story">Our Story</a>
<a href="#services">Therapy Services</a>
<a href="#map">Healing Footprint</a>
<a href="#journal">Milestones</a>
<a href="#skills">Skills & Passions</a>
<a href="#contact">Contact</a>
</nav>
<div class="side-foot">
"Paws for comfort,<br/>peace for soul"<br/><br/>--- A Vintage Healing Diary ---
</div>
</aside>

<main>
<!-- HERO -->
<section id="home">
<div class="hero-grid">
<div class="hero reveal">
<span class="section-tag">Paws & Peace · EST. 2026</span>
<h1>Paws for comfort,<br/>peace for soul<span class="stamp">SINCE 2026</span></h1>
<div class="slogan">"Paws for comfort, peace for soul — healing hearts through pet companionship."</div>
<p class="intro">Paws & Peace is a professional pet companion therapy station dedicated to emotional comfort and psychological healing. We combine trained therapy pets with compassionate care to help you find inner peace.</p>
<div class="btn-row">
<a href="#services" class="btn primary">🐾 Book a Session</a>
<a href="#story" class="btn">Read Our Story</a>
</div>
</div>
<div class="reveal" style="position:relative">
<div class="hero-card">
<div class="polaroid">
<span class="tape"></span>
<div class="ph">FIELD NOTES · 2026</div>
<div class="cap">--- Therapy dog "Biscuit" ---</div>
</div>
</div>
</div>
</div>
</section>

<!-- STORY / Company Overview -->
<section id="story">
<span class="section-tag reveal">CHAPTER I · OUR STORY</span>
<h2 class="section-title reveal">Warm paws, <em>quiet hearts</em></h2>
<p class="section-lead reveal">Paws & Peace was founded with the mission of "healing hearts through pet companionship". We believe that animals are nature’s best therapists.</p>
<div class="story-grid">
<div class="story-text reveal">
<p>In a fast-paced world, more and more people suffer from anxiety, loneliness and emotional exhaustion. However, not everyone can keep a pet due to housing restrictions or busy schedules. Paws & Peace fills this gap by offering a professional, warm space where people can interact with well-trained therapy pets — dogs, cats and small animals — under the guidance of certified counselors and animal trainers.</p>
<p>Our professional team includes licensed pet therapists, animal behaviorists and psychological consultants. We combine evidence-based animal-assisted intervention (AAI) with a cozy, vintage-inspired environment. Our slogan “Paws for comfort, peace for soul” reflects our core promise: through the gentle touch of a paw, we bring comfort to the heart and guide you back to inner peace.</p>
<p>We are committed to professionalism, warmth and personalization — every client receives an initial psychological assessment and a tailored therapy plan. This is not just a pet café; it is a healing journey.</p>
</div>
<div class="focus-box reveal">
<h4>✦ What we focus on & learn</h4>
<div class="focus-list">
<div class="item"><span class="num">01</span><div><b>Animal-Assisted Intervention (AAI)</b><span>Scientifically proven to lower cortisol and ease anxiety.</span></div></div>
<div class="item"><span class="num">02</span><div><b>Mental Health & Emotional Wellbeing</b><span>One-on-one sessions, group activities and pet-assisted counseling.</span></div></div>
<div class="item"><span class="num">03</span><div><b>Pet Care Education</b><span>Training courses and mental health workshops for the community.</span></div></div>
<div class="item"><span class="num">04</span><div><b>End-of-life Emotional Support</b><span>Compassionate companionship for farewells and grief.</span></div></div>
</div>
</div>
</div>
</section>

<!-- SERVICES / Product & Services -->
<section id="services">
<span class="section-tag reveal">CHAPTER II · THERAPY SERVICES</span>
<h2 class="section-title reveal">Healing through <em>gentle paws</em></h2>
<p class="section-lead reveal">We offer personalized pet companion therapy services designed for different needs — from anxiety relief to senior companionship.</p>
<div class="services-grid">
<div class="svc-card reveal"><div class="svc-icon">🐾</div><h3>One-on-One Companion Sessions</h3><p>Private, 50-minute sessions with a calm therapy pet and a licensed counselor. Ideal for anxiety, social anxiety, or simply emotional reset.</p><div class="meta">SERVICE · 01</div></div>
<div class="svc-card reveal"><div class="svc-icon">👥</div><h3>Group Interaction Activities</h3><p>Small-group workshops where participants bond with therapy pets and each other — reducing loneliness and building trust.</p><div class="meta">SERVICE · 02</div></div>
<div class="svc-card reveal"><div class="svc-icon">🕯️</div><h3>Pet-Assisted Psychological Counseling</h3><p>Integrate therapy pets into professional mental health sessions. Especially effective for trauma, depression and elderly care.</p><div class="meta">SERVICE · 03</div></div>
</div>
<div class="doing-grid">
<div class="doing reveal"><span class="stamp-tag">CURRENT PROJECT</span><h4>"Healing Living Room" Pop-up</h4><p>Partnering with community centers in 4 cities to offer free trial sessions for stressed urban workers and seniors.</p></div>
<div class="doing reveal"><span class="stamp-tag">SELF-GROWTH</span><h4>Research & Education</h4><p>We are currently conducting a 6-month study on pet therapy’s effect on workplace burnout, in cooperation with local universities.</p></div>
</div>
</section>

<!-- MAP -->
<section id="map">
<span class="section-tag reveal">CHAPTER III · HEALING FOOTPRINT</span>
<h2 class="section-title reveal">Our <em>cities</em> of comfort</h2>
<p class="section-lead reveal">Each pin on the map represents a place where Paws & Peace brings comfort. Click the cat paw markers for details.</p>
<div class="map-wrap reveal">
<div id="vintageMap"></div>
</div>
<div class="city-tags reveal" id="cityTags"></div>
</section>

<!-- MILESTONES -->
<section id="journal">
<span class="section-tag reveal">CHAPTER IV · MILESTONES</span>
<h2 class="section-title reveal">A <em>fading diary</em> of our journey</h2>
<p class="section-lead reveal">Every step we’ve taken is recorded in our vintage journal.</p>
<div class="timeline">
<div class="tl-item reveal"><div class="tl-year">★ SPRING 2026</div><h4>Brand Launch & First Therapy Dog "Biscuit"</h4><p>Opened our first studio in a renovated vintage house. Began weekly visits to a local nursing home.</p></div>
<div class="tl-item reveal"><div class="tl-year">★ AUTUMN 2026</div><h4>Partnership with Mental Health Hospital</h4><p>Launched AAI program in pediatric and psychiatric wards, serving 500+ patients.</p></div>
<div class="tl-item reveal"><div class="tl-year">★ SUMMER 2027</div><h4>National Tour: “Old Times, Warm Paws”</h4><p>Traveled to 12 cities, documenting pet-human bonds through vintage photography.</p></div>
<div class="tl-item reveal"><div class="tl-year">★ 2028 · NOW</div><h4>Four Permanent “Healing Living Rooms”</h4><p>Established physical spaces in Beijing, Shanghai, Chengdu and Guangzhou, open for appointments.</p></div>
</div>
</section>

<!-- SKILLS -->
<section id="skills">
<span class="section-tag reveal">CHAPTER V · SKILLS & PASSIONS</span>
<h2 class="section-title reveal">Craftsmanship & <em>heartfelt tools</em></h2>
<p class="section-lead reveal">We are both scientific practitioners and slow-life enthusiasts.</p>
<div class="skill-block reveal"><h4>✦ Professional Skills</h4><div class="chips"><span class="chip">Therapy Dog Certification (IAHAIO)</span><span class="chip">Pet Nutrition & Wellness</span><span class="chip">Animal Behavior Consulting IAABC</span><span class="chip">Pet First Aid & CPR</span><span class="chip">End-of-life Doula Training</span><span class="chip">Mental Health First Aid</span></div></div>
<div class="skill-block reveal"><h4>✦ Personal Passions</h4><div class="chips"><span class="chip olive">Vintage Film Photography</span><span class="chip olive">Handwritten Letters</span><span class="chip olive">Mid-century Furniture</span><span class="chip olive">Jazz & Vinyl Records</span><span class="chip olive">Journaling & Collage</span><span class="chip olive">Herbal Gardening</span></div></div>
<div class="skill-block reveal"><h4>✦ Continuous Learning</h4><div class="chips"><span class="chip">Animal Welfare Policy</span><span class="chip">Vintage Healing Aesthetics</span><span class="chip">Human-Animal Bond Research</span><span class="chip">Geriatric Pet Medicine</span></div></div>
</section>

<!-- CONTACT -->
<section id="contact">
<span class="section-tag reveal">CHAPTER VI · LETTERS & COLLABORATION</span>
<h2 class="section-title reveal">Write us a <em>slower letter</em></h2>
<p class="section-lead reveal">For therapy appointments, partnerships, or just to say hello — we reply with care.</p>
<div class="envelope reveal"><div class="stamp-img">PET<br/>HEALING<br/>★ ★ ★<br/>POSTAGE<br/>2026</div><div class="env-inner"><h3 style="font-family:'Playfair Display',serif;color:var(--rust);font-size:26px;margin-bottom:6px">To the one who slows down</h3><p style="color:var(--ink-soft);font-size:15px">Reach us through any of these vintage channels:</p><div class="contact-grid"><div class="c-row"><div class="lbl">EMAIL</div><div class="val">hello@pawsandpeace.com</div></div><div class="c-row"><div class="lbl">TELEPHONE</div><div class="val">+86 (10) 6502 8833</div></div><div class="c-row"><div class="lbl">WECHAT OFFICIAL</div><div class="val">PawsAndPeace</div></div><div class="c-row"><div class="lbl">INSTAGRAM / XIAOHONGSHU</div><div class="val">@pawsandpeace</div></div><div class="c-row"><div class="lbl">STUDIO ADDRESS</div><div class="val">Beijing · Shanghai · Chengdu · Guangzhou</div></div><div class="c-row"><div class="lbl">PARTNERSHIP</div><div class="val">partner@pawsandpeace.com</div></div></div><p style="margin-top:30px;font-style:italic;color:var(--ink-soft);font-family:'Playfair Display',serif"> --- Yours, with a soft paw.</p></div></div>
</section>
<footer>© 2026 Paws & Peace · Pet Companion Therapy Station · “Paws for comfort, peace for soul”</footer>
</main>

<div class="paw-trail" id="paw">
<svg viewBox="0 0 64 64"><g fill="#9C4A2A" opacity=".7"><circle cx='20' cy='22' r='6'/><circle cx='44' cy='22' r='6'/><circle cx='12' cy='38' r='5'/><circle cx='52' cy='38' r='5'/><ellipse cx='32' cy='46' rx='14' ry='11'/></g></svg>
</div>

<script>
// ===== 移动端侧边栏控制（全新重写，确保可用）=====
const mobileBtn = document.getElementById('mobileMenuBtn');
const sidebarEl = document.getElementById('sidebar');
const overlay = document.getElementById('mobileOverlay');
const navLinkItems = document.querySelectorAll('#nav a');

function openSidebar() {
  sidebarEl.classList.add('open');
  if (overlay) overlay.classList.add('show');
  document.body.style.overflow = 'hidden';
}

function closeSidebar() {
  sidebarEl.classList.remove('open');
  if (overlay) overlay.classList.remove('show');
  document.body.style.overflow = '';
}

if (mobileBtn) {
  mobileBtn.addEventListener('click', (e) => {
    e.stopPropagation();
    if (sidebarEl.classList.contains('open')) {
      closeSidebar();
    } else {
      openSidebar();
    }
  });
}

if (overlay) {
  overlay.addEventListener('click', closeSidebar);
}

// 点击导航链接后关闭侧边栏（移动端）
navLinkItems.forEach(link => {
  link.addEventListener('click', () => {
    if (window.innerWidth <= 900) {
      closeSidebar();
    }
  });
});

// 窗口大小改变时，如果变成桌面端，强制关闭侧边栏并恢复样式
window.addEventListener('resize', () => {
  if (window.innerWidth > 900) {
    closeSidebar();
  }
});

// ===== active nav on scroll =====
const sections = [...document.querySelectorAll('#home, #story, #services, #map, #journal, #skills, #contact')];
window.addEventListener('scroll', () => {
  const y = window.scrollY + 120;
  let idx = 0;
  sections.forEach((s, i) => {
    if (s && s.offsetTop <= y) idx = i;
  });
  navLinkItems.forEach((a, i) => a.classList.toggle('active', i === idx));
});

// ===== reveal on scroll =====
const io = new IntersectionObserver(entries => {
  entries.forEach(entry => {
    if (entry.isIntersecting) entry.target.classList.add('visible');
  });
}, { threshold: 0.12 });
document.querySelectorAll('.reveal').forEach(el => io.observe(el));

// ===== paw trail (desktop only) =====
const paw = document.getElementById('paw');
let ptTimer = 0;
window.addEventListener('mousemove', e => {
  if (window.innerWidth <= 768) return;
  paw.style.left = (e.clientX - 12) + 'px';
  paw.style.top = (e.clientY + 14) + 'px';
  paw.style.opacity = '0.85';
  clearTimeout(ptTimer);
  ptTimer = setTimeout(() => paw.style.opacity = '0', 400);
});

// ========== LEALFET MAP with cat paw markers ==========
const cities = [
  { name: 'Beijing', lat: 39.9042, lng: 116.4074, desc: 'Headquarters · Healing Living Room', addr: 'Dongcheng District · Vintage alleyway space' },
  { name: 'Shanghai', lat: 31.2304, lng: 121.4737, desc: 'Collaboration Studio', addr: 'Jing’an District · French Concession garden house' },
  { name: 'Chengdu', lat: 30.5728, lng: 104.0668, desc: 'Therapy Dog Training Base', addr: 'Qingyang District · Courtyard training field' },
  { name: 'Shenzhen', lat: 22.5431, lng: 114.0579, desc: 'Creative Content Studio', addr: 'Nanshan District · Renovated seafront factory' },
  { name: 'Guangzhou', lat: 23.1291, lng: 113.2644, desc: 'AAI Hospital Partner', addr: 'Yuexiu District · Pediatric ward' },
  { name: 'Hangzhou', lat: 30.2741, lng: 120.1551, desc: 'Seasonal Pop-up Point', addr: 'West Lake District · Mountain retreat' }
];

const catPawSVG = `<svg viewBox="0 0 64 64" xmlns="http://www.w3.org/2000/svg" width="30" height="30">
  <g fill="#9C4A2A" stroke="#5e2e1a" stroke-width="0.8">
    <path d="M32 44c-8 0-12-4-12-9 0-4 3-7 6-8 1 0 3-0.5 6-0.5 3 0 5 0.5 6 0.5 3 1 6 4 6 8 0 5-4 9-12 9z" />
    <ellipse cx="20" cy="28" rx="4.5" ry="6"/>
    <ellipse cx="44" cy="28" rx="4.5" ry="6"/>
    <ellipse cx="27" cy="20" rx="4" ry="5.5"/>
    <ellipse cx="37" cy="20" rx="4" ry="5.5"/>
  </g>
</svg>`;

const map = L.map('vintageMap').setView([35.0, 108.0], 5);
L.tileLayer('https://{s}.basemaps.cartocdn.com/light_all/{z}/{x}/{y}{r}.png', {
  attribution: '© <a href="https://www.openstreetmap.org/copyright">OSM</a> & CartoDB',
  subdomains: 'abcd',
  maxZoom: 12,
  minZoom: 4
}).addTo(map);

cities.forEach(c => {
  L.circle([c.lat, c.lng], {
    color: '#C89F6E',
    weight: 1.5,
    fillColor: '#E0BB84',
    fillOpacity: 0.25,
    radius: 60000,
    interactive: false
  }).addTo(map);
  
  const catIcon = L.divIcon({
    className: 'paw-marker',
    html: `<div style="width:44px;height:44px;display:flex;align-items:center;justify-content:center;background:#FDF8EE;border:2px solid #9C4A2A;border-radius:50%;box-shadow:2px 2px 0 rgba(59,42,30,.25);">${catPawSVG}</div>`,
    iconSize: [44, 44],
    iconAnchor: [22, 22],
    popupAnchor: [0, -24]
  });
  L.marker([c.lat, c.lng], { icon: catIcon })
    .addTo(map)
    .bindPopup(`
      <div class="pop-name">🐱 ${c.name}</div>
      <div class="pop-desc">${c.desc}</div>
      <div class="pop-addr">${c.addr}</div>
    `, { maxWidth: 240 });
});

window.addEventListener('resize', () => setTimeout(() => map.invalidateSize(), 100));
window.addEventListener('load', () => setTimeout(() => map.invalidateSize(), 200));

const mapContainer = document.getElementById('vintageMap');
if (mapContainer) {
  const resizeObserver = new ResizeObserver(() => map.invalidateSize());
  resizeObserver.observe(mapContainer);
}

const tagWrap = document.getElementById('cityTags');
cities.forEach(c => {
  const t = document.createElement('span');
  t.className = 'tag';
  t.textContent = '🐾 ' + c.name + ' · ' + c.desc;
  tagWrap.appendChild(t);
});
</script>
</body>
</html>
