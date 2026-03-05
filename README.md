<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=3.0, user-scalable=yes">
<meta name="screen-orientation" content="landscape">
<meta name="x5-orientation" content="landscape">
<meta name="full-screen" content="yes">
<meta name="browsermode" content="application">
<title>CUBIX — Neon Platformer</title>
<link href="https://fonts.googleapis.com/css2?family=Orbitron:wght@400;700;900&family=Share+Tech+Mono&display=swap" rel="stylesheet">
<style>
*,*::before,*::after{box-sizing:border-box;margin:0;padding:0}

body {
  background: #000;
  font-family: 'Orbitron', monospace;
  color: #0ff;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  min-height: 100vh;
  /* Scrollable — user can drag to reposition */
  overflow-x: hidden;
  overflow-y: auto;
}

/* Wrapper stacks game canvas + control bar */
#gameWrapper {
  display: flex;
  flex-direction: column;
  align-items: center;
  width: 100%;
}

/* ── Outer container with premium edge glow ── */
#gameContainer{
  position:relative;
  width: 900px;
  height: 540px;
  /* Scale down to fit viewport width on smaller screens */
  max-width: 100vw;
  overflow:hidden;
  box-shadow:
    0 0 0 1px rgba(0,255,255,0.18),
    0 0 40px rgba(0,255,255,0.12),
    0 0 100px rgba(0,255,255,0.06),
    0 0 0 3px rgba(255,0,255,0.04),
    inset 0 0 80px rgba(0,0,0,0.6);
  flex-shrink: 0;
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

/* ── LEVEL BADGE ── */
#_lvlBadge{
  position:absolute;top:8px;left:50%;transform:translateX(-50%);
  z-index:5;font-family:'Share Tech Mono',monospace;font-size:9px;
  letter-spacing:4px;color:rgba(255,255,255,0.2);
  pointer-events:none;
}

/* ══════════════════════════════════════════════════
   MOBILE CONTROLS — Landscape-First Layout
   ══════════════════════════════════════════════════ */

/* ── Portrait blocker: show rotate message ── */
#portraitBlock {
  display: none;
  position: fixed; inset: 0; z-index: 9999;
  background: #000;
  flex-direction: column;
  align-items: center; justify-content: center;
  gap: 20px;
}
#portraitBlock .rot-icon { font-size: 60px; animation: rotHint 1.6s ease-in-out infinite; }
@keyframes rotHint {
  0%,100% { transform: rotate(0deg); }
  50%      { transform: rotate(-90deg); }
}
#portraitBlock p {
  font-family: 'Orbitron', monospace;
  color: rgba(0,255,255,0.65);
  font-size: 11px;
  letter-spacing: 4px;
  text-align: center;
  line-height: 2.2;
}
@media (orientation: portrait) and (hover: none) and (pointer: coarse) {
  #portraitBlock { display: flex !important; }
}

/* ── Mobile control bar: hidden by default, shown on touch ── */
#mobileControls {
  display: none;
  width: 100%;
  height: 150px;
  position: relative;
  pointer-events: none;
  user-select: none;
  -webkit-user-select: none;
  flex-shrink: 0;
  /* Small gap so controls are visually separated & pushed down */
  margin-top: 8px;
}

/* ── On touch devices in landscape: fill viewport exactly ── */
@media (hover: none) and (pointer: coarse) and (orientation: landscape) {
  html, body {
    /* No bounce-scrolling during play but allow reposition */
    height: auto;
    overflow-x: hidden;
    overflow-y: auto;
  }
  #mobileControls { display: flex !important; }
  #gameContainer {
    width: 100vw !important;
    max-width: 100vw !important;
    /* Viewport height minus control bar (150) minus gap (8) minus margin (2) */
    height: calc(100vh - 164px) !important;
    min-height: 160px !important;
  }
  #gameContainer canvas {
    width: 100% !important;
    height: 100% !important;
  }
}

/* ── Also show controls on any touch device (for testing in browsers) ── */
@media (hover: none) and (pointer: coarse) {
  #mobileControls { display: flex !important; }
}

.ctrl-zone {
  position: absolute;
  bottom: 0;
  pointer-events: all;
  display: flex;
  align-items: flex-end;
}

/* padding-bottom pushes buttons down from the top edge of the bar */
#ctrlLeft  { left: 20px;  flex-direction: row;    align-items: flex-end; gap: 10px; padding-bottom: 12px; }
#ctrlRight { right: 20px; flex-direction: column; align-items: center;   gap: 8px;  padding-bottom: 12px; }

.ctrl-btn {
  border-radius: 50%;
  border: 2px solid rgba(0,255,255,0.4);
  background: rgba(0,0,0,0.55);
  backdrop-filter: blur(10px);
  -webkit-backdrop-filter: blur(10px);
  display: flex; align-items: center; justify-content: center;
  pointer-events: all;
  touch-action: none;
  -webkit-tap-highlight-color: transparent;
  cursor: pointer;
  transition: transform 0.08s, box-shadow 0.1s, border-color 0.1s, background 0.1s;
  position: relative;
  overflow: hidden;
}

/* Subtle inner rim */
.ctrl-btn::before {
  content: '';
  position: absolute; inset: 4px;
  border-radius: 50%;
  border: 1px solid rgba(0,255,255,0.1);
  pointer-events: none;
  transition: border-color 0.1s;
}
.ctrl-btn.pressed::before {
  border-color: rgba(0,255,255,0.4);
}

/* Glow fill on press */
.ctrl-btn::after {
  content: '';
  position: absolute; inset: 0;
  border-radius: 50%;
  background: radial-gradient(circle, rgba(0,255,255,0.0) 0%, transparent 70%);
  transition: background 0.12s;
  pointer-events: none;
}
.ctrl-btn.pressed::after {
  background: radial-gradient(circle, rgba(0,255,255,0.14) 0%, transparent 70%);
}

