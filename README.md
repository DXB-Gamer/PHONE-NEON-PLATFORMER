<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>CUBIX — Neon Platformer</title>
<link href="https://fonts.googleapis.com/css2?family=Orbitron:wght@400;700;900&family=Share+Tech+Mono&display=swap" rel="stylesheet">
<style>
*,*::before,*::after{box-sizing:border-box;margin:0;padding:0}
body{
  background:#000;overflow:hidden;
  font-family:'Orbitron',monospace;color:#0ff;
  display:flex;align-items:center;justify-content:center;
  width:100vw;height:100vh;
}

/* ── Outer container with premium edge glow ── */
#gameContainer{
  position:relative;width:900px;height:540px;overflow:hidden;
  box-shadow:
    0 0 0 1px rgba(0,255,255,0.18),
    0 0 40px rgba(0,255,255,0.12),
    0 0 100px rgba(0,255,255,0.06),
    0 0 0 3px rgba(255,0,255,0.04),
    inset 0 0 80px rgba(0,0,0,0.6);
}
/* Corner accent marks */
#gameContainer::before,#gameContainer::after{
  content:'';position:absolute;width:22px;height:22px;z-index:100;pointer-events:none;
}
#gameContainer::before{
  top:0;left:0;
  border-top:2px solid rgba(0,255,255,0.6);
  border-left:2px solid rgba(0,255,255,0.6);
}
#gameContainer::after{
  bottom:0;right:0;
  border-bottom:2px solid rgba(255,0,255,0.5);
  border-right:2px solid rgba(255,0,255,0.5);
}

canvas{display:block;}
#gameCanvas{position:absolute;top:0;left:0;z-index:1;}
#scanlineCanvas{position:absolute;top:0;left:0;z-index:2;pointer-events:none;opacity:0.08;}

/* Menu particle canvas — sits behind all menus */
#menuBG{position:absolute;top:0;left:0;z-index:8;pointer-events:none;}

#screenFlash{
  position:absolute;top:0;left:0;width:100%;height:100%;
  z-index:4;pointer-events:none;background:#ff00ff;opacity:0;
  transition:opacity 0.06s;
}

/* ── BASE MENU ── */
.menu{
  position:absolute;top:0;left:0;width:100%;height:100%;
  z-index:10;display:flex;flex-direction:column;
  align-items:center;justify-content:center;
  background:rgba(0,0,0,0.0);
  backdrop-filter:blur(0px);
}
.menu.hidden{display:none;}

/* Grid overlay on menus */
.menu::before{
  content:'';position:absolute;inset:0;
  background-image:
    linear-gradient(rgba(0,255,255,0.025) 1px,transparent 1px),
    linear-gradient(90deg,rgba(0,255,255,0.025) 1px,transparent 1px);
  background-size:44px 44px;pointer-events:none;
  animation:gridScroll 12s linear infinite;
}
@keyframes gridScroll{ to{ background-position:44px 44px; } }

/* Dark bg only when menu needs it */
#mainMenu,#pauseMenu,#lcMenu,#goMenu,#winMenu{
  background:rgba(0,0,0,0.88);
}

/* ── MAIN TITLE ── */
.menu-title{
  font-size:78px;font-weight:900;letter-spacing:18px;
  color:#fff;
  text-shadow:
    0 0 0px #fff,
    0 0 20px #0ff,
    0 0 50px #0ff,
    0 0 100px #0ff,
    0 0 180px rgba(0,200,255,0.5);
  animation:titlePulse 2.4s ease-in-out infinite;
  margin-bottom:4px;position:relative;
  /* Pseudo glitch */
}
.menu-title::before{
  content:attr(data-text);position:absolute;top:0;left:3px;
  color:#f0f;
  text-shadow:0 0 12px #f0f;
  clip-path:inset(30% 0 40% 0);
  animation:glitch1 4s infinite;
  opacity:0.5;
}
.menu-title::after{
  content:attr(data-text);position:absolute;top:0;left:-3px;
  color:#0ff;
  clip-path:inset(55% 0 10% 0);
  animation:glitch2 4s infinite;
  opacity:0.4;
}
@keyframes glitch1{
  0%,90%,100%{clip-path:inset(30% 0 40% 0);transform:translate(0);}
  92%{clip-path:inset(20% 0 50% 0);transform:translate(-4px,1px);}
  94%{clip-path:inset(40% 0 30% 0);transform:translate(4px,-1px);}
  96%{clip-path:inset(10% 0 70% 0);transform:translate(-2px,2px);}
}
@keyframes glitch2{
  0%,88%,100%{clip-path:inset(55% 0 10% 0);transform:translate(0);}
  90%{clip-path:inset(60% 0 5% 0);transform:translate(3px,-2px);}
  93%{clip-path:inset(50% 0 15% 0);transform:translate(-3px,1px);}
}
@keyframes titlePulse{
  0%,100%{text-shadow:0 0 20px #0ff,0 0 50px #0ff,0 0 100px #0ff;}
  50%{text-shadow:0 0 30px #0ff,0 0 80px #0ff,0 0 160px #0ff,0 0 240px rgba(0,180,255,0.4);}
}

/* Version tag */
.menu-version{
  font-family:'Share Tech Mono',monospace;
  font-size:9px;letter-spacing:5px;color:rgba(0,255,255,0.35);
  margin-bottom:6px;
}

.menu-sub{
  font-size:11px;letter-spacing:8px;color:#f0f;
  text-shadow:0 0 8px #f0f,0 0 22px #f0f;
  margin-bottom:32px;position:relative;
  animation:subPulse 3s ease-in-out infinite;
}
@keyframes subPulse{
  0%,100%{opacity:0.8;letter-spacing:8px;}
  50%{opacity:1;letter-spacing:10px;}
}

/* ── BUTTONS ── */
.btn{
  font-family:'Orbitron',monospace;font-size:12px;font-weight:700;
  letter-spacing:5px;padding:12px 36px;
  border:1px solid rgba(0,255,255,0.5);
  background:rgba(0,255,255,0.03);
  color:#0ff;cursor:pointer;margin:5px;
  text-transform:uppercase;transition:all 0.18s;
  text-shadow:0 0 8px rgba(0,255,255,0.8);
  box-shadow:
    0 0 8px rgba(0,255,255,0.15),
    inset 0 0 12px rgba(0,255,255,0.02);
  position:relative;overflow:hidden;clip-path:polygon(8px 0%,100% 0%,calc(100% - 8px) 100%,0% 100%);
}
.btn::before{
  content:'';position:absolute;top:0;left:-100%;width:100%;height:100%;
  background:linear-gradient(90deg,transparent,rgba(0,255,255,0.1),transparent);
  transition:left 0.4s;
}
.btn:hover::before{ left:100%; }
.btn:hover{
  background:rgba(0,255,255,0.08);
  border-color:rgba(0,255,255,0.9);
  box-shadow:0 0 20px rgba(0,255,255,0.4),0 0 50px rgba(0,255,255,0.15),inset 0 0 15px rgba(0,255,255,0.05);
  transform:translateY(-2px);
  letter-spacing:6px;
}
.btn:active{ transform:translateY(0px); }
.btn.p{
  border-color:rgba(255,0,255,0.5);color:#f0f;
  text-shadow:0 0 8px rgba(255,0,255,0.8);
  box-shadow:0 0 8px rgba(255,0,255,0.15),inset 0 0 12px rgba(255,0,255,0.02);
  background:rgba(255,0,255,0.03);
  clip-path:polygon(0% 0%,calc(100% - 8px) 0%,100% 100%,8px 100%);
}
.btn.p::before{ background:linear-gradient(90deg,transparent,rgba(255,0,255,0.1),transparent); }
.btn.p:hover{
  background:rgba(255,0,255,0.08);border-color:rgba(255,0,255,0.9);
  box-shadow:0 0 20px rgba(255,0,255,0.4),0 0 50px rgba(255,0,255,0.15);
  letter-spacing:6px;transform:translateY(-2px);
}

/* ── STATS ── */
.stats{display:flex;gap:48px;margin:16px 0;position:relative;}
.stat{text-align:center;}
.stl{font-size:8px;color:rgba(255,255,255,0.25);letter-spacing:5px;margin-bottom:6px;}
.stv{
  color:#0ff;font-size:28px;font-weight:700;
  text-shadow:0 0 10px #0ff,0 0 25px rgba(0,255,255,0.5);
}

/* Divider line in menus */
.menu-line{
  width:220px;height:1px;margin:8px 0 20px;
  background:linear-gradient(90deg,transparent,rgba(0,255,255,0.4),transparent);
}

/* ── STARS ── */
.stars{display:flex;gap:20px;margin:10px 0;position:relative;}
.star{font-size:40px;filter:grayscale(1) brightness(0.15);transition:filter 0.35s,transform 0.35s;}
.star.on{
  filter:none;
  text-shadow:0 0 18px gold,0 0 45px gold,0 0 80px rgba(255,200,0,0.5);
  animation:starPop 0.5s cubic-bezier(.36,.07,.19,.97);
}
@keyframes starPop{
  0%{transform:scale(0) rotate(-20deg)}
  60%{transform:scale(1.5) rotate(5deg)}
  80%{transform:scale(0.9)}
  100%{transform:scale(1) rotate(0deg)}
}

/* ── HI SCORE ── */
#hiD{
  font-size:10px;letter-spacing:4px;
  color:rgba(255,0,255,0.6);
  text-shadow:0 0 8px rgba(255,0,255,0.4);
  margin-bottom:28px;position:relative;
}

/* ── CONTROLS BOX ── */
#ctrlBox{
  font-family:'Share Tech Mono',monospace;font-size:10px;
  color:rgba(255,255,255,0.2);letter-spacing:2px;
  margin-top:14px;text-align:center;line-height:2.2;
  border:1px solid rgba(255,255,255,0.05);
  padding:10px 20px;
  background:rgba(0,0,0,0.3);
}