/* ── Move buttons — neon cyan ── */
#btnLeft, #btnRight {
  width: 68px; height: 68px;
  box-shadow: 0 0 8px rgba(0,255,255,0.12), inset 0 0 10px rgba(0,0,0,0.6);
}
#btnLeft.pressed, #btnRight.pressed {
  transform: scale(0.87);
  border-color: rgba(0,255,255,0.95);
  box-shadow: 0 0 22px rgba(0,255,255,0.65), 0 0 50px rgba(0,255,255,0.22), inset 0 0 12px rgba(0,255,255,0.1);
  background: rgba(0,20,20,0.65);
}

/* ── Jump button — magenta, biggest ── */
#btnJump {
  width: 76px; height: 76px;
  border-color: rgba(255,0,200,0.45);
  background: rgba(8,0,10,0.6);
  box-shadow: 0 0 12px rgba(255,0,200,0.18), inset 0 0 12px rgba(0,0,0,0.6);
}
#btnJump::before { border-color: rgba(255,0,200,0.12); }
#btnJump::after  { background: radial-gradient(circle, rgba(255,0,200,0.0) 0%, transparent 70%); }
#btnJump.pressed {
  transform: scale(0.86);
  border-color: rgba(255,0,255,0.95);
  box-shadow: 0 0 28px rgba(255,0,255,0.72), 0 0 60px rgba(255,0,255,0.22), inset 0 0 14px rgba(255,0,200,0.15);
  background: rgba(15,0,15,0.7);
}
#btnJump.pressed::after { background: radial-gradient(circle, rgba(255,0,200,0.18) 0%, transparent 70%); }

/* ── Fly button — electric blue, owner only ── */
#btnFly {
  width: 54px; height: 54px;
  border-color: rgba(60,140,255,0.45);
  background: rgba(0,4,18,0.6);
  box-shadow: 0 0 10px rgba(60,140,255,0.15), inset 0 0 10px rgba(0,0,0,0.6);
  display: none;
}
#btnFly::before { border-color: rgba(60,140,255,0.1); }
#btnFly.owner-visible { display: flex; }
#btnFly.pressed {
  transform: scale(0.87);
  border-color: rgba(100,180,255,0.95);
  box-shadow: 0 0 24px rgba(80,160,255,0.72), 0 0 55px rgba(60,140,255,0.25);
}
#btnFly.fly-active {
  border-color: rgba(100,200,255,0.8);
  box-shadow: 0 0 20px rgba(80,180,255,0.55), 0 0 45px rgba(60,160,255,0.2);
  animation: flyActivePulse 1.4s ease-in-out infinite;
}
@keyframes flyActivePulse {
  0%,100% { box-shadow: 0 0 14px rgba(80,180,255,0.5), 0 0 35px rgba(60,160,255,0.15); }
  50%      { box-shadow: 0 0 28px rgba(100,200,255,0.8), 0 0 65px rgba(80,180,255,0.28); }
}

/* ── Immortal / Shield button — gold, owner only ── */
#btnImmort {
  width: 60px; height: 60px;
  border-color: rgba(220,170,0,0.4);
  background: rgba(10,7,0,0.62);
  box-shadow: 0 0 10px rgba(220,170,0,0.12), inset 0 0 10px rgba(0,0,0,0.6);
  display: none;
}
#btnImmort::before { border-color: rgba(220,170,0,0.1); }
#btnImmort.owner-visible { display: flex; }
#btnImmort.pressed {
  transform: scale(0.87);
  border-color: rgba(255,220,0,0.95);
  box-shadow: 0 0 24px rgba(255,200,0,0.72), 0 0 55px rgba(220,160,0,0.25);
}
#btnImmort.immort-active {
  border-color: rgba(255,220,0,0.85);
  background: rgba(20,14,0,0.7);
  animation: shieldPulse 1.2s ease-in-out infinite;
}
@keyframes shieldPulse {
  0%,100% { box-shadow: 0 0 16px rgba(255,200,0,0.55), 0 0 40px rgba(220,160,0,0.18); }
  50%      { box-shadow: 0 0 32px rgba(255,220,0,0.85), 0 0 72px rgba(255,180,0,0.3); }
}

/* ── Right column bottom row: immortal + jump ── */
#ctrlRightBottom {
  display: flex;
  flex-direction: row;
  align-items: center;
  gap: 10px;
}

/* ── Redeem button — TOP RIGHT corner ── */
#_redeemBtn {
  position: fixed !important;
  top: 10px !important;
  right: 10px !important;
  bottom: auto !important;
  left: auto !important;
  z-index: 200 !important;
  font-family: 'Share Tech Mono', monospace !important;
  font-size: 9px !important;
  letter-spacing: 1px !important;
  color: rgba(0,255,255,0.2) !important;
  background: rgba(0,0,0,0.55) !important;
  border: 1px solid rgba(0,255,255,0.08) !important;
  padding: 5px 10px !important;
  cursor: pointer !important;
  transition: color 0.2s, border-color 0.2s, box-shadow 0.2s !important;
}
#_redeemBtn:hover {
  color: rgba(0,255,255,0.65) !important;
  border-color: rgba(0,255,255,0.35) !important;
  box-shadow: 0 0 10px rgba(0,255,255,0.12) !important;
}

/* Priv badge top-right */
#_privBadge {
  position: fixed !important;
  top: 36px !important;
  right: 10px !important;
  bottom: auto !important;
  left: auto !important;
  z-index: 200 !important;
}
#_privFlash {
  position: fixed !important;
  top: 62px !important;
  right: 10px !important;
  bottom: auto !important;
  left: auto !important;
  z-index: 200 !important;
}
</style>
</head>
<body>

<!-- Portrait rotation prompt -->
<div id="portraitBlock">
  <div class="rot-icon">📱</div>
  <p>ROTATE YOUR DEVICE<br>TO LANDSCAPE MODE<br>TO PLAY CUBIX</p>
</div>

<div id="gameWrapper">
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
     MOBILE CONTROLS — Elite Layout
     ═══════════════════════════════════════════════ -->
<div id="mobileControls">

  <!-- LEFT ZONE: move left + move right -->
  <div class="ctrl-zone" id="ctrlLeft">

    <button class="ctrl-btn" id="btnLeft" aria-label="Move Left">
      <svg width="34" height="34" viewBox="0 0 34 34" fill="none">
        <defs>
          <filter id="f1" x="-80%" y="-80%" width="260%" height="260%">
            <feGaussianBlur stdDeviation="2" result="b"/>
            <feMerge><feMergeNode in="b"/><feMergeNode in="SourceGraphic"/></feMerge>
          </filter>
        </defs>
        <polygon points="24,6 10,17 24,28" fill="rgba(0,255,255,0.92)" filter="url(#f1)"/>
        <line x1="10" y1="6" x2="10" y2="28" stroke="rgba(0,255,255,0.35)" stroke-width="2" stroke-linecap="round"/>
      </svg>
    </button>

    <button class="ctrl-btn" id="btnRight" aria-label="Move Right">
      <svg width="34" height="34" viewBox="0 0 34 34" fill="none">
        <defs>
          <filter id="f2" x="-80%" y="-80%" width="260%" height="260%">
            <feGaussianBlur stdDeviation="2" result="b"/>
            <feMerge><feMergeNode in="b"/><feMergeNode in="SourceGraphic"/></feMerge>
          </filter>
        </defs>
        <polygon points="10,6 24,17 10,28" fill="rgba(0,255,255,0.92)" filter="url(#f2)"/>
        <line x1="24" y1="6" x2="24" y2="28" stroke="rgba(0,255,255,0.35)" stroke-width="2" stroke-linecap="round"/>
      </svg>
    </button>

  </div>

  <!-- RIGHT ZONE: Fly (top, owner only) · then [Immortal] [Jump] row -->
  <div class="ctrl-zone" id="ctrlRight">

    <!-- FLY — owner only, sits above jump -->
    <button class="ctrl-btn" id="btnFly" aria-label="Fly">
      <svg width="30" height="30" viewBox="0 0 30 30" fill="none">
        <defs>
          <filter id="fBlue" x="-80%" y="-80%" width="260%" height="260%">
            <feGaussianBlur stdDeviation="2.2" result="b"/>
            <feMerge><feMergeNode in="b"/><feMergeNode in="SourceGraphic"/></feMerge>
          </filter>
        </defs>
        <!-- Double chevron up = fly / double-jump icon -->
        <polyline points="5,22 15,11 25,22" stroke="rgba(100,190,255,0.95)" stroke-width="2.8"
          stroke-linecap="round" stroke-linejoin="round" fill="none" filter="url(#fBlue)"/>
        <polyline points="5,15 15,4 25,15" stroke="rgba(160,220,255,0.65)" stroke-width="2"
          stroke-linecap="round" stroke-linejoin="round" fill="none"/>
        <!-- Small dot at tip -->
        <circle cx="15" cy="4" r="1.8" fill="rgba(200,240,255,0.8)"/>
      </svg>
    </button>

    <!-- Bottom row: immortal shield (left) + jump (right) -->
    <div id="ctrlRightBottom">

      <!-- IMMORTAL shield — owner only -->
      <button class="ctrl-btn" id="btnImmort" aria-label="Immortal Shield">
        <svg width="32" height="32" viewBox="0 0 32 32" fill="none">
          <defs>
            <filter id="fGold" x="-60%" y="-60%" width="220%" height="220%">
              <feGaussianBlur stdDeviation="2" result="b"/>
              <feMerge><feMergeNode in="b"/><feMergeNode in="SourceGraphic"/></feMerge>
            </filter>
            <linearGradient id="shieldGrad" x1="0%" y1="0%" x2="100%" y2="100%">
              <stop offset="0%" stop-color="rgba(255,230,80,0.9)"/>
              <stop offset="100%" stop-color="rgba(200,140,0,0.9)"/>
            </linearGradient>
          </defs>
          <!-- Shield outline -->
          <path d="M16 3 L27 7.5 L27 16 C27 22.5 22 27.5 16 29.5 C10 27.5 5 22.5 5 16 L5 7.5 Z"
                stroke="url(#shieldGrad)" stroke-width="2" fill="rgba(255,190,0,0.1)"
                stroke-linejoin="round" filter="url(#fGold)"/>
          <!-- Checkmark inside shield -->
          <polyline points="11,16 14.5,19.5 21,13" stroke="rgba(255,230,80,0.95)"
                stroke-width="2.2" stroke-linecap="round" stroke-linejoin="round" fill="none"/>
          <!-- Star accent at top -->
          <circle cx="16" cy="10" r="2" fill="rgba(255,220,60,0.7)"/>
        </svg>
      </button>

      <!-- JUMP — main action button -->
      <button class="ctrl-btn" id="btnJump" aria-label="Jump">
        <svg width="38" height="38" viewBox="0 0 38 38" fill="none">
          <defs>
            <filter id="fMag" x="-60%" y="-60%" width="220%" height="220%">
              <feGaussianBlur stdDeviation="2.5" result="b"/>
              <feMerge><feMergeNode in="b"/><feMergeNode in="SourceGraphic"/></feMerge>
            </filter>
            <linearGradient id="jumpGrad" x1="50%" y1="0%" x2="50%" y2="100%">
              <stop offset="0%" stop-color="rgba(255,80,220,0.95)"/>
              <stop offset="100%" stop-color="rgba(200,0,180,0.85)"/>
            </linearGradient>
          </defs>
          <!-- Outer orbit ring -->
          <circle cx="19" cy="19" r="13" stroke="rgba(255,0,200,0.25)" stroke-width="1.5"
            fill="none" stroke-dasharray="5 3"/>
          <!-- Arrow body -->
          <polygon points="19,5 30,21 8,21" fill="url(#jumpGrad)" filter="url(#fMag)"/>
          <!-- Stem -->
          <rect x="16.5" y="21" width="5" height="7" rx="2.5" fill="rgba(255,0,200,0.65)"/>
          <!-- Glow dot at tip -->
          <circle cx="19" cy="5" r="2.5" fill="rgba(255,160,240,0.8)"/>
        </svg>
      </button>

    </div>
  </div>