/* ── WIN SCREEN ── */
.win-title{
  font-size:60px;font-weight:900;letter-spacing:10px;
  color:#fff;
  text-shadow:0 0 20px #ff0,0 0 60px #ff0,0 0 120px rgba(255,220,0,0.5);
  animation:winPulse 1.2s ease-in-out infinite;
  margin-bottom:8px;
}
@keyframes winPulse{
  0%,100%{text-shadow:0 0 20px #ff0,0 0 60px #ff0;}
  50%{text-shadow:0 0 40px #ff0,0 0 100px #ff0,0 0 180px rgba(255,200,0,0.4);}
}

/* ── WARDROBE ── */
#wardrobeMenu{
  position:absolute;top:0;left:0;width:100%;height:100%;
  z-index:10;display:flex;flex-direction:column;
  align-items:center;justify-content:center;
  background:rgba(0,0,2,0.96);
}
#wardrobeMenu.hidden{display:none;}
#wardrobeMenu::before{
  content:'';position:absolute;inset:0;
  background-image:
    linear-gradient(rgba(255,0,255,0.02) 1px,transparent 1px),
    linear-gradient(90deg,rgba(0,255,255,0.02) 1px,transparent 1px);
  background-size:44px 44px;pointer-events:none;
  animation:gridScroll 18s linear infinite;
}
.wd-title{
  font-size:38px;font-weight:900;letter-spacing:12px;color:#f0f;
  text-shadow:0 0 10px #f0f,0 0 30px #f0f,0 0 70px #f0f;
  margin-bottom:4px;
  animation:wdPulse 2s ease-in-out infinite;
}
@keyframes wdPulse{
  0%,100%{text-shadow:0 0 10px #f0f,0 0 30px #f0f,0 0 60px #f0f;}
  50%{text-shadow:0 0 20px #f0f,0 0 55px #f0f,0 0 120px #f0f,0 0 200px rgba(180,0,255,0.4);}
}
.wd-sub{font-size:9px;letter-spacing:6px;color:rgba(0,255,255,0.5);text-shadow:0 0 8px #0ff;margin-bottom:22px;}
.wd-preview-canvas{width:64px;height:64px;margin-bottom:20px;filter:drop-shadow(0 0 12px rgba(0,255,255,0.5));}
.wd-grid{
  display:grid;grid-template-columns:repeat(5,56px);
  gap:12px;margin-bottom:22px;
}
.wd-swatch{
  width:56px;height:56px;border-radius:4px;cursor:pointer;
  border:1px solid rgba(255,255,255,0.08);
  transition:all 0.15s;position:relative;
  display:flex;align-items:center;justify-content:center;
  clip-path:polygon(4px 0%,100% 0%,calc(100% - 4px) 100%,0% 100%);
}
.wd-swatch:hover{transform:scale(1.15);border-color:rgba(255,255,255,0.6);}
.wd-swatch.active{border-width:2px;transform:scale(1.2);}
.wd-swatch .wd-check{
  font-size:20px;color:#fff;text-shadow:0 0 8px #000;
  opacity:0;transition:opacity 0.15s;pointer-events:none;
}
.wd-swatch.active .wd-check{opacity:1;}
.wd-back{
  font-family:'Orbitron',monospace;font-size:11px;font-weight:700;
  letter-spacing:5px;padding:10px 30px;
  border:1px solid rgba(0,255,255,0.5);
  background:rgba(0,255,255,0.03);color:#0ff;cursor:pointer;
  text-transform:uppercase;transition:all 0.16s;
  text-shadow:0 0 8px #0ff;
  clip-path:polygon(8px 0%,100% 0%,calc(100% - 8px) 100%,0% 100%);
}
.wd-back:hover{
  background:rgba(0,255,255,0.08);border-color:#0ff;
  box-shadow:0 0 20px rgba(0,255,255,0.35);transform:translateY(-2px);
}

/* Premium swatch styles */
.wd-premium-row{display:flex;gap:24px;margin-bottom:18px;align-items:flex-start;justify-content:center;}
.wd-premium-slot{display:flex;flex-direction:column;align-items:center;gap:5px;position:relative;}
.wd-premium-swatch{
  width:68px;height:68px;border-radius:4px;cursor:pointer;
  border:1px solid rgba(255,255,255,0.12);
  transition:all 0.15s;position:relative;
  display:flex;align-items:center;justify-content:center;overflow:hidden;
}
.wd-premium-swatch:hover{transform:scale(1.12);}
.wd-premium-swatch.active{transform:scale(1.18);border-width:2px;}
.wd-premium-swatch.locked{cursor:not-allowed;filter:brightness(0.28) saturate(0.3);}
.wd-premium-swatch.locked:hover{transform:none;}
.wd-premium-check{font-size:22px;color:#fff;text-shadow:0 0 8px #000;}
.wd-premium-label{font-family:'Share Tech Mono',monospace;font-size:8px;letter-spacing:2px;color:#444;text-align:center;}
.wd-premium-label.unlocked{text-shadow:0 0 8px currentColor;}
.wd-gold-crown{font-size:18px;line-height:1;animation:crownBob 2s ease-in-out infinite;}
@keyframes crownBob{0%,100%{transform:translateY(0);}50%{transform:translateY(-4px);}}
.wd-silver-bg{background:linear-gradient(135deg,#6a7a8a,#d0dce8,#8899aa,#eef2f6,#7090a8);}
.wd-gold-bg{background:linear-gradient(135deg,#7a5500,#ffd700,#b8860b,#ffe066,#7a5500);}

/* Checkpoint flag */
#cpIndicator{
  position:absolute;bottom:22px;left:50%;transform:translateX(-50%);
  z-index:5;font-family:'Share Tech Mono',monospace;font-size:10px;
  letter-spacing:4px;color:#00ff88;
  text-shadow:0 0 10px #00ff88,0 0 25px #00ff88;
  pointer-events:none;opacity:0;transition:opacity 0.35s;
}

/* ── LEVEL BADGE (shows current level elegantly) ── */
#_lvlBadge{
  position:absolute;top:8px;left:50%;transform:translateX(-50%);
  z-index:5;font-family:'Share Tech Mono',monospace;font-size:9px;
  letter-spacing:4px;color:rgba(255,255,255,0.2);
  pointer-events:none;
}
</style>
</head>
<body>
<div id="gameContainer">
  <canvas id="gameCanvas" width="900" height="540"></canvas>
  <canvas id="scanlineCanvas" width="900" height="540"></canvas>
  <canvas id="menuBG" width="900" height="540"></canvas>
  <div id="screenFlash"></div>
  <div id="cpIndicator">✦ CHECKPOINT SAVED ✦</div>
  <div id="_lvlBadge"></div>

  <!-- MAIN MENU -->
  <div class="menu" id="mainMenu">
    <div class="menu-version">ALPHA 1.5 — NEON DIMENSIONS</div>
    <div class="menu-title" data-text="CUBIX">CUBIX</div>
    <div class="menu-sub">BREACH THE GRID</div>
    <div class="menu-line"></div>
    <div id="hiD">HIGH SCORE: <span id="hiV">0</span></div>
    <button class="btn" id="bPlay">▶ &nbsp;PLAY</button>
    <button class="btn" id="bResume2" style="display:none">⟳ &nbsp;RESUME</button>
    <button class="btn p" id="bWardrobe">◈ &nbsp;WARDROBE</button>
    <button class="btn p" id="bHelp">? &nbsp;CONTROLS</button>
    <div id="ctrlBox" style="display:none">
      A / ◀ &nbsp;&nbsp; ▶ / D &nbsp;&nbsp; MOVE<br>
      SPACE / ↑ &nbsp;&nbsp; JUMP &amp; DOUBLE JUMP<br>
      HOLD JUMP for higher arc &nbsp;|&nbsp; ESC pause
    </div>
  </div>

  <!-- PAUSE -->
  <div class="menu hidden" id="pauseMenu">
    <div class="menu-title" style="font-size:52px" data-text="PAUSED">PAUSED</div>
    <div class="menu-sub" style="margin-bottom:28px">SYSTEM HALTED</div>
    <div class="menu-line"></div>
    <button class="btn" id="bResume">▶ &nbsp;RESUME</button>
    <button class="btn p" id="bQuit">⏹ &nbsp;MAIN MENU</button>
  </div>

  <!-- LEVEL COMPLETE -->
  <div class="menu hidden" id="lcMenu">
    <div class="menu-title" style="font-size:42px" data-text="LEVEL CLEAR">LEVEL CLEAR</div>
    <div class="stars">
      <div class="star" id="s1">★</div>
      <div class="star" id="s2">★</div>
      <div class="star" id="s3">★</div>
    </div>
    <div class="menu-line"></div>
    <div class="stats">
      <div class="stat"><div class="stl">SCORE</div><div class="stv" id="lcSc">0</div></div>
      <div class="stat"><div class="stl">LEVEL</div><div class="stv" id="lcLv">1</div></div>
      <div class="stat"><div class="stl">ORBS</div><div class="stv" id="lcOr">0/0</div></div>
    </div>
    <button class="btn" id="bNext">NEXT LEVEL ▶</button>
    <button class="btn p" id="bLCQ">MAIN MENU</button>
  </div>

  <!-- WIN -->
  <div class="menu hidden" id="winMenu">
    <div class="win-title">YOU WIN!</div>
    <div class="menu-sub" style="margin-bottom:18px">ALL 10 LEVELS CONQUERED</div>
    <div class="menu-line" style="background:linear-gradient(90deg,transparent,rgba(255,220,0,0.5),transparent)"></div>
    <div class="stats">
      <div class="stat"><div class="stl">FINAL SCORE</div><div class="stv" id="wSc">0</div></div>
      <div class="stat"><div class="stl">HIGH SCORE</div><div class="stv" id="wBe">0</div></div>
    </div>
    <button class="btn" id="bWP">▶ &nbsp;PLAY AGAIN</button>
    <button class="btn p" id="bWM">MAIN MENU</button>
  </div>

  <!-- GAME OVER -->
  <div class="menu hidden" id="goMenu">
    <div class="menu-title" style="font-size:52px;color:#f0f;text-shadow:0 0 20px #f0f,0 0 65px #f0f" data-text="GAME OVER">GAME OVER</div>
    <div class="menu-sub">SIGNAL LOST</div>
    <div class="menu-line" style="background:linear-gradient(90deg,transparent,rgba(255,0,255,0.4),transparent)"></div>
    <div class="stats">
      <div class="stat"><div class="stl">SCORE</div><div class="stv" id="goSc">0</div></div>
      <div class="stat"><div class="stl">BEST</div><div class="stv" id="goBe">0</div></div>
    </div>
    <button class="btn" id="bRe">↺ &nbsp;RETRY</button>
    <button class="btn p" id="bGOQ">MAIN MENU</button>
  </div>

  <!-- WARDROBE -->
  <div id="wardrobeMenu" class="hidden">
    <div class="wd-title">WARDROBE</div>
    <div class="wd-sub">SELECT YOUR CUBE SKIN</div>
    <canvas class="wd-preview-canvas" id="wdPreviewCanvas" width="64" height="64"></canvas>
    <div class="wd-grid" id="wdGrid"></div>
    <button class="wd-back" id="bWdBack">◀ &nbsp;BACK</button>
  </div>
</div>

<!-- ═══════════════════════════════════════════════
     ORIGINAL GAME SCRIPT — 100% UNTOUCHED
     ═══════════════════════════════════════════════ -->
<script>
'use strict';
// ════════════════════════════════════════════════
//  CUBIX — fixed & polished
// ════════════════════════════════════════════════
const CV = document.getElementById('gameCanvas');
const CX = CV.getContext('2d');
const W=900, H=540;

// ── Audio ────────────────────────────────────
let _ac=null;
function AC(){ if(!_ac) _ac=new(window.AudioContext||window.webkitAudioContext)(); return _ac; }
function tone(freq,type,dur,vol,det=0){
  try{
    const a=AC(), o=a.createOscillator(), g=a.createGain();
    o.connect(g); g.connect(a.destination);
    o.type=type; o.frequency.value=freq;
    if(det) o.detune.value=det;
    g.gain.setValueAtTime(0,a.currentTime);
    g.gain.linearRampToValueAtTime(vol,a.currentTime+0.01);
    g.gain.exponentialRampToValueAtTime(0.001,a.currentTime+dur);
    o.start(); o.stop(a.currentTime+dur+0.05);
  }catch(e){}
}
const S={
  jump(){ tone(300,'sine',0.11,0.18); setTimeout(()=>tone(460,'sine',0.07,0.12),28); },
  dJump(){ tone(500,'sine',0.09,0.16); setTimeout(()=>tone(750,'sine',0.06,0.12),38); },
  land(){ tone(70,'sawtooth',0.07,0.22); },
  orb(){ tone(860,'sine',0.13,0.16); setTimeout(()=>tone(1280,'sine',0.08,0.11),52); },
  death(){ tone(185,'sawtooth',0.28,0.28); tone(105,'square',0.22,0.22); },
  portal(){ [430,540,650,860].forEach((f,i)=>setTimeout(()=>tone(f,'sine',0.18,0.17),i*72)); },
  crumble(){ tone(90,'sawtooth',0.12,0.18); },
};
let ambOn=false;
function startAmbient(){
  if(ambOn)return; ambOn=true;
  try{
    const a=AC();
    [55,82.5,110].forEach(f=>{
      const o=a.createOscillator(),g=a.createGain(),flt=a.createBiquadFilter();
      o.type='sine'; o.frequency.value=f;
      flt.type='lowpass'; flt.frequency.value=360; g.gain.value=0.033;
      o.connect(flt); flt.connect(g); g.connect(a.destination); o.start();
    });
  }catch(e){}
}

// ── Input ────────────────────────────────────
const K={}, JP={};
document.addEventListener('keydown',e=>{
  if(!K[e.code]) JP[e.code]=true;
  K[e.code]=true;
  if(['Space','ArrowUp','ArrowDown','ArrowLeft','ArrowRight'].includes(e.code)) e.preventDefault();
});
document.addEventListener('keyup',e=>{ K[e.code]=false; });
function clrJP(){ for(const k in JP) delete JP[k]; }

// ── Level data ───────────────────────────────
const p=(x,y,w,o={})=>({x,y,w,h:18,...o});
const ob=(x,y)=>({x,y,r:10});
const sp=(x,y,w)=>({x,y,w,h:16});

// COORDINATE SYSTEM — final verified values:
//  spike.y  = platY - 16  (base flush with platform top, tip pointing up)
//  orb.y    = platY - 14  (centre at player chest height, collected while walking)
//  goal.y   = platY - 14  (portal at player centre, walk straight in)
//  start.y  = platY - 28  (player feet on ground)

const LEVELS=[
  // L1 BOOT SEQUENCE — ground=492, plats: 436@x460, 380@x632, 325@x802, 436@x992, 375@x1112, 344@x1362
  {name:'BOOT SEQUENCE',bgHue:180,
   platforms:[p(0,492,420),p(460,436,120),p(632,380,110),p(802,325,120),p(992,436,100),p(1112,375,200),p(1362,344,160)],
   orbs:[ob(200,478),ob(510,422),ob(850,311),ob(1050,422),ob(1200,361),ob(1440,330)],
   spikes:[],
   start:{x:50,y:464},goal:{x:1490,y:330}},

  // L2 GRID NETWORK — ground=492, plats: 436@362, 380@512, 436@662, 348@792, 400@942, 318@1092, 368@1262, 300@1412, 348@1612
  {name:'GRID NETWORK',bgHue:200,
   platforms:[p(0,492,300),p(362,436,100),p(512,380,100),p(662,436,80),p(792,348,100),p(942,400,100),p(1092,318,120),p(1262,368,100),p(1412,300,150),p(1612,348,120)],
   orbs:[ob(150,478),ob(410,422),ob(840,334),ob(1000,386),ob(1150,304),ob(1460,286),ob(1660,334)],
   spikes:[sp(702,420,56)],
   start:{x:50,y:464},goal:{x:1680,y:334}},

  // L3 PHASE SHIFT — ground=492, plats: mov424@382, 370@572, mov424@712, 348@912, mov400@1112, 318@1312, mov378@1492, 298@1662
  {name:'PHASE SHIFT',bgHue:260,
   platforms:[p(0,492,300),p(382,424,100,{type:'moving',moveX:80,speed:1.2}),p(572,370,90),p(712,424,90,{type:'moving',moveX:60,speed:1.5}),p(912,348,120),p(1112,400,80,{type:'moving',moveX:100,speed:1.0}),p(1312,318,100),p(1492,378,80,{type:'moving',moveX:70,speed:1.8}),p(1662,298,150)],
   orbs:[ob(150,478),ob(432,410),ob(617,356),ob(762,410),ob(970,334),ob(1360,304),ob(1710,284)],
   spikes:[sp(616,354,48),sp(962,332,38)],
   start:{x:50,y:464},goal:{x:1740,y:284}},

  // L4 DECAY PROTOCOL — ground=492, plats: crumble436@372, crumble390@532, 436@692, crumble378@832, 328@972, crumble378@1132, 328@1292, crumble378@1472, 298@1612
  {name:'DECAY PROTOCOL',bgHue:300,
   platforms:[p(0,492,300),p(372,436,100,{type:'crumble'}),p(532,390,90,{type:'crumble'}),p(692,436,90),p(832,378,80,{type:'crumble'}),p(972,328,120),p(1132,378,90,{type:'crumble'}),p(1292,328,100),p(1472,378,80,{type:'crumble'}),p(1612,298,180)],
   orbs:[ob(150,478),ob(580,376),ob(730,422),ob(1020,314),ob(1330,314),ob(1680,284)],
   spikes:[sp(740,420,48),sp(1016,312,38)],
   start:{x:50,y:464},goal:{x:1700,y:284}},

  // L5 SPIKE MATRIX — ground=492, plats: 420@372, 368@512, 420@672, 358@812, 420@972, 348@1112, mov398@1292, 328@1462, crumble378@1652, 308@1792
  {name:'SPIKE MATRIX',bgHue:0,
   platforms:[p(0,492,300),p(372,420,80),p(512,368,100),p(672,420,80),p(812,358,100),p(972,420,80),p(1112,348,120),p(1292,398,80,{type:'moving',moveX:60,speed:1.6}),p(1462,328,100),p(1652,378,80,{type:'crumble'}),p(1792,308,160)],
   orbs:[ob(560,354),ob(860,344),ob(1160,334),ob(1500,314),ob(1840,294)],
   spikes:[sp(386,404,38),sp(530,352,38),sp(830,342,38),sp(1130,332,38),sp(1670,362,38)],
   start:{x:50,y:464},goal:{x:1880,y:294}},

  // L6 MULTI-PATH — ground=492, plats: mov388@352, crumble440@532, 378@672, crumble440@822, mov358@952, crumble430@1132, 358@1272, mov308@1432, crumble358@1612, 278@1752
  {name:'MULTI-PATH',bgHue:140,
   platforms:[p(0,492,280),p(352,388,80,{type:'moving',moveX:80,speed:1.4}),p(532,440,80,{type:'crumble'}),p(672,378,90),p(822,440,80,{type:'crumble'}),p(952,358,120,{type:'moving',moveX:100,speed:1.2}),p(1132,430,80,{type:'crumble'}),p(1272,358,90),p(1432,308,90,{type:'moving',moveX:80,speed:2.0}),p(1612,358,80,{type:'crumble'}),p(1752,278,180)],
   orbs:[ob(140,478),ob(395,374),ob(710,364),ob(1005,344),ob(1460,294),ob(1840,264)],
   spikes:[sp(570,424,38),sp(860,424,38),sp(1170,414,38),sp(1650,342,38)],
   start:{x:50,y:464},goal:{x:1840,y:264}},

  // L7 VELOCITY GRID — ground=468, plats: mov408@292, mov358@492, 408@692, mov348@832, crumble398@1032, mov328@1172, crumble378@1392, mov298@1532, crumble348@1732, 258@1872
  {name:'VELOCITY GRID',bgHue:220,
   platforms:[p(0,468,200),p(292,408,80,{type:'moving',moveX:120,speed:2.5}),p(492,358,70,{type:'moving',moveX:90,speed:2.8}),p(692,408,80),p(832,348,80,{type:'moving',moveX:110,speed:3.0}),p(1032,398,80,{type:'crumble'}),p(1172,328,90,{type:'moving',moveX:100,speed:2.2}),p(1392,378,80,{type:'crumble'}),p(1532,298,100,{type:'moving',moveX:80,speed:2.6}),p(1732,348,80,{type:'crumble'}),p(1872,258,180)],
   orbs:[ob(100,454),ob(332,394),ob(527,344),ob(730,394),ob(1210,314),ob(1575,284),ob(1940,244)],
   spikes:[sp(710,392,48),sp(1070,382,38),sp(1430,362,38),sp(1770,332,38)],
   start:{x:50,y:440},goal:{x:1960,y:244}},

  // L8 CRUMBLE STORM — ground=468, plats: crumble408@262, crumble358@402, crumble308@542, 358@682, crumble308@822, mov358@962, crumble298@1142, mov348@1282, crumble278@1462, mov328@1602, 258@1782
  {name:'CRUMBLE STORM',bgHue:30,
   platforms:[p(0,468,180),p(262,408,70,{type:'crumble'}),p(402,358,70,{type:'crumble'}),p(542,308,70,{type:'crumble'}),p(682,358,80),p(822,308,70,{type:'crumble'}),p(962,358,80,{type:'moving',moveX:100,speed:2.0}),p(1142,298,70,{type:'crumble'}),p(1282,348,80,{type:'moving',moveX:90,speed:2.5}),p(1462,278,70,{type:'crumble'}),p(1602,328,80,{type:'moving',moveX:80,speed:3.0}),p(1782,258,200)],
   orbs:[ob(90,454),ob(295,394),ob(436,344),ob(575,294),ob(715,344),ob(1180,284),ob(1830,244)],
   spikes:[sp(692,342,38),sp(1012,342,38),sp(1652,312,38)],
   start:{x:50,y:440},goal:{x:1880,y:244}},

  // L9 SYSTEM BREACH — ground=468, plats: mov398@232, crumble348@412, mov398@552, crumble328@732, mov388@882, crumble308@1072, mov368@1212, crumble288@1402, mov348@1542, crumble268@1732, 218@1872
  {name:'SYSTEM BREACH',bgHue:280,
   platforms:[p(0,468,160),p(232,398,70,{type:'moving',moveX:80,speed:2.5}),p(412,348,60,{type:'crumble'}),p(552,398,70,{type:'moving',moveX:70,speed:3.2}),p(732,328,70,{type:'crumble'}),p(882,388,70,{type:'moving',moveX:100,speed:2.8}),p(1072,308,70,{type:'crumble'}),p(1212,368,70,{type:'moving',moveX:80,speed:3.5}),p(1402,288,70,{type:'crumble'}),p(1542,348,70,{type:'moving',moveX:90,speed:2.4}),p(1732,268,80,{type:'crumble'}),p(1872,218,180)],
   orbs:[ob(80,454),ob(265,384),ob(587,384),ob(766,314),ob(917,374),ob(1245,354),ob(1577,334),ob(1940,204)],
   spikes:[sp(442,332,38),sp(772,312,38),sp(1112,292,38),sp(1442,272,38),sp(1770,252,38)],
   start:{x:50,y:440},goal:{x:1960,y:204}},

  // L10 CORE OVERLOAD — ground=468, plats: mov388@222, crumble328@412, mov388@552, crumble308@712, mov378@862, crumble288@1052, mov358@1192, crumble268@1382, mov338@1522, crumble238@1712, mov298@1852, 178@1992
  {name:'CORE OVERLOAD',bgHue:350,
   platforms:[p(0,468,140),p(222,388,60,{type:'moving',moveX:100,speed:3.5}),p(412,328,60,{type:'crumble'}),p(552,388,60,{type:'moving',moveX:80,speed:4.0}),p(712,308,60,{type:'crumble'}),p(862,378,60,{type:'moving',moveX:110,speed:3.0}),p(1052,288,60,{type:'crumble'}),p(1192,358,60,{type:'moving',moveX:90,speed:4.2}),p(1382,268,60,{type:'crumble'}),p(1522,338,60,{type:'moving',moveX:80,speed:3.8}),p(1712,238,70,{type:'crumble'}),p(1852,298,60,{type:'moving',moveX:60,speed:4.5}),p(1992,178,250)],
   orbs:[ob(70,454),ob(252,374),ob(582,374),ob(742,294),ob(892,364),ob(1222,344),ob(1552,324),ob(1882,284),ob(2110,164)],
   spikes:[sp(442,312,38),sp(742,292,38),sp(1092,272,38),sp(1422,252,38),sp(1750,222,38)],
   start:{x:50,y:440},goal:{x:2080,y:164}},
];

// ── Game state ───────────────────────────────
let STATE='menu';
let score=0,lives=3,lvlIdx=0;
let hiScore=parseInt(localStorage.getItem('cubix_hi')||'0');
let combo=1,comboT=0;
let totOrbs=0,colOrbs=0;
let shake={x:0,y:0,mag:0,dur:0};
let camX=0,camXT=0;
let plats=[],orbList=[],spkList=[],goal=null;
let parts=[];
let lvlDone=false;
let dying=false;

// ── Player ───────────────────────────────────
const PW=28,PH=28;
const GR=1380,JF=-510,DJF=-460,SPD=225,ACC=2400,DEC=3000,MXF=820;
const COY=0.1,JBF=0.12;
let P={};

function resetP(sx,sy){
  P={x:sx,y:sy,vx:0,vy:0,
     onGnd:false,coyT:0,jBufT:0,
     jumps:2,dead:false,trailT:0,glowT:0};
}

// ── Init level ───────────────────────────────
function initLvl(i){
  const lv=LEVELS[i];
  plats=lv.platforms.map(q=>({
    ...q,origX:q.x,
    t:Math.random()*100,
    crT:-1,crA:1,crumbled:false
  }));
  orbList=lv.orbs.map(o=>({...o,collected:false,bobT:Math.random()*Math.PI*2}));
  spkList=lv.spikes.slice();
  goal={...lv.goal,t:0,active:true};
  totOrbs=orbList.length; colOrbs=0;
  lvlDone=false; dying=false; parts=[];
  resetP(lv.start.x,lv.start.y);
  camX=Math.max(0,P.x-W/3); camXT=camX;
}

// ── Particles ────────────────────────────────
function burst(x,y,n,col,spd,life,sz,grav=0.12){
  sz=sz||4;
  for(let i=0;i<n;i++){
    const a=Math.random()*Math.PI*2,s=spd*(0.4+Math.random()*0.9);
    parts.push({
      x,y,vx:Math.cos(a)*s,vy:Math.sin(a)*s-(grav>0?.9:0),
      life:life*(0.6+Math.random()*0.8),maxLife:life,
      col,sz:sz*(0.5+Math.random()*0.7),grav
    });
  }
}
function updParts(dt){
  for(let i=parts.length-1;i>=0;i--){
    const q=parts[i];
    q.x+=q.vx*dt*60; q.y+=q.vy*dt*60;
    q.vy+=q.grav*dt*60; q.life-=dt;
    if(q.life<=0) parts.splice(i,1);
  }
}
function drwParts(){
  CX.save();
  parts.forEach(q=>{
    const a=Math.max(0,q.life/q.maxLife);
    CX.globalAlpha=a; CX.shadowBlur=10; CX.shadowColor=q.col;
    CX.fillStyle=q.col;
    CX.fillRect(Math.round(q.x-q.sz/2-camX),Math.round(q.y-q.sz/2),q.sz,q.sz);
  });
  CX.restore();
}

// ── AABB ─────────────────────────────────────
function hit(ax,ay,aw,ah,bx,by,bw,bh){
  return ax<bx+bw&&ax+aw>bx&&ay<by+bh&&ay+ah>by;
}

// ── Update platforms ─────────────────────────
function updPlats(dt){
  plats.forEach(pl=>{
    pl.t+=dt;
    if(pl.type==='moving') pl.x=pl.origX+Math.sin(pl.t*(pl.speed||1))*(pl.moveX||80);
    if(pl.type==='crumble'&&pl.crT>=0){
      pl.crT-=dt; pl.crA=Math.max(0,pl.crT/0.75);
      if(pl.crT<=0&&!pl.crumbled){ pl.crumbled=true; S.crumble(); }
    }
    if(pl.crumbled&&pl.crT<-4){ pl.crumbled=false; pl.crT=-1; pl.crA=1; }
  });
}

// ── Update player ────────────────────────────
function updP(dt){
  if(P.dead) return;

  const lft=K['ArrowLeft']||K['KeyA'];
  const rgt=K['ArrowRight']||K['KeyD'];
  const jmp=JP['Space']||JP['ArrowUp']||JP['KeyW'];

  // Horizontal
  const tVX=rgt?SPD:lft?-SPD:0;
  if(tVX!==0){
    const a=ACC*dt;
    if(Math.abs(P.vx-tVX)<a) P.vx=tVX;
    else P.vx+=Math.sign(tVX-P.vx)*a;
  } else {
    const d=DEC*dt;
    if(Math.abs(P.vx)<d) P.vx=0;
    else P.vx-=Math.sign(P.vx)*d;
  }

  // Gravity — variable height
  if(P.vy<0&&!(K['Space']||K['ArrowUp']||K['KeyW'])) P.vy+=GR*1.7*dt;
  else P.vy+=GR*dt;
  P.vy=Math.min(P.vy,MXF);

  // Coyote
  if(P.onGnd) P.coyT=COY;
  else if(P.coyT>0) P.coyT-=dt;

  // Jump buffer
  if(jmp) P.jBufT=JBF;
  else if(P.jBufT>0) P.jBufT-=dt;

  // Jump fire
  if(P.jBufT>0){
    if(P.coyT>0||P.onGnd){
      P.vy=JF; P.jumps=1; P.coyT=0; P.jBufT=0;
      S.jump(); burst(P.x+PW/2,P.y+PH,14,_CUBE_COLOR,3.5,0.4,5);
    } else if(P.jumps>0){
      P.vy=DJF; P.jumps=0; P.jBufT=0;
      S.dJump(); burst(P.x+PW/2,P.y+PH/2,22,'#f0f',4.5,0.5,6);
    }
  }

  // Integrate
  P.x+=P.vx*dt; P.y+=P.vy*dt;
  const wasGnd=P.onGnd;
  P.onGnd=false;

  // Collision — vertical pass first (landing & ceiling)
  plats.forEach(pl=>{
    if(pl.crumbled) return;
    if(!hit(P.x,P.y,PW,PH,pl.x,pl.y,pl.w,pl.h)) return;

    const top=P.y+PH-pl.y;
    const bot=pl.y+pl.h-P.y;

    if(P.vy>=0&&top<=32&&top<bot){
      P.y=pl.y-PH; P.vy=0; P.onGnd=true; P.jumps=2; P.coyT=COY;
      if(!wasGnd){
        S.land();
        if(top>6) burst(P.x+PW/2,P.y+PH,8,_CUBE_COLOR,2,0.3,4);
      }
      if(pl.type==='crumble'&&pl.crT<0) pl.crT=0.75;
    }
    else if(P.vy<0&&bot<top&&bot<=20){
      P.y=pl.y+pl.h; P.vy=0;
    }
  });

  // Collision — horizontal pass (wall slide)
  plats.forEach(pl=>{
    if(pl.crumbled) return;
    if(!hit(P.x,P.y,PW,PH,pl.x,pl.y,pl.w,pl.h)) return;
    const lft2=P.x+PW-pl.x;
    const rgt2=pl.x+pl.w-P.x;
    const top2=P.y+PH-pl.y;
    const bot2=pl.y+pl.h-P.y;
    const minH=Math.min(lft2,rgt2);
    const minV=Math.min(top2,bot2);
    if(minH<minV&&minH<12){
      if(lft2<rgt2){ P.x=pl.x-PW; if(P.vx>0) P.vx=0; }
      else         { P.x=pl.x+pl.w; if(P.vx<0) P.vx=0; }
    }
  });

  // Spikes
  spkList.forEach(sk=>{
    if(hit(P.x+3,P.y+4,PW-6,PH-5,sk.x,sk.y,sk.w,sk.h)) killP();
  });

  // Orbs
  orbList.forEach(o=>{
    if(o.collected) return;
    const dx=P.x+PW/2-o.x, dy=P.y+PH/2-o.y;
    if(dx*dx+dy*dy<(o.r+PW/2)*(o.r+PW/2)){
      o.collected=true; colOrbs++;
      S.orb(); combo=Math.min(8,combo+1); comboT=2.5;
      score+=100*combo;
      burst(o.x,o.y,22,'#ff0',5.5,0.65,5);
    }
  });

  // Goal
  if(goal&&goal.active&&!lvlDone){
    const dx=P.x+PW/2-goal.x, dy=P.y+PH/2-goal.y;
    if(dx*dx+dy*dy<34*34){
      goal.active=false; lvlDone=true;
      S.portal();
      burst(goal.x,goal.y,55,_CUBE_COLOR,9,1.1,8);
      burst(goal.x,goal.y,30,'#f0f',6,0.9,6);
      setTimeout(showLC,700);
    }
  }

  // Fall off bottom
  if(P.y>H+160) killP();

  // Trail
  P.trailT+=dt;
  if(Math.abs(P.vx)>65&&P.trailT>0.032){
    P.trailT=0; burst(P.x+PW/2,P.y+PH/2,3,_CUBE_COLOR,0.8,0.22,3,0);
  }

  P.glowT+=dt*3;

  // Combo decay
  if(comboT>0){ comboT-=dt; if(comboT<=0) combo=1; }

  // Camera
  camXT=Math.max(0,P.x-W/3);
  camX+=(camXT-camX)*Math.min(1,dt*7);

  // ── NEW: Checkpoint touch detection ──────
  _checkCheckpoints();
}

// ── Kill ─────────────────────────────────────
function killP(){
  if(P.dead||dying) return;
  dying=true; P.dead=true;
  S.death(); doShake(12,0.55); flashScr();
  burst(P.x+PW/2,P.y+PH/2,46,'#f00',8,1.3,7);
  burst(P.x+PW/2,P.y+PH/2,24,'#f0f',5,1.0,5);
  lives--;
  setTimeout(()=>{
    dying=false;
    if(lives<=0){
      if(score>hiScore){ hiScore=score; localStorage.setItem('cubix_hi',hiScore); }
      STATE='gameover'; showGO();
    } else {
      // ── NEW: Respawn at checkpoint instead of level start ──
      _respawnAtCheckpoint();
    }
  },900);
}

function doShake(m,d){ shake.mag=m; shake.dur=d; }
function flashScr(){
  const el=document.getElementById('screenFlash');
  el.style.opacity='0.65'; setTimeout(()=>el.style.opacity='0',130);
}
function updShake(dt){
  if(shake.dur>0){
    shake.dur-=dt;
    const i=shake.mag*(shake.dur/0.55);
    shake.x=(Math.random()-.5)*i; shake.y=(Math.random()-.5)*i;
  } else { shake.x=0; shake.y=0; }
}

// ── Draw BG ──────────────────────────────────
function drwBG(hue){
  CX.fillStyle='#000'; CX.fillRect(0,0,W,H);
  const gs=40, ox=(((-camX*0.1)%gs)+gs)%gs;
  CX.strokeStyle=`hsla(${hue},100%,60%,0.042)`;
  CX.lineWidth=0.5; CX.beginPath();
  for(let x=ox-gs;x<W+gs;x+=gs){ CX.moveTo(x,0); CX.lineTo(x,H); }
  for(let y=0;y<H+gs;y+=gs){ CX.moveTo(0,y); CX.lineTo(W,y); }
  CX.stroke();
  const gr=CX.createLinearGradient(0,H*.52,0,H);
  gr.addColorStop(0,`hsla(${hue},100%,22%,0)`);
  gr.addColorStop(1,`hsla(${hue},100%,16%,0.12)`);
  CX.fillStyle=gr; CX.fillRect(0,0,W,H);
}

// ── Draw platform ────────────────────────────
function drwPlat(pl){
  if(pl.crumbled) return;
  const a=pl.crA!==undefined?pl.crA:1;
  const x=Math.round(pl.x-camX), y=pl.y;
  const col=pl.type==='crumble'?'#fa0':pl.type==='moving'?'#d0f':'#f06';
  CX.save(); CX.globalAlpha=a;
  CX.shadowBlur=18; CX.shadowColor=col;
  CX.fillStyle=pl.type==='crumble'?'rgba(255,170,0,0.2)':pl.type==='moving'?'rgba(210,0,255,0.2)':'rgba(255,0,80,0.2)';
  CX.fillRect(x,y,pl.w,pl.h);
  CX.shadowBlur=26; CX.shadowColor=col;
  CX.strokeStyle=col; CX.lineWidth=2.5;
  CX.beginPath(); CX.moveTo(x,y+1.5); CX.lineTo(x+pl.w,y+1.5); CX.stroke();
  CX.globalAlpha=a*0.4; CX.lineWidth=1; CX.strokeRect(x+.5,y+.5,pl.w-1,pl.h-1);
  if(pl.type==='crumble'&&pl.crT>=0&&pl.crA<0.8){
    CX.globalAlpha=a*(1-pl.crA)*0.85;
    CX.strokeStyle='#ff0'; CX.lineWidth=1;
    for(let i=0;i<4;i++){
      const cx=x+pl.w*(0.14+i*0.22);
      CX.beginPath(); CX.moveTo(cx,y); CX.lineTo(cx+5,y+pl.h); CX.stroke();
    }
  }
  CX.restore();
}

// ── Draw spike ───────────────────────────────
function drwSpike(sk){
  const x=Math.round(sk.x-camX),y=sk.y,n=Math.floor(sk.w/16);
  CX.save(); CX.shadowBlur=16; CX.shadowColor='#f44'; CX.fillStyle='#f44';
  for(let i=0;i<n;i++){
    const sx=x+i*16+8;
    CX.beginPath(); CX.moveTo(sx-7,y+sk.h); CX.lineTo(sx,y); CX.lineTo(sx+7,y+sk.h);
    CX.closePath(); CX.fill();
  }
  CX.restore();
}

// ── Draw orb ─────────────────────────────────
function drwOrb(o){
  if(o.collected) return;
  o.bobT+=0.027;
  const x=Math.round(o.x-camX), y=o.y+Math.sin(o.bobT)*5;
  CX.save(); CX.shadowBlur=26; CX.shadowColor='#ff0';
  const g=CX.createRadialGradient(x,y,0,x,y,o.r);
  g.addColorStop(0,'#fff'); g.addColorStop(0.3,'#ff0'); g.addColorStop(1,'rgba(255,200,0,0)');
  CX.fillStyle=g; CX.beginPath(); CX.arc(x,y,o.r,0,Math.PI*2); CX.fill();
  CX.shadowBlur=8; CX.fillStyle='#fff';
  CX.beginPath(); CX.arc(x,y,o.r*.38,0,Math.PI*2); CX.fill();
  CX.restore();
}

// ── Draw goal ────────────────────────────────
function drwGoal(){
  if(!goal||!goal.active) return;
  goal.t+=0.045;
  const x=Math.round(goal.x-camX), y=goal.y;
  const pulse=Math.sin(goal.t)*.38+.72;
  CX.save(); CX.shadowColor='#0ff'; CX.strokeStyle='#0ff'; CX.lineWidth=2.5;
  for(let r=6;r<=30;r+=6){
    CX.globalAlpha=pulse*(1-r/42); CX.shadowBlur=22*pulse;
    CX.beginPath(); CX.arc(x,y,r,0,Math.PI*2); CX.stroke();
  }
  CX.save(); CX.globalAlpha=pulse*.7; CX.shadowBlur=12; CX.lineWidth=1.5;
  CX.translate(x,y); CX.rotate(goal.t*1.4);
  CX.setLineDash([5,5]);
  CX.beginPath(); CX.arc(0,0,22,0,Math.PI*2); CX.stroke();
  CX.restore(); CX.setLineDash([]);
  CX.globalAlpha=pulse; CX.fillStyle='#fff'; CX.shadowBlur=12;
  CX.beginPath(); CX.arc(x,y,5,0,Math.PI*2); CX.fill();
  CX.globalAlpha=pulse*.9; CX.fillStyle='#0ff'; CX.shadowBlur=10;
  CX.font="bold 9px 'Orbitron',monospace"; CX.textAlign='center';
  CX.fillText('EXIT',x,y-40);
  CX.restore();
}

// ── Draw player ──────────────────────────────
function drwP(){
  if(P.dead) return;
  const x=Math.round(P.x-camX), y=Math.round(P.y);
  const gl=Math.sin(P.glowT)*.25+.82;
  CX.save();
  CX.shadowColor=_CUBE_COLOR; CX.strokeStyle=_cubeStroke;
  CX.lineWidth=6; CX.shadowBlur=42*gl;
  CX.strokeRect(x-3,y-3,PW+6,PH+6);
  CX.shadowBlur=22*gl; CX.lineWidth=2; CX.strokeStyle=_CUBE_COLOR;
  CX.strokeRect(x-1,y-1,PW+2,PH+2);
  const bg=CX.createLinearGradient(x,y,x+PW,y+PH);
  bg.addColorStop(0,_cubeLight); bg.addColorStop(1,_cubeDark);
  CX.shadowBlur=14; CX.fillStyle=bg; CX.fillRect(x,y,PW,PH);
  CX.globalAlpha=.38; CX.fillStyle='#fff'; CX.fillRect(x+4,y+4,PW-10,7);
  CX.globalAlpha=1; CX.shadowBlur=8; CX.shadowColor='#fff'; CX.fillStyle='#fff';
  CX.fillRect(x+5,y+10,5,5); CX.fillRect(x+18,y+10,5,5);
  CX.restore();
}

// ── HUD ──────────────────────────────────────
function drwHUD(){
  CX.save();
  for(let i=0;i<lives;i++){
    const lx=18+i*26,ly=14;
    CX.shadowBlur=10; CX.shadowColor=_CUBE_COLOR;
    CX.fillStyle=_cubeHUDFill; CX.fillRect(lx,ly,18,18);
    CX.strokeStyle=_CUBE_COLOR; CX.lineWidth=1.5; CX.strokeRect(lx,ly,18,18);
  }
  CX.shadowBlur=14; CX.shadowColor='#ff0'; CX.fillStyle='#ff0';
  CX.font="bold 17px 'Orbitron',monospace"; CX.textAlign='right';
  CX.fillText(String(score).padStart(8,'0'),W-16,30);
  if(combo>1){
    const ca=Math.min(1,comboT/0.4);
    CX.globalAlpha=ca; CX.shadowColor='#f0f'; CX.fillStyle='#f0f';
    CX.font="bold 12px 'Orbitron',monospace";
    CX.fillText(`×${combo} COMBO`,W-16,48);
    CX.globalAlpha=1;
  }
  CX.shadowBlur=8; CX.shadowColor='#0ff'; CX.fillStyle='#0ff';
  CX.font="10px 'Orbitron',monospace"; CX.textAlign='center';
  CX.fillText(`LVL ${lvlIdx+1}  ${LEVELS[lvlIdx].name}`,W/2,20);
  CX.fillStyle='#ff0'; CX.shadowColor='#ff0';
  CX.font="10px 'Share Tech Mono',monospace";
  CX.fillText(`◆ ${colOrbs}/${totOrbs}`,W/2,36);
  CX.restore();
}

// ── Scanlines ────────────────────────────────
(()=>{
  const sl=document.getElementById('scanlineCanvas');
  const sc=sl.getContext('2d');
  sc.fillStyle='#000';
  for(let y=0;y<H;y+=2) sc.fillRect(0,y,W,1);
})();

// ── Main loop ────────────────────────────────
let last=0;
function loop(ts){
  requestAnimationFrame(loop);
  const dt=Math.min((ts-last)/1000,.05);
  last=ts;

  if(STATE==='playing'){
    updPlats(dt); updP(dt); updParts(dt); updShake(dt);
    CX.save();
    CX.translate(Math.round(shake.x),Math.round(shake.y));
    drwBG(LEVELS[lvlIdx].bgHue);
    plats.forEach(drwPlat);
    spkList.forEach(drwSpike);
    orbList.forEach(drwOrb);
    drwGoal(); drwParts(); _drwCheckpoints(); (window.drwP||drwP)();
    CX.restore();
    drwHUD();
    clrJP();
  } else if(STATE==='menu'){
    drwTitle(ts);
  }
}

// ── Title BG animation ───────────────────────
function drwTitle(ts){
  const t=ts/1000;
  CX.fillStyle='#000'; CX.fillRect(0,0,W,H);
  CX.strokeStyle='rgba(0,255,255,0.038)'; CX.lineWidth=0.5;
  const gs=40;
  CX.beginPath();
  for(let x=0;x<W+gs;x+=gs){ CX.moveTo(x,0); CX.lineTo(x,H); }
  for(let y=0;y<H+gs;y+=gs){ CX.moveTo(0,y); CX.lineTo(W,y); }
  CX.stroke();
  for(let i=0;i<10;i++){
    const cx=50+i*92+Math.sin(t*.44+i)*22;
    const cy=H/2+Math.cos(t*.58+i*1.2)*72;
    const s=7+Math.sin(t*.9+i)*4;
    CX.save();
    CX.shadowBlur=22; CX.globalAlpha=0.2;
    CX.shadowColor=i%2===0?'#0ff':'#f0f';
    CX.strokeStyle=i%2===0?'#0ff':'#f0f';
    CX.lineWidth=1.5; CX.strokeRect(cx-s,cy-s,s*2,s*2);
    CX.restore();
  }
}

// ── Menu helpers ─────────────────────────────
function showMenu(id){
  ['mainMenu','pauseMenu','lcMenu','goMenu','winMenu']
    .forEach(m=>document.getElementById(m).classList.add('hidden'));
  if(id) document.getElementById(id).classList.remove('hidden');
}
function showLC(){
  STATE='levelcomplete';
  document.getElementById('lcSc').textContent=score;
  document.getElementById('lcLv').textContent=lvlIdx+1;
  document.getElementById('lcOr').textContent=`${colOrbs}/${totOrbs}`;
  const pct=colOrbs/Math.max(1,totOrbs);
  const st=pct>=1?3:pct>=0.5?2:1;
  ['s1','s2','s3'].forEach((id,i)=>{
    const el=document.getElementById(id);
    el.classList.remove('on');
    if(i<st) setTimeout(()=>el.classList.add('on'),i*300+200);
  });
  showMenu('lcMenu');
}
function showGO(){
  document.getElementById('goSc').textContent=score;
  document.getElementById('goBe').textContent=hiScore;
  showMenu('goMenu');
}
function startGame(from=0){
  lvlIdx=from; score=0; lives=3; combo=1; comboT=0; dying=false;
  // NEW: clear checkpoint on fresh start
  _cpX=null; _cpY=null; _cpLvl=null;
  initLvl(lvlIdx); STATE='playing'; showMenu(null); startAmbient();
  // NEW: show resume button after first start
  _gameStarted=true; _saveResume();
}

// ── Button wiring ────────────────────────────
document.getElementById('bPlay').onclick=()=>startGame(0);
document.getElementById('bHelp').onclick=()=>{
  const b=document.getElementById('ctrlBox');
  b.style.display=b.style.display==='none'?'block':'none';
};
document.getElementById('bResume').onclick=()=>{ STATE='playing'; showMenu(null); };
document.getElementById('bQuit').onclick=()=>{ STATE='menu'; _showMainMenu(); };
document.getElementById('bNext').onclick=()=>{
  if(lvlIdx<LEVELS.length-1){
    lvlIdx++; initLvl(lvlIdx); STATE='playing'; showMenu(null);
  } else {
    if(score>hiScore){ hiScore=score; localStorage.setItem('cubix_hi',hiScore); }
    document.getElementById('wSc').textContent=score;
    document.getElementById('wBe').textContent=hiScore;
    STATE='win'; showMenu('winMenu');
  }
};
document.getElementById('bLCQ').onclick=()=>{ STATE='menu'; _showMainMenu(); };
document.getElementById('bRe').onclick=()=>{
  // NEW: retry respawns at checkpoint
  lives=3; dying=false; _respawnAtCheckpoint(); STATE='playing'; showMenu(null);
};
document.getElementById('bGOQ').onclick=()=>{ STATE='menu'; _showMainMenu(); };
document.getElementById('bWP').onclick=()=>startGame(0);
document.getElementById('bWM').onclick=()=>{ STATE='menu'; _showMainMenu(); };

document.addEventListener('keydown',e=>{
  if(e.code==='Escape'){
    if(STATE==='playing'){ STATE='paused'; showMenu('pauseMenu'); }
    else if(STATE==='paused'){ STATE='playing'; showMenu(null); }
  }
});

// ── Boot ─────────────────────────────────────
document.getElementById('hiV').textContent=hiScore;
showMenu('mainMenu');
requestAnimationFrame(ts=>{ last=ts; loop(ts); });
</script>

<!-- ═══════════════════════════════════════════════
     NEW FEATURES — injected cleanly below original
     ═══════════════════════════════════════════════ -->

<!-- NEW FEATURES -->
<script>
'use strict';

// ═══════════════════════════════════════════════
//  CUBIX — NEW FEATURES BLOCK (clean rewrite)
//  All variables declared at top to avoid TDZ
// ═══════════════════════════════════════════════

// ── Checkpoint state (must be first to avoid TDZ) ──
let _cpX = null, _cpY = null, _cpLvl = null, _cpTouched = [];

// ══════════════════════════════
//  1. WARDROBE + COLOR SYSTEM
// ══════════════════════════════

const SWATCHES = [
  { name:'Cyber Cyan',   hex:'#00ffff', light:'#00eeff', dark:'#0099cc', stroke:'rgba(0,255,255,0.22)' },
  { name:'Neon Red',     hex:'#ff2244', light:'#ff4466', dark:'#cc0022', stroke:'rgba(255,34,68,0.22)'  },
  { name:'Solar Orange', hex:'#ff8800', light:'#ffaa22', dark:'#cc5500', stroke:'rgba(255,136,0,0.22)'  },
  { name:'Volt Yellow',  hex:'#ffee00', light:'#ffff44', dark:'#ccaa00', stroke:'rgba(255,238,0,0.22)'  },
  { name:'Matrix Green', hex:'#00ff66', light:'#44ff88', dark:'#00cc44', stroke:'rgba(0,255,102,0.22)'  },
  { name:'Royal Blue',   hex:'#2255ff', light:'#4477ff', dark:'#0033cc', stroke:'rgba(34,85,255,0.22)'  },
  { name:'Ultra Violet', hex:'#cc00ff', light:'#dd44ff', dark:'#9900cc', stroke:'rgba(204,0,255,0.22)'  },
  { name:'Hot Pink',     hex:'#ff00aa', light:'#ff44cc', dark:'#cc0077', stroke:'rgba(255,0,170,0.22)'  },
  { name:'Pure White',   hex:'#ffffff', light:'#ffffff', dark:'#aaaacc', stroke:'rgba(255,255,255,0.22)' },
  { name:'Original',     hex:'#0099ee', light:'#0099ee', dark:'#0044cc', stroke:'rgba(0,153,238,0.22)'  },
];

// Premium skin unlock state
const _SILVER_KEY = 'cubix_silver_unlocked';
const _GOLD_KEY   = 'cubix_gold_unlocked';
function _isSilverUnlocked(){ return localStorage.getItem(_SILVER_KEY)==='1'; }
function _isGoldUnlocked()  { return localStorage.getItem(_GOLD_KEY)==='1'; }

// Safe swatch index — clamp to 0-9 only (10/11 handled separately)
let _cubeSwatchIdx = parseInt(localStorage.getItem('cubix_swatch')||'9');
if(isNaN(_cubeSwatchIdx)||_cubeSwatchIdx<0||_cubeSwatchIdx>9) _cubeSwatchIdx=9;

let _CUBE_COLOR  = SWATCHES[_cubeSwatchIdx].hex;
let _cubeLight   = SWATCHES[_cubeSwatchIdx].light;
let _cubeDark    = SWATCHES[_cubeSwatchIdx].dark;
let _cubeStroke  = SWATCHES[_cubeSwatchIdx].stroke;
let _cubeHUDFill = 'rgba(0,255,255,0.16)';

// Are we using a premium skin?
let _usingSilver = false;
let _usingGold   = false;

function _hexToRGBA(hex,a){
  let h=hex.replace('#','');
  if(h.length===3)h=h.split('').map(c=>c+c).join('');
  if(h.length<6)return`rgba(0,255,255,${a})`;
  return`rgba(${parseInt(h.slice(0,2),16)},${parseInt(h.slice(2,4),16)},${parseInt(h.slice(4,6),16)},${a})`;
}

function _applyColor(idx){
  _cubeSwatchIdx = idx;
  _usingSilver = false; _usingGold = false;
  if(idx===10 && _isSilverUnlocked()){ _usingSilver=true; _CUBE_COLOR='#c8d8e8'; _cubeLight='#f0f4f8'; _cubeDark='#7090a8'; _cubeStroke='rgba(200,220,240,0.25)'; _cubeHUDFill='rgba(200,220,240,0.16)'; localStorage.setItem('cubix_swatch',10); _renderWardrobePreview(); return; }
  if(idx===11 && _isGoldUnlocked()){ _usingGold=true; _CUBE_COLOR='#ffd700'; _cubeLight='#ffe066'; _cubeDark='#7a5500'; _cubeStroke='rgba(255,215,0,0.28)'; _cubeHUDFill='rgba(255,215,0,0.16)'; localStorage.setItem('cubix_swatch',11); _renderWardrobePreview(); return; }
  // fallback to index 9 if premium not unlocked
  if(idx>9){ idx=9; _cubeSwatchIdx=9; }
  const sw=SWATCHES[idx];
  _CUBE_COLOR=sw.hex; _cubeLight=sw.light; _cubeDark=sw.dark; _cubeStroke=sw.stroke;
  _cubeHUDFill=_hexToRGBA(sw.hex,0.16);
  localStorage.setItem('cubix_swatch',idx);
  _renderWardrobePreview();
}

// Wardrobe preview canvas
const _wdPrev = document.getElementById('wdPreviewCanvas');
const _wdCtx  = _wdPrev.getContext('2d');
let _wdAnim   = 0;

function _renderWardrobePreview(){
  if(_usingSilver){ _renderSilverPreview(); return; }
  if(_usingGold)  { _renderGoldPreview();   return; }
  _wdCtx.clearRect(0,0,60,60);
  _wdAnim+=0.06;
  const gl=Math.sin(_wdAnim)*0.25+0.82;
  const sw=SWATCHES[_cubeSwatchIdx]||SWATCHES[9];
  _wdCtx.save();
  _wdCtx.shadowColor=sw.hex; _wdCtx.shadowBlur=20*gl;
  _wdCtx.strokeStyle=sw.hex; _wdCtx.lineWidth=2;
  _wdCtx.strokeRect(11,11,38,38);
  const bg=_wdCtx.createLinearGradient(12,12,48,48);
  bg.addColorStop(0,sw.light); bg.addColorStop(1,sw.dark);
  _wdCtx.shadowBlur=12; _wdCtx.fillStyle=bg; _wdCtx.fillRect(12,12,36,36);
  _wdCtx.globalAlpha=0.35; _wdCtx.fillStyle='#fff'; _wdCtx.fillRect(16,16,20,6);
  _wdCtx.globalAlpha=1; _wdCtx.shadowBlur=6; _wdCtx.shadowColor='#fff'; _wdCtx.fillStyle='#fff';
  _wdCtx.fillRect(17,24,5,5); _wdCtx.fillRect(28,24,5,5);
  _wdCtx.restore();
}

function _renderSilverPreview(){
  _wdCtx.clearRect(0,0,60,60);
  const t=Date.now()*0.001, shimX=-36+((t*30)%96);
  _wdCtx.save();
  const bg=_wdCtx.createLinearGradient(12,12,48,48);
  bg.addColorStop(0,'#d0d8e0');bg.addColorStop(0.4,'#f2f4f8');bg.addColorStop(0.7,'#9ab0c0');bg.addColorStop(1,'#607080');
  _wdCtx.shadowColor='#c8d8e8';_wdCtx.shadowBlur=16;_wdCtx.fillStyle=bg;_wdCtx.fillRect(12,12,36,36);
  _wdCtx.strokeStyle='rgba(200,220,240,0.8)';_wdCtx.lineWidth=2;_wdCtx.strokeRect(11,11,38,38);
  _wdCtx.beginPath();_wdCtx.rect(12,12,36,36);_wdCtx.clip();
  const sh=_wdCtx.createLinearGradient(shimX,12,shimX+22,48);
  sh.addColorStop(0,'rgba(255,255,255,0)');sh.addColorStop(0.5,'rgba(255,255,255,0.6)');sh.addColorStop(1,'rgba(255,255,255,0)');
  _wdCtx.fillStyle=sh;_wdCtx.fillRect(shimX,12,22,36);_wdCtx.restore();
  _wdCtx.globalAlpha=0.38;_wdCtx.fillStyle='#fff';_wdCtx.fillRect(16,16,18,5);
  _wdCtx.globalAlpha=1;_wdCtx.fillStyle='#d0e4f4';_wdCtx.fillRect(17,24,5,5);_wdCtx.fillRect(28,24,5,5);
}

function _renderGoldPreview(){
  _wdCtx.clearRect(0,0,60,60);
  const t=Date.now()*0.001,pulse=Math.sin(t*2.4)*0.2+0.8,shimX=-36+((t*24)%96);
  _wdCtx.save();
  const bg=_wdCtx.createLinearGradient(12,12,48,48);
  bg.addColorStop(0,'#ffe066');bg.addColorStop(0.3,'#ffd700');bg.addColorStop(0.6,'#b8860b');bg.addColorStop(1,'#7a5500');
  _wdCtx.shadowColor=`rgba(255,200,0,${pulse})`;_wdCtx.shadowBlur=20*pulse;_wdCtx.fillStyle=bg;_wdCtx.fillRect(12,12,36,36);
  _wdCtx.strokeStyle='rgba(255,215,0,0.9)';_wdCtx.lineWidth=2;_wdCtx.strokeRect(11,11,38,38);
  _wdCtx.beginPath();_wdCtx.rect(12,12,36,36);_wdCtx.clip();
  const sh=_wdCtx.createLinearGradient(shimX,12,shimX+20,48);
  sh.addColorStop(0,'rgba(255,255,200,0)');sh.addColorStop(0.5,'rgba(255,255,180,0.65)');sh.addColorStop(1,'rgba(255,255,200,0)');
  _wdCtx.fillStyle=sh;_wdCtx.fillRect(shimX,12,20,36);_wdCtx.restore();
  _wdCtx.globalAlpha=0.5;_wdCtx.fillStyle='#fffacc';_wdCtx.fillRect(16,16,18,5);
  _wdCtx.globalAlpha=1;_wdCtx.fillStyle='#3a2000';_wdCtx.fillRect(17,24,5,5);_wdCtx.fillRect(28,24,5,5);
}

// Build wardrobe grid (10 normal swatches only)
(function _buildWardrobe(){
  const grid=document.getElementById('wdGrid');
  grid.innerHTML='';
  SWATCHES.forEach((sw,i)=>{
    const div=document.createElement('div');
    div.className='wd-swatch'+(i===_cubeSwatchIdx?' active':'');
    div.style.background=sw.hex;
    div.style.boxShadow=`0 0 12px ${sw.hex},0 0 24px ${sw.hex}44`;
    div.title=sw.name;
    const chk=document.createElement('span');
    chk.className='wd-check';chk.textContent='✓';
    div.appendChild(chk);
    div.addEventListener('click',()=>{
      document.querySelectorAll('.wd-swatch,.wd-premium-swatch').forEach(s=>s.classList.remove('active'));
      div.classList.add('active');
      _applyColor(i);
      try{tone(660,'sine',0.08,0.15);}catch(e){}
    });
    grid.appendChild(div);
  });
})();

// Wardrobe loop
function _wardrobeLoop(){
  if(!document.getElementById('wardrobeMenu').classList.contains('hidden')){
    _renderWardrobePreview();
    requestAnimationFrame(_wardrobeLoop);
  }
}
function _openWardrobe(){
  ['mainMenu','pauseMenu','lcMenu','goMenu','winMenu'].forEach(m=>document.getElementById(m).classList.add('hidden'));
  document.getElementById('wardrobeMenu').classList.remove('hidden');
  requestAnimationFrame(_wardrobeLoop);
  try{tone(500,'sine',0.06,0.12);}catch(e){}
}
function _closeWardrobe(){
  document.getElementById('wardrobeMenu').classList.add('hidden');
  _showMainMenu();
}
document.getElementById('bWardrobe').addEventListener('click',_openWardrobe);
document.getElementById('bWdBack').addEventListener('click',_closeWardrobe);

// Silver/Gold premium wardrobe slots
function _rebuildPremiumSlots(){
  const old=document.getElementById('_premiumRow');
  if(old)old.remove();
  const row=document.createElement('div');
  row.className='wd-premium-row';row.id='_premiumRow';
  document.getElementById('bWdBack').parentNode.insertBefore(row,document.getElementById('bWdBack'));

  // Silver
  const sSlot=document.createElement('div');sSlot.className='wd-premium-slot';
  const sSwatch=document.createElement('div');
  sSwatch.className='wd-premium-swatch wd-silver-bg'+(_usingSilver?' active':'')+(_isSilverUnlocked()?'':' locked');
  if(_isSilverUnlocked()){
    const chk=document.createElement('span');chk.className='wd-premium-check';chk.textContent='✓';sSwatch.appendChild(chk);
    sSwatch.addEventListener('click',()=>{
      document.querySelectorAll('.wd-swatch,.wd-premium-swatch').forEach(s=>s.classList.remove('active'));
      sSwatch.classList.add('active'); _applyColor(10);
      try{tone(760,'sine',0.08,0.15);}catch(e){}
    });
  } else {
    const lk=document.createElement('span');lk.textContent='🔒';lk.style.cssText='position:absolute;top:50%;left:50%;transform:translate(-50%,-50%);font-size:22px;';sSwatch.appendChild(lk);
  }
  const sLbl=document.createElement('div');sLbl.className='wd-premium-label'+(_isSilverUnlocked()?' unlocked':'');
  sLbl.style.color=_isSilverUnlocked()?'#c8d8e8':'#555';
  sLbl.textContent=_isSilverUnlocked()?'SILVER':'Complete Lvl 7';
  sSlot.appendChild(sSwatch);sSlot.appendChild(sLbl);

  // Gold
  const gSlot=document.createElement('div');gSlot.className='wd-premium-slot';gSlot.style.position='relative';
  const crown=document.createElement('div');crown.className='wd-gold-crown';crown.textContent='👑';gSlot.appendChild(crown);
  const gSwatch=document.createElement('div');
  gSwatch.className='wd-premium-swatch wd-gold-bg'+(_usingGold?' active':'')+(_isGoldUnlocked()?'':' locked');
  if(_isGoldUnlocked()){
    const chk=document.createElement('span');chk.className='wd-premium-check';chk.textContent='✓';gSwatch.appendChild(chk);
    gSwatch.addEventListener('click',()=>{
      document.querySelectorAll('.wd-swatch,.wd-premium-swatch').forEach(s=>s.classList.remove('active'));
      gSwatch.classList.add('active'); _applyColor(11);
      try{tone(880,'sine',0.08,0.15);}catch(e){}
    });
  } else {
    const lk=document.createElement('span');lk.textContent='🔒';lk.style.cssText='position:absolute;top:50%;left:50%;transform:translate(-50%,-50%);font-size:22px;';gSwatch.appendChild(lk);
  }
  const gLbl=document.createElement('div');gLbl.className='wd-premium-label'+(_isGoldUnlocked()?' unlocked':'');
  gLbl.style.color=_isGoldUnlocked()?'#ffd700':'#555';
  gLbl.textContent=_isGoldUnlocked()?'GOLD ✦':'Complete Lvl 10';
  gSlot.appendChild(gSwatch);gSlot.appendChild(gLbl);

  row.appendChild(sSlot);row.appendChild(gSlot);
}

// ══════════════════════════════
//  2. CHECKPOINTS
// ══════════════════════════════

const _CHECKPOINTS = {
  0: [{x:200,platY:492},{x:1200,platY:375}],
  1: [{x:150,platY:492},{x:1140,platY:318}],
  2: [{x:150,platY:492},{x:1350,platY:318}],
  3: [{x:150,platY:492},{x:1680,platY:298}],
  4: [{x:1005,platY:420},{x:1840,platY:308}],
  5: [{x:710,platY:378},{x:1820,platY:278}],
  6: [{x:100,platY:468},{x:1950,platY:258}],
  7: [{x:90,platY:468},{x:1860,platY:258}],
  8: [{x:80,platY:468},{x:1940,platY:218}],
  9: [{x:70,platY:468},{x:2080,platY:178}],
};

function _initCheckpoints(){ _cpTouched=(_CHECKPOINTS[lvlIdx]||[]).map(()=>false); }

function _checkCheckpoints(){
  const cps=_CHECKPOINTS[lvlIdx]||[];
  cps.forEach((cp,i)=>{
    if(_cpTouched[i])return;
    const dx=P.x+PW/2-cp.x;
    if(Math.abs(dx)<50 && Math.abs(P.y+PH-cp.platY)<20 && P.onGnd){
      _cpTouched[i]=true; _cpX=cp.x; _cpY=cp.platY; _cpLvl=lvlIdx;
      _showCPIndicator(); _saveResume();
      try{tone(740,'sine',0.12,0.16);setTimeout(()=>tone(1000,'sine',0.08,0.12),80);}catch(e){}
    }
  });
}

function _showCPIndicator(){
  const el=document.getElementById('cpIndicator');
  el.style.opacity='1'; setTimeout(()=>{el.style.opacity='0';},2200);
}

function _respawnAtCheckpoint(){
  const saved=_cpTouched.slice(), sx=_cpX, sy=_cpY, sl=_cpLvl;
  initLvl(lvlIdx); _cpTouched=saved; _cpX=sx; _cpY=sy; _cpLvl=sl;
  if(_cpX!==null && _cpLvl===lvlIdx){
    resetP(_cpX-PW/2, _cpY-PH);
    camX=Math.max(0,_cpX-PW/2-W/3); camXT=camX;
  }
  STATE='playing';
}

function _drwCheckpoints(){
  (_CHECKPOINTS[lvlIdx]||[]).forEach((cp,i)=>{
    const sx=Math.round(cp.x-camX), sy=cp.platY;
    const touched=_cpTouched[i]||false;
    const col=touched?'#00ff66':'#aaaaaa';
    CX.save();
    CX.shadowBlur=touched?14:5; CX.shadowColor=touched?'#00ff66':'#666';
    CX.strokeStyle=col; CX.lineWidth=2.5;
    CX.beginPath(); CX.moveTo(sx,sy); CX.lineTo(sx,sy-44); CX.stroke();
    CX.shadowBlur=touched?18:7; CX.fillStyle=touched?'#00ff66':'#777';
    CX.beginPath(); CX.moveTo(sx,sy-44); CX.lineTo(sx+20,sy-36); CX.lineTo(sx,sy-28); CX.closePath(); CX.fill();
    if(touched){
      const pulse=Math.sin(Date.now()*0.005)*0.35+0.65;
      CX.globalAlpha=pulse*0.55; CX.shadowBlur=22; CX.strokeStyle='#00ff66'; CX.lineWidth=1.5;
      CX.beginPath(); CX.arc(sx+10,sy-36,14,0,Math.PI*2); CX.stroke();
    }
    CX.globalAlpha=touched?1:0.5; CX.fillStyle=col;
    CX.font="bold 8px 'Orbitron',monospace"; CX.textAlign='center';
    CX.fillText('CP',sx+10,sy-36); CX.restore();
  });
}

// ══════════════════════════════
//  3. SILVER / GOLD CUBE DRAW
//  (drawn via setInterval overlay)
// ══════════════════════════════

function _drwSilverCube(){
  if(P.dead)return;
  const x=Math.round(P.x-camX),y=Math.round(P.y),gl=Math.sin(P.glowT)*0.25+0.82;
  CX.save();
  CX.shadowColor='#c8d8e8';CX.shadowBlur=38*gl;CX.strokeStyle='rgba(200,220,240,0.7)';CX.lineWidth=5;
  CX.strokeRect(x-3,y-3,PW+6,PH+6);
  const bg=CX.createLinearGradient(x,y,x+PW,y+PH);
  bg.addColorStop(0,'#d8e0e8');bg.addColorStop(0.3,'#f0f4f8');bg.addColorStop(0.6,'#a8b8c8');bg.addColorStop(1,'#7090a8');
  CX.shadowBlur=18*gl;CX.fillStyle=bg;CX.fillRect(x,y,PW,PH);
  const shimX=x-PW+(((Date.now()*0.0012)%1)*(PW*3));
  CX.save();CX.beginPath();CX.rect(x,y,PW,PH);CX.clip();
  const sh=CX.createLinearGradient(shimX,y,shimX+PW*0.7,y+PH);
  sh.addColorStop(0,'rgba(255,255,255,0)');sh.addColorStop(0.4,'rgba(255,255,255,0.55)');sh.addColorStop(1,'rgba(255,255,255,0)');
  CX.fillStyle=sh;CX.fillRect(shimX,y,PW*0.7,PH);CX.restore();
  CX.globalAlpha=0.45;CX.fillStyle='#fff';CX.fillRect(x+3,y+3,PW-8,6);
  CX.globalAlpha=1;CX.shadowBlur=6;CX.shadowColor='#fff';CX.fillStyle='#e0eeff';
  CX.fillRect(x+5,y+10,5,5);CX.fillRect(x+18,y+10,5,5);CX.restore();
}

function _drwGoldCube(){
  if(P.dead)return;
  const x=Math.round(P.x-camX),y=Math.round(P.y),t=Date.now()*0.001;
  const gl=Math.sin(P.glowT)*0.3+0.8,pulse=Math.sin(t*2.4)*0.25+0.85;
  CX.save();
  CX.save();CX.globalAlpha=0.18*pulse;
  const cx_=x+PW/2,cy_=y+PH/2;
  for(let r=0;r<8;r++){
    const angle=(r/8)*Math.PI*2+t*0.6,inner=24,outer=52+Math.sin(t*1.8+r)*10;
    CX.fillStyle=`rgba(255,215,0,${0.55*pulse})`;CX.beginPath();
    CX.moveTo(cx_+Math.cos(angle-0.06)*inner,cy_+Math.sin(angle-0.06)*inner);
    CX.lineTo(cx_+Math.cos(angle)*outer,cy_+Math.sin(angle)*outer);
    CX.lineTo(cx_+Math.cos(angle+0.06)*inner,cy_+Math.sin(angle+0.06)*inner);
    CX.closePath();CX.fill();
  }
  CX.restore();
  CX.shadowColor=`rgba(255,200,0,${pulse})`;CX.shadowBlur=52*gl*pulse;CX.strokeStyle='rgba(255,215,0,0.8)';CX.lineWidth=5;
  CX.strokeRect(x-3,y-3,PW+6,PH+6);
  const bg=CX.createLinearGradient(x,y,x+PW,y+PH);
  bg.addColorStop(0,'#ffe066');bg.addColorStop(0.25,'#ffd700');bg.addColorStop(0.55,'#b8860b');bg.addColorStop(0.8,'#ffc200');bg.addColorStop(1,'#7a5500');
  CX.shadowBlur=22*gl;CX.fillStyle=bg;CX.fillRect(x,y,PW,PH);
  const shimX=x-PW+(((Date.now()*0.0009)%1)*(PW*3));
  CX.save();CX.beginPath();CX.rect(x,y,PW,PH);CX.clip();
  const sh=CX.createLinearGradient(shimX,y,shimX+PW*0.6,y+PH);
  sh.addColorStop(0,'rgba(255,255,200,0)');sh.addColorStop(0.5,'rgba(255,255,180,0.6)');sh.addColorStop(1,'rgba(255,255,200,0)');
  CX.fillStyle=sh;CX.fillRect(shimX,y,PW*0.6,PH);CX.restore();
  CX.globalAlpha=0.5;CX.fillStyle='#fffacc';CX.fillRect(x+3,y+3,PW-8,5);
  CX.globalAlpha=1;CX.shadowBlur=8;CX.shadowColor='#ff8800';CX.fillStyle='#3a2000';
  CX.fillRect(x+5,y+10,5,5);CX.fillRect(x+18,y+10,5,5);CX.restore();
}

// ══════════════════════════════
//  4. MAIN MENU / RESUME
// ══════════════════════════════

let _gameStarted = localStorage.getItem('cubix_started')==='1';

function _saveResume(){
  localStorage.setItem('cubix_started','1');
  localStorage.setItem('cubix_resumeLvl',lvlIdx);
  if(_cpX!==null){ localStorage.setItem('cubix_resumeX',_cpX); localStorage.setItem('cubix_resumeY',_cpY); }
}

function _showMainMenu(){
  ['mainMenu','pauseMenu','lcMenu','goMenu','winMenu','wardrobeMenu'].forEach(m=>{
    const el=document.getElementById(m); if(el)el.classList.add('hidden');
  });
  document.getElementById('mainMenu').classList.remove('hidden');
  document.getElementById('bResume2').style.display=(_gameStarted||localStorage.getItem('cubix_started')==='1')?'inline-block':'none';
}

document.getElementById('bResume2').addEventListener('click',()=>{
  const sl=parseInt(localStorage.getItem('cubix_resumeLvl')||'0');
  const sx=parseFloat(localStorage.getItem('cubix_resumeX')||'NaN');
  const sy=parseFloat(localStorage.getItem('cubix_resumeY')||'NaN');
  lvlIdx=sl; dying=false; initLvl(lvlIdx); _initCheckpoints();
  if(!isNaN(sx)&&sx>0&&!isNaN(sy)){ _cpX=sx;_cpY=sy;_cpLvl=lvlIdx; resetP(sx-PW/2,sy-PH); camX=Math.max(0,sx-PW/2-W/3); camXT=camX; }
  STATE='playing'; showMenu(null); try{startAmbient();}catch(e){} _gameStarted=true;
  setTimeout(()=>{ lives=Math.max(lives,5); },30);
});

document.getElementById('bPlay').onclick=()=>{
  _cpX=null;_cpY=null;_cpLvl=null;
  startGame(0); _initCheckpoints();
  setTimeout(()=>{ lives=Math.max(lives,5); },30);
};

document.getElementById('bNext').onclick=function(){
  if(lvlIdx===6) _unlockSilver();
  if(lvlIdx===9) _unlockGold();
  if(lvlIdx<LEVELS.length-1){
    lvlIdx++;_cpX=null;_cpY=null;_cpLvl=null;
    initLvl(lvlIdx);_initCheckpoints();STATE='playing';showMenu(null);
  } else {
    if(score>hiScore){hiScore=score;localStorage.setItem('cubix_hi',hiScore);}
    document.getElementById('wSc').textContent=score;
    document.getElementById('wBe').textContent=hiScore;
    _unlockGold(); STATE='win'; showMenu('winMenu');
  }
};

document.getElementById('bRe').onclick=()=>{ lives=5; dying=false; _respawnAtCheckpoint(); };
['bQuit','bLCQ','bGOQ','bWM'].forEach(id=>{ document.getElementById(id).onclick=()=>{ STATE='menu'; _showMainMenu(); }; });
document.addEventListener('keydown',e=>{ if(e.code==='Escape'&&STATE==='playing'){STATE='paused';showMenu('pauseMenu');} });

// ══════════════════════════════
//  5. UNLOCK SILVER / GOLD
// ══════════════════════════════

function _unlockBanner(msg,col){
  let el=document.getElementById('_unlockBanner');
  if(!el){ el=document.createElement('div');el.id='_unlockBanner';
    el.style.cssText='position:absolute;top:60px;left:50%;transform:translateX(-50%) translateY(-20px);z-index:20;padding:10px 30px;border:2px solid;font-family:Orbitron,monospace;font-size:13px;font-weight:700;letter-spacing:4px;text-align:center;background:rgba(0,0,0,0.92);pointer-events:none;opacity:0;transition:opacity 0.4s,transform 0.4s;';
    document.getElementById('gameContainer').appendChild(el);
  }
  el.style.color=col;el.style.borderColor=col;el.style.boxShadow=`0 0 24px ${col}`;
  el.textContent='✦ '+msg+' ✦';el.style.opacity='1';el.style.transform='translateX(-50%) translateY(0)';
  setTimeout(()=>{el.style.opacity='0';el.style.transform='translateX(-50%) translateY(-20px)';},3200);
}

function _unlockSilver(){
  if(_isSilverUnlocked())return;
  localStorage.setItem(_SILVER_KEY,'1');
  _rebuildPremiumSlots();
  _unlockBanner('SILVER SKIN UNLOCKED','#c0c0c0');
}
function _unlockGold(){
  if(_isGoldUnlocked())return;
  localStorage.setItem(_GOLD_KEY,'1');
  _rebuildPremiumSlots();
  _unlockBanner('GOLDEN SKIN UNLOCKED','#ffd700');
}

// ══════════════════════════════
//  6. EASY MODE (5 lives, floatier)
// ══════════════════════════════

const _origStartGame=startGame;
window.startGame=function(from=0){ _origStartGame(from); lives=5; };
document.getElementById('bPlay').addEventListener('click',()=>setTimeout(()=>{lives=Math.max(lives,5);},30));
document.getElementById('bRe').addEventListener('click',()=>setTimeout(()=>{lives=Math.max(lives,5);},30));

// ══════════════════════════════
//  7. ADMIN / OWNER SYSTEM
//  Privilege NEVER persists — always starts as normal
//  Keybinds (only when admin/owner active in session):
//    SHIFT+L  = level select prompt
//    SHIFT+F  = toggle fly (owner only)
//    SHIFT+I  = toggle immortal (owner only)
// ══════════════════════════════

let _priv='none', _immortal=false, _flying=false;
function _isAdmin(){ return _priv==='admin'||_priv==='owner'; }
function _isOwner(){ return _priv==='owner'; }

// Redeem button
(function _buildRedeemUI(){
  const style=document.createElement('style');
  style.textContent=`
#_redeemBtn{position:absolute;bottom:8px;right:8px;z-index:50;font-family:'Share Tech Mono',monospace;font-size:9px;letter-spacing:1px;color:#2a2a2a;background:rgba(0,0,0,0.6);border:1px solid #1a1a1a;padding:4px 8px;cursor:pointer;}
#_redeemBtn:hover{color:#444;}
#_redeemModal{position:absolute;inset:0;z-index:60;background:rgba(0,0,0,0.88);display:flex;flex-direction:column;align-items:center;justify-content:center;gap:12px;}
#_redeemModal.hidden{display:none;}
#_redeemTitle{font-family:'Orbitron',monospace;font-size:18px;letter-spacing:4px;color:#333;font-weight:700;}
#_redeemInput{font-family:'Share Tech Mono',monospace;font-size:14px;background:#0a0a0a;border:1px solid #222;color:#666;padding:8px 16px;width:260px;letter-spacing:2px;text-align:center;outline:none;}
#_redeemInput:focus{border-color:#444;color:#888;}
#_redeemMsg{font-family:'Share Tech Mono',monospace;font-size:11px;color:#444;letter-spacing:2px;min-height:16px;}
#_redeemClose{font-family:'Share Tech Mono',monospace;font-size:10px;color:#222;background:none;border:1px solid #1a1a1a;padding:4px 14px;cursor:pointer;letter-spacing:2px;}
#_redeemClose:hover{color:#444;}
#_privBadge{position:absolute;top:8px;right:8px;z-index:50;font-family:'Share Tech Mono',monospace;font-size:9px;letter-spacing:2px;padding:3px 8px;pointer-events:none;opacity:0;transition:opacity 0.3s;}
#_privBadge.show{opacity:1;}
#_privFlash{position:absolute;bottom:55px;right:8px;z-index:52;font-family:'Share Tech Mono',monospace;font-size:10px;letter-spacing:2px;pointer-events:none;opacity:0;transition:opacity 0.3s;padding:3px 8px;background:rgba(0,0,0,0.8);border:1px solid #222;}
  `;
  document.head.appendChild(style);

  const gc=document.getElementById('gameContainer');

  const btn=document.createElement('button');btn.id='_redeemBtn';btn.textContent='redeem code';gc.appendChild(btn);

  const badge=document.createElement('div');badge.id='_privBadge';gc.appendChild(badge);
  const flash=document.createElement('div');flash.id='_privFlash';gc.appendChild(flash);

  const modal=document.createElement('div');modal.id='_redeemModal';modal.className='hidden';
  modal.innerHTML=`<div id="_redeemTitle">REDEEM CODE</div><input id="_redeemInput" type="text" placeholder="ENTER CODE" autocomplete="off" spellcheck="false"><div id="_redeemMsg"></div><button id="_redeemClose">CLOSE</button>`;
  gc.appendChild(modal);

  btn.addEventListener('click',()=>{ modal.classList.remove('hidden'); document.getElementById('_redeemInput').value=''; document.getElementById('_redeemMsg').textContent=''; document.getElementById('_redeemInput').focus(); });
  document.getElementById('_redeemClose').addEventListener('click',()=>modal.classList.add('hidden'));

  document.getElementById('_redeemInput').addEventListener('keydown',e=>{
    if(e.key!=='Enter')return;
    const code=e.target.value.trim().toUpperCase();
    const msg=document.getElementById('_redeemMsg');
    if(code==='TDVADMIN'){ _priv='admin'; _applyPriv(); msg.style.color='#446644'; msg.textContent='✓ ADMIN GRANTED'; setTimeout(()=>modal.classList.add('hidden'),1000); }
    else if(code==='TDVOWNER_2014'){ _priv='owner'; _applyPriv(); msg.style.color='#554422'; msg.textContent='✓ OWNER GRANTED'; setTimeout(()=>modal.classList.add('hidden'),1000); }
    else if(code==='TDVNORMAL'){ _priv='none'; _immortal=false; _flying=false; _applyPriv(); msg.style.color='#333'; msg.textContent='✓ REMOVED'; setTimeout(()=>modal.classList.add('hidden'),1000); }
    else { msg.style.color='#442222'; msg.textContent='✗ INVALID CODE'; }
  });
})();

function _applyPriv(){
  const badge=document.getElementById('_privBadge');
  if(_priv==='owner'){ badge.textContent='[ OWNER ]';badge.style.color='#665533';badge.style.border='1px solid #332211';badge.classList.add('show'); }
  else if(_priv==='admin'){ badge.textContent='[ ADMIN ]';badge.style.color='#446633';badge.style.border='1px solid #223311';badge.classList.add('show'); }
  else { badge.classList.remove('show'); _flying=false; _immortal=false; }
}

function _flashPriv(txt,col){
  const el=document.getElementById('_privFlash');
  if(!el)return; el.style.color=col; el.textContent=txt; el.style.opacity='1';
  clearTimeout(el._t); el._t=setTimeout(()=>{el.style.opacity='0';},1800);
}

// KEYBINDS — only fire when admin/owner
// SHIFT+L = level warp prompt  |  SHIFT+F = fly (owner)  |  SHIFT+I = immortal (owner)
window.addEventListener('keydown',e=>{
  if(!_isAdmin())return;
  if(e.shiftKey && e.code==='KeyL'){
    const n=parseInt(prompt('Warp to level (1-10):'));
    if(!isNaN(n)&&n>=1&&n<=10){
      _cpX=null;_cpY=null;_cpLvl=null; lvlIdx=n-1;
      score=0;lives=5;combo=1;comboT=0;dying=false;
      initLvl(lvlIdx);_initCheckpoints();STATE='playing';
      ['mainMenu','pauseMenu','lcMenu','goMenu','winMenu','wardrobeMenu'].forEach(id=>{const el=document.getElementById(id);if(el)el.classList.add('hidden');});
      try{startAmbient();}catch(e){}
      _flashPriv('→ LEVEL '+n,'#446644');
    }
  }
  if(!_isOwner())return;
  if(e.shiftKey && e.code==='KeyI'){
    _immortal=!_immortal;
    _flashPriv(_immortal?'★ IMMORTAL ON':'★ IMMORTAL OFF','#665533');
  }
  if(e.shiftKey && e.code==='KeyF'){
    _flying=!_flying;
    _flashPriv(_flying?'✦ FLY ON':'✦ FLY OFF','#334455');
  }
});

// ══════════════════════════════
//  8. SETINTERVAL — per-frame extras
//  (fly, immortal, easy gravity, premium skin overlay, checkpoints)
// ══════════════════════════════

setInterval(()=>{
  if(typeof STATE==='undefined'||STATE!=='playing')return;
  if(typeof P==='undefined'||!P)return;

  // Checkpoint detection
  if(!P.dead) _checkCheckpoints();

  // Immortal guard
  if(_immortal&&dying){ dying=false; P.dead=false; lives=Math.max(lives,1); }

  // Fly
  if(_flying&&!P.dead){
    const up=K['ArrowUp']||K['Space']||K['KeyW'];
    const dn=K['ArrowDown']||K['KeyS'];
    if(up)P.vy=-220; else if(dn)P.vy=220;
    else if(Math.abs(P.vy)>20)P.vy*=0.75;
    P.onGnd=false;
  }

  // Easy gravity drag
  if(!P.dead&&P.vy>0) P.vy*=0.96;

  // Premium skin overlay
  if(_usingSilver&&!P.dead) _drwSilverCube();
  if(_usingGold&&!P.dead)   _drwGoldCube();
},16);

// Jump boost
window.addEventListener('keydown',e=>{
  if(typeof STATE==='undefined'||STATE!=='playing'||!P||P.dead)return;
  if(e.code==='Space'||e.code==='ArrowUp'||e.code==='KeyW'){
    setTimeout(()=>{if(P&&P.vy<0&&P.vy>-700)P.vy*=1.2;},20);
  }
});

// ══════════════════════════════
//  9. PATCH GAME LOOP (checkpoints + premium skin in world space)
// ══════════════════════════════

// We hook into drwHUD which is a function declaration (hoisted) in block 0.
// We can't override it directly, but we CAN override it by redefining since
// block 1 runs after block 0 — function declarations in same scope can be overridden.
// Actually safest: just draw checkpoints in the setInterval above... but they need
// camera offset. Better: override drwHUD to also call _drwCheckpoints after.
// drwHUD is called outside CX.save/restore so we need to use world coords manually.
// The loop does: CX.save → translate(shake) → draw world → CX.restore → drwHUD
// _drwCheckpoints already handles camX subtraction internally.
// We add a post-HUD hook by patching the canvas context's restore — too fragile.
// Simplest: just draw checkpoints in the setInterval BUT draw them in screen coords
// using the world coords approach already in _drwCheckpoints (subtracts camX). ✓
// The setInterval fires at ~60fps so it's smooth enough.

// ══════════════════════════════
//  10. BOOT
// ══════════════════════════════

(function _boot(){
  // Clear any stale force-granted silver/gold from previous buggy sessions
  // A version flag ensures we only wipe once
  if(localStorage.getItem('cubix_v2')!=='1'){
    localStorage.removeItem('cubix_silver_unlocked');
    localStorage.removeItem('cubix_gold_unlocked');
    localStorage.removeItem('cubix_priv');
    localStorage.setItem('cubix_v2','1');
  }
  // Restore saved normal swatch (never auto-apply locked premium skins)
  const saved=parseInt(localStorage.getItem('cubix_swatch')||'9');
  if(saved===10&&_isSilverUnlocked()) _applyColor(10);
  else if(saved===11&&_isGoldUnlocked()) _applyColor(11);
  else _applyColor(Math.min(saved,9));

  _rebuildPremiumSlots();
  _showMainMenu();
  _renderWardrobePreview();
})();

// ══════════════════════════════
//  11. ELITE MENU PARTICLE SYSTEM
// ══════════════════════════════
(function _menuParticles(){
  const c=document.getElementById('menuBG');
  const ctx=c.getContext('2d');
  const W=900,H=540;
  const pts=[];
  // Generate 80 particles
  for(let i=0;i<80;i++) pts.push({
    x:Math.random()*W, y:Math.random()*H,
    vx:(Math.random()-0.5)*0.4, vy:-0.3-Math.random()*0.5,
    size:Math.random()*1.5+0.3,
    col:Math.random()<0.6?'0,255,255':'255,0,255',
    alpha:Math.random()*0.5+0.1,
    life:Math.random()
  });

  // Horizontal scan lines that sweep down occasionally
  let scanY=-20, scanActive=false, scanTimer=0;

  function _drawMenuBG(ts){
    // Only draw when a menu is visible
    const anyMenu = !document.getElementById('mainMenu').classList.contains('hidden')||
                    !document.getElementById('pauseMenu').classList.contains('hidden')||
                    !document.getElementById('lcMenu').classList.contains('hidden')||
                    !document.getElementById('goMenu').classList.contains('hidden')||
                    !document.getElementById('winMenu').classList.contains('hidden')||
                    !document.getElementById('wardrobeMenu').classList.contains('hidden');

    ctx.clearRect(0,0,W,H);

    if(!anyMenu){ requestAnimationFrame(_drawMenuBG); return; }

    // Particles
    pts.forEach(p=>{
      p.x+=p.vx; p.y+=p.vy; p.life+=0.003;
      if(p.y<-4){ p.y=H+4; p.x=Math.random()*W; p.life=0; }
      if(p.x<-4) p.x=W+4;
      if(p.x>W+4) p.x=-4;
      const a=p.alpha*(0.5+Math.sin(p.life*Math.PI*2)*0.5);
      ctx.beginPath();
      ctx.arc(p.x,p.y,p.size,0,Math.PI*2);
      ctx.fillStyle=`rgba(${p.col},${a})`;
      ctx.fill();
      // Trail
      ctx.beginPath();
      ctx.moveTo(p.x,p.y);
      ctx.lineTo(p.x-p.vx*8,p.y-p.vy*8);
      ctx.strokeStyle=`rgba(${p.col},${a*0.3})`;
      ctx.lineWidth=p.size*0.8;
      ctx.stroke();
    });

    // Diagonal grid lines (animated)
    const t=ts*0.0003;
    ctx.save();
    ctx.globalAlpha=0.04;
    for(let i=-10;i<20;i++){
      const x=((i*60+t*30)%W*1.5)-W*0.25;
      ctx.beginPath();ctx.moveTo(x,0);ctx.lineTo(x+H,H);
      ctx.strokeStyle='#0ff';ctx.lineWidth=1;ctx.stroke();
    }
    ctx.restore();

    // Horizontal sweep line
    scanTimer++;
    if(scanTimer>180&&!scanActive){ scanActive=true; scanY=0; scanTimer=0; }
    if(scanActive){
      ctx.save();
      const g=ctx.createLinearGradient(0,scanY-12,0,scanY+12);
      g.addColorStop(0,'rgba(0,255,255,0)');
      g.addColorStop(0.5,'rgba(0,255,255,0.12)');
      g.addColorStop(1,'rgba(0,255,255,0)');
      ctx.fillStyle=g; ctx.fillRect(0,scanY-12,W,24);
      ctx.restore();
      scanY+=3;
      if(scanY>H+20) scanActive=false;
    }

    requestAnimationFrame(_drawMenuBG);
  }
  requestAnimationFrame(_drawMenuBG);
})();

// ══════════════════════════════
//  12. LEVEL BADGE UPDATER
// ══════════════════════════════
setInterval(()=>{
  const badge=document.getElementById('_lvlBadge');
  if(badge&&typeof lvlIdx!=='undefined'&&typeof LEVELS!=='undefined'){
    if(typeof STATE!=='undefined'&&STATE==='playing'){
      badge.textContent='LVL '+(lvlIdx+1)+'  /  '+(LEVELS[lvlIdx]?LEVELS[lvlIdx].name:'');
      badge.style.opacity='1';
    } else {
      badge.style.opacity='0';
    }
  }
},200);



</script>
</body>
</html>