</div><!-- /mobileControls -->
</div><!-- /gameWrapper -->

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

const LEVELS=[
  {name:'BOOT SEQUENCE',bgHue:180,
   platforms:[p(0,492,420),p(460,436,120),p(632,380,110),p(802,325,120),p(992,436,100),p(1112,375,200),p(1362,344,160)],
   orbs:[ob(200,478),ob(510,422),ob(850,311),ob(1050,422),ob(1200,361),ob(1440,330)],
   spikes:[],
   start:{x:50,y:464},goal:{x:1490,y:330}},
  {name:'GRID NETWORK',bgHue:200,
   platforms:[p(0,492,300),p(362,436,100),p(512,380,100),p(662,436,80),p(792,348,100),p(942,400,100),p(1092,318,120),p(1262,368,100),p(1412,300,150),p(1612,348,120)],
   orbs:[ob(150,478),ob(410,422),ob(840,334),ob(1000,386),ob(1150,304),ob(1460,286),ob(1660,334)],
   spikes:[sp(702,420,56)],
   start:{x:50,y:464},goal:{x:1680,y:334}},
  {name:'PHASE SHIFT',bgHue:260,
   platforms:[p(0,492,300),p(382,424,100,{type:'moving',moveX:80,speed:1.2}),p(572,370,90),p(712,424,90,{type:'moving',moveX:60,speed:1.5}),p(912,348,120),p(1112,400,80,{type:'moving',moveX:100,speed:1.0}),p(1312,318,100),p(1492,378,80,{type:'moving',moveX:70,speed:1.8}),p(1662,298,150)],
   orbs:[ob(150,478),ob(432,410),ob(617,356),ob(762,410),ob(970,334),ob(1360,304),ob(1710,284)],
   spikes:[sp(616,354,48),sp(962,332,38)],
   start:{x:50,y:464},goal:{x:1740,y:284}},
  {name:'DECAY PROTOCOL',bgHue:300,
   platforms:[p(0,492,300),p(372,436,100,{type:'crumble'}),p(532,390,90,{type:'crumble'}),p(692,436,90),p(832,378,80,{type:'crumble'}),p(972,328,120),p(1132,378,90,{type:'crumble'}),p(1292,328,100),p(1472,378,80,{type:'crumble'}),p(1612,298,180)],
   orbs:[ob(150,478),ob(580,376),ob(730,422),ob(1020,314),ob(1330,314),ob(1680,284)],
   spikes:[sp(740,420,48),sp(1016,312,38)],
   start:{x:50,y:464},goal:{x:1700,y:284}},
  {name:'SPIKE MATRIX',bgHue:0,
   platforms:[p(0,492,300),p(372,420,80),p(512,368,100),p(672,420,80),p(812,358,100),p(972,420,80),p(1112,348,120),p(1292,398,80,{type:'moving',moveX:60,speed:1.6}),p(1462,328,100),p(1652,378,80,{type:'crumble'}),p(1792,308,160)],
   orbs:[ob(560,354),ob(860,344),ob(1160,334),ob(1500,314),ob(1840,294)],
   spikes:[sp(386,404,38),sp(530,352,38),sp(830,342,38),sp(1130,332,38),sp(1670,362,38)],
   start:{x:50,y:464},goal:{x:1880,y:294}},
  {name:'MULTI-PATH',bgHue:140,
   platforms:[p(0,492,280),p(352,388,80,{type:'moving',moveX:80,speed:1.4}),p(532,440,80,{type:'crumble'}),p(672,378,90),p(822,440,80,{type:'crumble'}),p(952,358,120,{type:'moving',moveX:100,speed:1.2}),p(1132,430,80,{type:'crumble'}),p(1272,358,90),p(1432,308,90,{type:'moving',moveX:80,speed:2.0}),p(1612,358,80,{type:'crumble'}),p(1752,278,180)],
   orbs:[ob(140,478),ob(395,374),ob(710,364),ob(1005,344),ob(1460,294),ob(1840,264)],
   spikes:[sp(570,424,38),sp(860,424,38),sp(1170,414,38),sp(1650,342,38)],
   start:{x:50,y:464},goal:{x:1840,y:264}},
  {name:'VELOCITY GRID',bgHue:220,
   platforms:[p(0,468,200),p(292,408,80,{type:'moving',moveX:120,speed:2.5}),p(492,358,70,{type:'moving',moveX:90,speed:2.8}),p(692,408,80),p(832,348,80,{type:'moving',moveX:110,speed:3.0}),p(1032,398,80,{type:'crumble'}),p(1172,328,90,{type:'moving',moveX:100,speed:2.2}),p(1392,378,80,{type:'crumble'}),p(1532,298,100,{type:'moving',moveX:80,speed:2.6}),p(1732,348,80,{type:'crumble'}),p(1872,258,180)],
   orbs:[ob(100,454),ob(332,394),ob(527,344),ob(730,394),ob(1210,314),ob(1575,284),ob(1940,244)],
   spikes:[sp(710,392,48),sp(1070,382,38),sp(1430,362,38),sp(1770,332,38)],
   start:{x:50,y:440},goal:{x:1960,y:244}},
  {name:'CRUMBLE STORM',bgHue:30,
   platforms:[p(0,468,180),p(262,408,70,{type:'crumble'}),p(402,358,70,{type:'crumble'}),p(542,308,70,{type:'crumble'}),p(682,358,80),p(822,308,70,{type:'crumble'}),p(962,358,80,{type:'moving',moveX:100,speed:2.0}),p(1142,298,70,{type:'crumble'}),p(1282,348,80,{type:'moving',moveX:90,speed:2.5}),p(1462,278,70,{type:'crumble'}),p(1602,328,80,{type:'moving',moveX:80,speed:3.0}),p(1782,258,200)],
   orbs:[ob(90,454),ob(295,394),ob(436,344),ob(575,294),ob(715,344),ob(1180,284),ob(1830,244)],
   spikes:[sp(692,342,38),sp(1012,342,38),sp(1652,312,38)],
   start:{x:50,y:440},goal:{x:1880,y:244}},
  {name:'SYSTEM BREACH',bgHue:280,
   platforms:[p(0,468,160),p(232,398,70,{type:'moving',moveX:80,speed:2.5}),p(412,348,60,{type:'crumble'}),p(552,398,70,{type:'moving',moveX:70,speed:3.2}),p(732,328,70,{type:'crumble'}),p(882,388,70,{type:'moving',moveX:100,speed:2.8}),p(1072,308,70,{type:'crumble'}),p(1212,368,70,{type:'moving',moveX:80,speed:3.5}),p(1402,288,70,{type:'crumble'}),p(1542,348,70,{type:'moving',moveX:90,speed:2.4}),p(1732,268,80,{type:'crumble'}),p(1872,218,180)],
   orbs:[ob(80,454),ob(265,384),ob(587,384),ob(766,314),ob(917,374),ob(1245,354),ob(1577,334),ob(1940,204)],
   spikes:[sp(442,332,38),sp(772,312,38),sp(1112,292,38),sp(1442,272,38),sp(1770,252,38)],
   start:{x:50,y:440},goal:{x:1960,y:204}},
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

  if(P.vy<0&&!(K['Space']||K['ArrowUp']||K['KeyW'])) P.vy+=GR*1.7*dt;
  else P.vy+=GR*dt;
  P.vy=Math.min(P.vy,MXF);

  if(P.onGnd) P.coyT=COY;
  else if(P.coyT>0) P.coyT-=dt;

  if(jmp) P.jBufT=JBF;
  else if(P.jBufT>0) P.jBufT-=dt;

  if(P.jBufT>0){
    if(P.coyT>0||P.onGnd){
      P.vy=JF; P.jumps=1; P.coyT=0; P.jBufT=0;
      S.jump(); burst(P.x+PW/2,P.y+PH,14,_CUBE_COLOR,3.5,0.4,5);
    } else if(P.jumps>0){
      P.vy=DJF; P.jumps=0; P.jBufT=0;
      S.dJump(); burst(P.x+PW/2,P.y+PH/2,22,'#f0f',4.5,0.5,6);
    }
  }

  P.x+=P.vx*dt; P.y+=P.vy*dt;
  const wasGnd=P.onGnd;
  P.onGnd=false;

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

  spkList.forEach(sk=>{
    if(hit(P.x+3,P.y+4,PW-6,PH-5,sk.x,sk.y,sk.w,sk.h)) killP();
  });

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

  if(P.y>H+160) killP();

  P.trailT+=dt;
  if(Math.abs(P.vx)>65&&P.trailT>0.032){
    P.trailT=0; burst(P.x+PW/2,P.y+PH/2,3,_CUBE_COLOR,0.8,0.22,3,0);
  }

  P.glowT+=dt*3;

  if(comboT>0){ comboT-=dt; if(comboT<=0) combo=1; }

  camXT=Math.max(0,P.x-W/3);
  camX+=(camXT-camX)*Math.min(1,dt*7);

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

(()=>{
  const sl=document.getElementById('scanlineCanvas');
  const sc=sl.getContext('2d');
  sc.fillStyle='#000';
  for(let y=0;y<H;y+=2) sc.fillRect(0,y,W,1);
})();

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
  _cpX=null; _cpY=null; _cpLvl=null;
  initLvl(lvlIdx); STATE='playing'; showMenu(null); startAmbient();
  _gameStarted=true; _saveResume();
}

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

document.getElementById('hiV').textContent=hiScore;
showMenu('mainMenu');
requestAnimationFrame(ts=>{ last=ts; loop(ts); });
</script>

<!-- ═══════════════════════════════════════════════
     NEW FEATURES — injected cleanly below original
     ═══════════════════════════════════════════════ -->
<script>
'use strict';

// ═══════════════════════════════════════════════
//  CUBIX — NEW FEATURES BLOCK
// ═══════════════════════════════════════════════

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

const _SILVER_KEY = 'cubix_silver_unlocked';
const _GOLD_KEY   = 'cubix_gold_unlocked';
function _isSilverUnlocked(){ return localStorage.getItem(_SILVER_KEY)==='1'; }
function _isGoldUnlocked()  { return localStorage.getItem(_GOLD_KEY)==='1'; }

let _cubeSwatchIdx = parseInt(localStorage.getItem('cubix_swatch')||'9');
if(isNaN(_cubeSwatchIdx)||_cubeSwatchIdx<0||_cubeSwatchIdx>9) _cubeSwatchIdx=9;

let _CUBE_COLOR  = SWATCHES[_cubeSwatchIdx].hex;
let _cubeLight   = SWATCHES[_cubeSwatchIdx].light;
let _cubeDark    = SWATCHES[_cubeSwatchIdx].dark;
let _cubeStroke  = SWATCHES[_cubeSwatchIdx].stroke;
let _cubeHUDFill = 'rgba(0,255,255,0.16)';

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
  if(idx>9){ idx=9; _cubeSwatchIdx=9; }
  const sw=SWATCHES[idx];
  _CUBE_COLOR=sw.hex; _cubeLight=sw.light; _cubeDark=sw.dark; _cubeStroke=sw.stroke;
  _cubeHUDFill=_hexToRGBA(sw.hex,0.16);
  localStorage.setItem('cubix_swatch',idx);
  _renderWardrobePreview();
}

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

function _rebuildPremiumSlots(){
  const old=document.getElementById('_premiumRow');
  if(old)old.remove();
  const row=document.createElement('div');
  row.className='wd-premium-row';row.id='_premiumRow';
  document.getElementById('bWdBack').parentNode.insertBefore(row,document.getElementById('bWdBack'));

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
//  6. EASY MODE (5 lives)
// ══════════════════════════════

const _origStartGame=startGame;
window.startGame=function(from=0){ _origStartGame(from); lives=5; };
document.getElementById('bPlay').addEventListener('click',()=>setTimeout(()=>{lives=Math.max(lives,5);},30));
document.getElementById('bRe').addEventListener('click',()=>setTimeout(()=>{lives=Math.max(lives,5);},30));

// ══════════════════════════════
//  7. ADMIN / OWNER SYSTEM
//  SHIFT+L = level select (admin+)
//  SHIFT+F = fly toggle (owner only)
//  SHIFT+I = immortal toggle (owner only)
// ══════════════════════════════

let _priv='none', _immortal=false, _flying=false;
function _isAdmin(){ return _priv==='admin'||_priv==='owner'; }
function _isOwner(){ return _priv==='owner'; }

// Redeem UI — positioned top-right via CSS override
(function _buildRedeemUI(){
  const style=document.createElement('style');
  style.textContent=`
#_redeemModal{position:fixed;inset:0;z-index:300;background:rgba(0,0,0,0.92);display:flex;flex-direction:column;align-items:center;justify-content:center;gap:14px;}
#_redeemModal.hidden{display:none;}
#_redeemTitle{font-family:'Orbitron',monospace;font-size:16px;letter-spacing:5px;color:rgba(0,255,255,0.4);font-weight:700;}
#_redeemInput{font-family:'Share Tech Mono',monospace;font-size:14px;background:#050505;border:1px solid rgba(0,255,255,0.15);color:rgba(0,255,255,0.7);padding:9px 18px;width:270px;letter-spacing:3px;text-align:center;outline:none;transition:border-color 0.2s;}
#_redeemInput:focus{border-color:rgba(0,255,255,0.5);color:#0ff;box-shadow:0 0 10px rgba(0,255,255,0.1);}
#_redeemMsg{font-family:'Share Tech Mono',monospace;font-size:11px;color:rgba(0,255,255,0.3);letter-spacing:2px;min-height:16px;}
#_redeemClose{font-family:'Share Tech Mono',monospace;font-size:10px;color:rgba(0,255,255,0.25);background:none;border:1px solid rgba(0,255,255,0.1);padding:5px 16px;cursor:pointer;letter-spacing:2px;transition:all 0.2s;}
#_redeemClose:hover{color:rgba(0,255,255,0.6);border-color:rgba(0,255,255,0.35);}
  `;
  document.head.appendChild(style);

  const btn=document.createElement('button');
  btn.id='_redeemBtn';
  btn.textContent='redeem code';
  document.body.appendChild(btn);

  const badge=document.createElement('div');badge.id='_privBadge';
  badge.style.cssText='position:fixed;top:36px;right:10px;z-index:200;font-family:"Share Tech Mono",monospace;font-size:9px;letter-spacing:2px;padding:3px 8px;pointer-events:none;opacity:0;transition:opacity 0.3s;';
  document.body.appendChild(badge);

  const flash=document.createElement('div');flash.id='_privFlash';
  flash.style.cssText='position:fixed;top:62px;right:10px;z-index:200;font-family:"Share Tech Mono",monospace;font-size:10px;letter-spacing:2px;pointer-events:none;opacity:0;transition:opacity 0.3s;padding:3px 8px;background:rgba(0,0,0,0.8);border:1px solid rgba(0,255,255,0.1);';
  document.body.appendChild(flash);

  const modal=document.createElement('div');modal.id='_redeemModal';modal.className='hidden';
  modal.innerHTML=`
    <div id="_redeemTitle">◈ REDEEM CODE ◈</div>
    <input id="_redeemInput" type="text" placeholder="ENTER ACCESS CODE" autocomplete="off" spellcheck="false">
    <div id="_redeemMsg"></div>
    <button id="_redeemClose">✕ &nbsp;CLOSE</button>
  `;
  document.body.appendChild(modal);

  btn.addEventListener('click',()=>{ modal.classList.remove('hidden'); document.getElementById('_redeemInput').value=''; document.getElementById('_redeemMsg').textContent=''; document.getElementById('_redeemInput').focus(); });
  document.getElementById('_redeemClose').addEventListener('click',()=>modal.classList.add('hidden'));

  document.getElementById('_redeemInput').addEventListener('keydown',e=>{
    if(e.key!=='Enter')return;
    const code=e.target.value.trim().toUpperCase();
    const msg=document.getElementById('_redeemMsg');
    if(code==='TDVADMIN'){ _priv='admin'; _applyPriv(); msg.style.color='rgba(0,255,100,0.7)'; msg.textContent='✓ ADMIN ACCESS GRANTED'; setTimeout(()=>modal.classList.add('hidden'),1200); }
    else if(code==='TDVOWNER_2014'){ _priv='owner'; _applyPriv(); msg.style.color='rgba(255,200,0,0.7)'; msg.textContent='✓ OWNER ACCESS GRANTED'; setTimeout(()=>modal.classList.add('hidden'),1200); }
    else if(code==='TDVNORMAL'){ _priv='none'; _immortal=false; _flying=false; _applyPriv(); msg.style.color='rgba(180,180,180,0.5)'; msg.textContent='✓ PRIVILEGES REMOVED'; setTimeout(()=>modal.classList.add('hidden'),1000); }
    else { msg.style.color='rgba(255,50,50,0.7)'; msg.textContent='✗ INVALID CODE'; }
  });
})();

function _applyPriv(){
  const badge=document.getElementById('_privBadge');
  if(_priv==='owner'){ badge.textContent='[ OWNER ]';badge.style.color='rgba(255,200,0,0.5)';badge.style.border='1px solid rgba(255,200,0,0.15)';badge.style.opacity='1'; }
  else if(_priv==='admin'){ badge.textContent='[ ADMIN ]';badge.style.color='rgba(0,255,100,0.45)';badge.style.border='1px solid rgba(0,255,100,0.12)';badge.style.opacity='1'; }
  else { badge.style.opacity='0'; _flying=false; _immortal=false; }
}

function _flashPriv(txt,col){
  const el=document.getElementById('_privFlash');
  if(!el)return; el.style.color=col; el.textContent=txt; el.style.opacity='1';
  clearTimeout(el._t); el._t=setTimeout(()=>{el.style.opacity='0';},1800);
}

// Keybinds — SHIFT+L admin+ | SHIFT+F, SHIFT+I owner only
window.addEventListener('keydown',e=>{
  if(!_isAdmin())return;
  // SHIFT+L = level warp (archived — functional but no UI prompt to keep it clean)
  if(e.shiftKey && e.code==='KeyL'){
    const n=parseInt(prompt('Warp to level (1-10):'));
    if(!isNaN(n)&&n>=1&&n<=10){
      _cpX=null;_cpY=null;_cpLvl=null; lvlIdx=n-1;
      score=0;lives=5;combo=1;comboT=0;dying=false;
      initLvl(lvlIdx);_initCheckpoints();STATE='playing';
      ['mainMenu','pauseMenu','lcMenu','goMenu','winMenu','wardrobeMenu'].forEach(id=>{const el=document.getElementById(id);if(el)el.classList.add('hidden');});
      try{startAmbient();}catch(e){}
      _flashPriv('→ LEVEL '+n,'rgba(0,255,100,0.7)');
    }
  }
  if(!_isOwner())return;
  if(e.shiftKey && e.code==='KeyI'){
    _immortal=!_immortal;
    _flashPriv(_immortal?'★ IMMORTAL ON':'★ IMMORTAL OFF','rgba(255,200,0,0.7)');
  }
  if(e.shiftKey && e.code==='KeyF'){
    _flying=!_flying;
    _flashPriv(_flying?'✦ FLY ON':'✦ FLY OFF','rgba(100,180,255,0.7)');
  }
});

// ══════════════════════════════
//  8. SETINTERVAL — per-frame extras
// ══════════════════════════════

setInterval(()=>{
  if(typeof STATE==='undefined'||STATE!=='playing')return;
  if(typeof P==='undefined'||!P)return;

  if(!P.dead) _checkCheckpoints();

  if(_immortal&&dying){ dying=false; P.dead=false; lives=Math.max(lives,1); }

  if(_flying&&!P.dead){
    const up=K['ArrowUp']||K['Space']||K['KeyW'];
    const dn=K['ArrowDown']||K['KeyS'];
    if(up)P.vy=-220; else if(dn)P.vy=220;
    else if(Math.abs(P.vy)>20)P.vy*=0.75;
    P.onGnd=false;
  }

  if(!P.dead&&P.vy>0) P.vy*=0.96;

  if(_usingSilver&&!P.dead) _drwSilverCube();
  if(_usingGold&&!P.dead)   _drwGoldCube();
},16);

window.addEventListener('keydown',e=>{
  if(typeof STATE==='undefined'||STATE!=='playing'||!P||P.dead)return;
  if(e.code==='Space'||e.code==='ArrowUp'||e.code==='KeyW'){
    setTimeout(()=>{if(P&&P.vy<0&&P.vy>-700)P.vy*=1.2;},20);
  }
});

// ══════════════════════════════
//  9. BOOT
// ══════════════════════════════

(function _boot(){
  if(localStorage.getItem('cubix_v2')!=='1'){
    localStorage.removeItem('cubix_silver_unlocked');
    localStorage.removeItem('cubix_gold_unlocked');
    localStorage.removeItem('cubix_priv');
    localStorage.setItem('cubix_v2','1');
  }
  const saved=parseInt(localStorage.getItem('cubix_swatch')||'9');
  if(saved===10&&_isSilverUnlocked()) _applyColor(10);
  else if(saved===11&&_isGoldUnlocked()) _applyColor(11);
  else _applyColor(Math.min(saved,9));

  _rebuildPremiumSlots();
  _showMainMenu();
  _renderWardrobePreview();
})();

// ══════════════════════════════
//  10. ELITE MENU PARTICLE SYSTEM
// ══════════════════════════════
(function _menuParticles(){
  const c=document.getElementById('menuBG');
  const ctx=c.getContext('2d');
  const W=900,H=540;
  const pts=[];
  for(let i=0;i<80;i++) pts.push({
    x:Math.random()*W, y:Math.random()*H,
    vx:(Math.random()-0.5)*0.4, vy:-0.3-Math.random()*0.5,
    size:Math.random()*1.5+0.3,
    col:Math.random()<0.6?'0,255,255':'255,0,255',
    alpha:Math.random()*0.5+0.1,
    life:Math.random()
  });

  let scanY=-20, scanActive=false, scanTimer=0;

  function _drawMenuBG(ts){
    const anyMenu = !document.getElementById('mainMenu').classList.contains('hidden')||
                    !document.getElementById('pauseMenu').classList.contains('hidden')||
                    !document.getElementById('lcMenu').classList.contains('hidden')||
                    !document.getElementById('goMenu').classList.contains('hidden')||
                    !document.getElementById('winMenu').classList.contains('hidden')||
                    !document.getElementById('wardrobeMenu').classList.contains('hidden');

    ctx.clearRect(0,0,W,H);
    if(!anyMenu){ requestAnimationFrame(_drawMenuBG); return; }

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
      ctx.beginPath();
      ctx.moveTo(p.x,p.y);
      ctx.lineTo(p.x-p.vx*8,p.y-p.vy*8);
      ctx.strokeStyle=`rgba(${p.col},${a*0.3})`;
      ctx.lineWidth=p.size*0.8;
      ctx.stroke();
    });

    const t=ts*0.0003;
    ctx.save();
    ctx.globalAlpha=0.04;
    for(let i=-10;i<20;i++){
      const x=((i*60+t*30)%W*1.5)-W*0.25;
      ctx.beginPath();ctx.moveTo(x,0);ctx.lineTo(x+H,H);
      ctx.strokeStyle='#0ff';ctx.lineWidth=1;ctx.stroke();
    }
    ctx.restore();

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
//  11. LEVEL BADGE UPDATER
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

<!-- ═══════════════════════════════════════════════
     MOBILE CONTROLS SCRIPT
     ═══════════════════════════════════════════════ -->
<script>
(function() {
  'use strict';

  // ── Touch/click handlers for move + jump ──
  const BTN_MAP = {
    btnLeft:  ['ArrowLeft'],
    btnRight: ['ArrowRight'],
    btnJump:  ['Space', 'ArrowUp'],
  };

  const _activeTouches = {};

  function pressBtn(id) {
    const el = document.getElementById(id);
    if (!el) return;
    el.classList.add('pressed');
    const codes = BTN_MAP[id];
    if (!codes) return;
    codes.forEach(code => {
      if (!K[code]) JP[code] = true;
      K[code] = true;
    });
  }

  function releaseBtn(id) {
    const el = document.getElementById(id);
    if (!el) return;
    el.classList.remove('pressed');
    const codes = BTN_MAP[id];
    if (!codes) return;
    codes.forEach(code => { K[code] = false; });
  }

  ['btnLeft', 'btnRight', 'btnJump'].forEach(id => {
    const el = document.getElementById(id);
    if (!el) return;

    el.addEventListener('touchstart', e => {
      e.preventDefault();
      Array.from(e.changedTouches).forEach(t => {
        if (!_activeTouches[id]) _activeTouches[id] = new Set();
        _activeTouches[id].add(t.identifier);
      });
      pressBtn(id);
    }, { passive: false });

    el.addEventListener('touchend', e => {
      e.preventDefault();
      Array.from(e.changedTouches).forEach(t => {
        if (_activeTouches[id]) _activeTouches[id].delete(t.identifier);
      });
      if (!_activeTouches[id] || _activeTouches[id].size === 0) releaseBtn(id);
    }, { passive: false });

    el.addEventListener('touchcancel', e => {
      e.preventDefault();
      if (_activeTouches[id]) _activeTouches[id].clear();
      releaseBtn(id);
    }, { passive: false });

    // Mouse fallback for desktop testing
    el.addEventListener('mousedown', e => { e.preventDefault(); pressBtn(id); });
    window.addEventListener('mouseup', () => releaseBtn(id));
  });

  // ── FLY BUTTON — hold to fly up, tap to toggle fly mode ──
  const flyBtn = document.getElementById('btnFly');
  if (flyBtn) {
    let _flyHeld = false;

    const _flyStart = e => {
      e.preventDefault();
      _flyHeld = true;
      flyBtn.classList.add('pressed');
      // If fly mode active → push up
      if (typeof _flying !== 'undefined' && _flying) {
        K['ArrowUp'] = true; JP['ArrowUp'] = true;
      } else {
        // Toggle fly mode on tap
        window.dispatchEvent(new KeyboardEvent('keydown', { code:'KeyF', shiftKey:true, bubbles:true }));
        setTimeout(() => {
          if (typeof _flying !== 'undefined' && _flying) {
            K['ArrowUp'] = true; JP['ArrowUp'] = true;
            flyBtn.classList.add('fly-active');
          } else {
            flyBtn.classList.remove('fly-active');
          }
        }, 40);
      }
    };

    const _flyEnd = e => {
      if(e) e.preventDefault();
      _flyHeld = false;
      flyBtn.classList.remove('pressed');
      K['ArrowUp'] = false;
    };

    flyBtn.addEventListener('touchstart', _flyStart, { passive: false });
    flyBtn.addEventListener('touchend',   _flyEnd,   { passive: false });
    flyBtn.addEventListener('touchcancel',_flyEnd,   { passive: false });
    flyBtn.addEventListener('mousedown',  _flyStart);
    window.addEventListener('mouseup',    _flyEnd);
  }

  // ── IMMORTAL BUTTON — tap to toggle ──
  const immBtn = document.getElementById('btnImmort');
  if (immBtn) {
    const _immToggle = e => {
      e.preventDefault();
      if (typeof _priv === 'undefined' || _priv !== 'owner') return;
      immBtn.classList.add('pressed');
      setTimeout(() => immBtn.classList.remove('pressed'), 160);
      window.dispatchEvent(new KeyboardEvent('keydown', { code:'KeyI', shiftKey:true, bubbles:true }));
      setTimeout(() => {
        if (typeof _immortal !== 'undefined' && _immortal) {
          immBtn.classList.add('immort-active');
        } else {
          immBtn.classList.remove('immort-active');
        }
      }, 50);
    };
    immBtn.addEventListener('touchstart', _immToggle, { passive: false });
    immBtn.addEventListener('mousedown',  _immToggle);
  }

  // ── Sync owner-only button visibility & states ──
  setInterval(() => {
    const isOwner = (typeof _priv !== 'undefined' && _priv === 'owner');
    const flyEl  = document.getElementById('btnFly');
    const immEl  = document.getElementById('btnImmort');

    if (flyEl) {
      flyEl.classList.toggle('owner-visible', isOwner);
      flyEl.classList.toggle('fly-active', isOwner && typeof _flying !== 'undefined' && _flying);
    }
    if (immEl) {
      immEl.classList.toggle('owner-visible', isOwner);
      immEl.classList.toggle('immort-active', isOwner && typeof _immortal !== 'undefined' && _immortal);
    }
  }, 350);

  // ── Dim controls when not playing ──
  setInterval(() => {
    const ctrl = document.getElementById('mobileControls');
    if (!ctrl) return;
    const inPlay = (typeof STATE !== 'undefined' && STATE === 'playing');
    ctrl.style.opacity     = inPlay ? '1' : '0.1';
    ctrl.style.pointerEvents = inPlay ? '' : 'none';
  }, 200);

  // ── Unlock audio on first touch ──
  document.addEventListener('touchstart', () => {
    try { AC(); } catch(e) {}
  }, { once: true });

  // ── Prevent scroll/zoom during play only ──
  document.addEventListener('touchmove', e => {
    // Allow scroll on the control bar so user can reposition the page
    if (e.target.closest('#mobileControls')) return;
    if (typeof STATE !== 'undefined' && STATE === 'playing') e.preventDefault();
  }, { passive: false });

})();
</script>
</body>
</html>
