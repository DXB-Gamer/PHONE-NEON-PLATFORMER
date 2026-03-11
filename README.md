<html>
<head>

<!-- Favicon -->
<link rel="apple-touch-icon" sizes="180x180" href="apple-touch-icon.png">
<link rel="icon" type="image/png" sizes="32x32" href="favicon-32x32.png">
<link rel="icon" type="image/png" sizes="16x16" href="favicon-16x16.png">
<link rel="icon" href="favicon.ico">
<link rel="manifest" href="site.webmanifest">

</head>
<body>

<!-- your game code -->
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
<meta name="apple-mobile-web-app-capable" content="yes">
<meta name="apple-mobile-web-app-status-bar-style" content="black-translucent">
<title>CUBIX — Neon Platformer</title>
<link href="https://fonts.googleapis.com/css2?family=Orbitron:wght@400;700;900&family=Share+Tech+Mono&display=swap" rel="stylesheet">
<style>
*,*::before,*::after{box-sizing:border-box;margin:0;padding:0}

html, body {
  width: 100%; height: 100%;
  background: #000;
  font-family: 'Orbitron', monospace;
  color: #0ff;
  overflow: hidden;
  overscroll-behavior: none;
  -webkit-overflow-scrolling: touch;
  /* Prevent iOS text selection on buttons */
  -webkit-touch-callout: none;
}

body {
  display: flex;
  align-items: center;
  justify-content: center;
}

#gameWrapper {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  width: 100%;
  height: 100%;
}

/* ── Desktop: fixed 900×540, centered ── */
#gameContainer {
  position: relative;
  width: 900px;
  height: 540px;
  overflow: hidden;
  flex-shrink: 0;
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

/* ── Horizontal button row for menus ── */
.btn-row {
  display: flex;
  flex-direction: row;
  gap: 14px;
  justify-content: center;
  flex-wrap: wrap;
  margin-top: 6px;
}
.btn-row .btn { margin: 0; }

canvas { display: block; }
#gameCanvas{position:absolute;top:0;left:0;z-index:1;touch-action:none;-webkit-touch-callout:none;}
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
  margin-bottom:16px;position:relative;
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
.stats{display:flex;gap:32px;margin:10px 0;position:relative;}
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
.wd-premium-swatch.active.wd-gold-bg{
  animation: goldGlow 1.4s ease-in-out infinite;
  box-shadow: 0 0 18px rgba(255,200,0,0.8), 0 0 40px rgba(255,160,0,0.5);
}
@keyframes goldGlow {
  0%,100%{ box-shadow: 0 0 14px rgba(255,200,0,0.7), 0 0 32px rgba(255,150,0,0.4); }
  50%    { box-shadow: 0 0 28px rgba(255,220,0,1.0), 0 0 60px rgba(255,180,0,0.7); }
}

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
   MOBILE CONTROLS
   ══════════════════════════════════════════════════ */

/* Portrait block — shown via JS only after user picks PHONE */
#portraitBlock {
  display: none;
  position: fixed; inset: 0; z-index: 9999;
  background: #000;
  flex-direction: column;
  align-items: center; justify-content: center;
  gap: 20px;
}
#portraitBlock .rot-icon {
  font-size: 72px;
  animation: rotHint 1.4s ease-in-out infinite;
  display: block;
}
@keyframes rotHint {
  0%,100% { transform: rotate(0deg); }
  50%      { transform: rotate(-90deg); }
}
#portraitBlock .rot-arrow {
  font-size: 48px;
  animation: rotHint 1.4s ease-in-out infinite;
  opacity: 0.6;
}
#portraitBlock p {
  font-family: 'Orbitron', monospace;
  color: rgba(0,255,255,0.75);
  font-size: 11px;
  letter-spacing: 5px;
  text-align: center;
  line-height: 2.4;
  text-shadow: 0 0 12px rgba(0,255,255,0.4);
}

/* ── Control bar: fixed to bottom, iPhone + iPad + Android ── */
#mobileControls {
  display: none;
  position: fixed;
  bottom: 0; left: 0; right: 0;
  height: var(--ctrl-bar-h, 115px);
  padding-bottom: env(safe-area-inset-bottom, 0px);
  z-index: 200;
  pointer-events: none;
  user-select: none;
  -webkit-user-select: none;
  background: linear-gradient(to top, rgba(0,0,0,0.75) 0%, transparent 100%);
}

/* Any touch device: phone, iPad, Android tablet */
@media (pointer: coarse) {
  #mobileControls { display: flex !important; }
}
@media (hover: none) {
  #mobileControls { display: flex !important; }
}

.ctrl-zone {
  position: absolute;
  bottom: 32px;
  pointer-events: all;
  display: flex;
  align-items: center;
}

#ctrlLeft  { left: 12px;  flex-direction: row;    gap: 14px; padding: 6px 10px; }
#ctrlRight { right: 12px; flex-direction: column; gap: 8px;  bottom: 28px; padding: 6px 10px; }

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

:root {
  --btn-lr:   64px;
  --btn-jump: 72px;
  --btn-fly:  50px;
  --btn-imm:  58px;
  --btn-dj:   58px;
  --btn-blast: 54px;
  --ctrl-bar-h: 115px;
}

/* ── Move buttons — neon cyan ── */
#btnLeft, #btnRight {
  width: var(--btn-lr); height: var(--btn-lr);
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
  width: var(--btn-jump); height: var(--btn-jump);
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
  width: var(--btn-fly); height: var(--btn-fly);
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
  width: var(--btn-imm); height: var(--btn-imm);
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

/* ── Down jump & blast buttons ── */
#btnDownJump {
  width: var(--btn-dj); height: var(--btn-dj);
  border-color: rgba(0,255,255,0.45);
  background: rgba(0,10,12,0.6);
  box-shadow: 0 0 10px rgba(0,255,255,0.15), inset 0 0 10px rgba(0,0,0,0.6);
}
#btnDownJump.pressed {
  transform: scale(0.87);
  border-color: rgba(0,255,255,0.95);
  box-shadow: 0 0 22px rgba(0,255,255,0.65), inset 0 0 12px rgba(0,255,255,0.1);
}
#btnBlast {
  width: var(--btn-blast); height: var(--btn-blast);
  border-color: rgba(255,140,0,0.5);
  background: rgba(12,5,0,0.6);
  box-shadow: 0 0 10px rgba(255,140,0,0.18), inset 0 0 10px rgba(0,0,0,0.6);
}
#btnBlast.pressed {
  transform: scale(0.87);
  border-color: rgba(255,180,0,0.95);
  box-shadow: 0 0 24px rgba(255,160,0,0.7), inset 0 0 12px rgba(255,120,0,0.12);
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
  padding: 8px 14px !important;
  cursor: pointer !important;
  min-width: 44px !important;
  min-height: 44px !important;
  -webkit-tap-highlight-color: transparent !important;
  touch-action: manipulation !important;
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

/* ── MENU TRANSITION — zoom-out/in effect ── */
@keyframes menuZoomIn {
  from { transform: scale(1.18); opacity: 0; }
  to   { transform: scale(1);    opacity: 1; }
}
@keyframes menuZoomOut {
  from { transform: scale(1);    opacity: 1; }
  to   { transform: scale(0.84); opacity: 0; }
}
@keyframes btnPop {
  0%   { transform: scale(0.85); opacity: 0; }
  65%  { transform: scale(1.06); }
  100% { transform: scale(1);    opacity: 1; }
}
.menu-entering {
  animation: menuZoomIn 0.38s cubic-bezier(0.22,0.61,0.36,1) forwards !important;
}
.menu-leaving {
  animation: menuZoomOut 0.28s ease-in forwards !important;
  pointer-events: none !important;
}
.menu-entering .btn {
  animation: btnPop 0.35s cubic-bezier(0.22,0.61,0.36,1) both;
}
.menu-entering .btn:nth-child(1) { animation-delay: 0.10s; }
.menu-entering .btn:nth-child(2) { animation-delay: 0.17s; }
.menu-entering .btn:nth-child(3) { animation-delay: 0.24s; }
/* ── CONFIRM DIALOG ── */
#cubixConfirm {
  display: none;
  position: fixed; inset: 0; z-index: 20000;
  background: rgba(0,0,0,0.92);
  align-items: center; justify-content: center;
  flex-direction: column; gap: 18px;
  font-family: 'Orbitron', monospace;
}
#cubixConfirm.show { display: flex; }
#cubixConfirm .cf-title {
  color: #f66; font-size: 13px; letter-spacing: 5px;
  text-shadow: 0 0 16px #f44;
  text-align: center; line-height: 1.8;
}
#cubixConfirm .cf-msg {
  color: rgba(255,255,255,0.55); font-size: 9px;
  letter-spacing: 3px; text-align: center; line-height: 2;
}
#cubixConfirm .cf-btns { display: flex; gap: 16px; }
#cubixConfirm .cf-yes {
  padding: 12px 28px; border: 1px solid rgba(255,60,60,0.6);
  background: rgba(255,60,60,0.08); color: #f66;
  font-family: 'Orbitron', monospace; font-size: 10px;
  letter-spacing: 4px; cursor: pointer;
  -webkit-tap-highlight-color: transparent;
  transition: background 0.15s;
}
#cubixConfirm .cf-yes:hover, #cubixConfirm .cf-yes:active { background: rgba(255,60,60,0.22); }
#cubixConfirm .cf-no {
  padding: 12px 28px; border: 1px solid rgba(0,255,255,0.3);
  background: rgba(0,255,255,0.04); color: #0ff;
  font-family: 'Orbitron', monospace; font-size: 10px;
  letter-spacing: 4px; cursor: pointer;
  -webkit-tap-highlight-color: transparent;
  transition: background 0.15s;
}
#cubixConfirm .cf-no:hover, #cubixConfirm .cf-no:active { background: rgba(0,255,255,0.12); }
</style>
</head>
<body>

<!-- Confirm dialog (reboot / switch device) -->
<div id="cubixConfirm">
  <div class="cf-title" id="cfTitle">REBOOT ALL DATA?</div>
  <div class="cf-msg" id="cfMsg">YOUR PROGRESS WILL BE LOST</div>
  <div class="cf-btns">
    <button class="cf-yes" id="cfYes">YES</button>
    <button class="cf-no"  id="cfNo">NO</button>
  </div>
</div>

<!-- Portrait rotation prompt -->
<div id="portraitBlock">
  <div class="rot-icon">📱</div>
  <div class="rot-arrow">↻</div>
  <p>ROTATE TO<br>LANDSCAPE<br>TO PLAY CUBIX</p>
</div>

<!-- DEVICE PICKER -->
<div id="devicePicker" style="display:flex;position:fixed;inset:0;z-index:10000;background:#000;flex-direction:column;align-items:center;justify-content:center;gap:28px;font-family:'Orbitron',monospace;">
  <div style="color:#0ff;font-size:14px;letter-spacing:6px;text-shadow:0 0 18px #0ff;">SELECT YOUR DEVICE</div>
  <div style="color:rgba(0,255,255,0.45);font-size:9px;letter-spacing:4px;text-align:center;line-height:2;">OPTIMISES LAYOUT &amp; CONTROLS</div>
  <div style="display:flex;gap:16px;flex-wrap:wrap;justify-content:center;">
    <button id="bDevPhone" style="width:140px;padding:20px 0;border:1px solid rgba(0,255,255,0.4);background:rgba(0,255,255,0.04);color:#0ff;font-family:'Orbitron',monospace;font-size:10px;letter-spacing:4px;cursor:pointer;display:flex;flex-direction:column;align-items:center;gap:10px;-webkit-tap-highlight-color:transparent;">
      <span style="font-size:32px;">📱</span>PHONE
    </button>
    <button id="bDevIpad" style="width:140px;padding:20px 0;border:1px solid rgba(255,0,255,0.4);background:rgba(255,0,255,0.04);color:#f0f;font-family:'Orbitron',monospace;font-size:10px;letter-spacing:4px;cursor:pointer;display:flex;flex-direction:column;align-items:center;gap:10px;-webkit-tap-highlight-color:transparent;">
      <span style="font-size:32px;">📲</span>TABLET
    </button>
    <button id="bDevLaptop" style="width:140px;padding:20px 0;border:1px solid rgba(0,255,100,0.4);background:rgba(0,255,100,0.04);color:#0f6;font-family:'Orbitron',monospace;font-size:10px;letter-spacing:4px;cursor:pointer;display:flex;flex-direction:column;align-items:center;gap:10px;-webkit-tap-highlight-color:transparent;">
      <span style="font-size:32px;">💻</span>LAPTOP
    </button>
  </div>
</div>

<div id="gameWrapper">
<div id="gameContainer">
  <canvas id="gameCanvas" width="900" height="540"></canvas>
  <canvas id="scanlineCanvas" width="900" height="540"></canvas>
  <canvas id="menuBG" width="900" height="540"></canvas>
  <div id="screenFlash"></div>
  <div id="cpIndicator">✦ CHECKPOINT SAVED ✦</div>
  <div id="_lvlBadge"></div>
  <!-- Pause button — sits top-right of canvas, visible during play -->
  <button id="_pauseBtn" style="position:absolute;top:8px;left:8px;z-index:20;width:38px;height:38px;border-radius:8px;border:2px solid rgba(0,255,255,0.5);background:rgba(0,0,0,0.65);color:rgba(0,255,255,1);font-size:16px;cursor:pointer;display:none;align-items:center;justify-content:center;-webkit-tap-highlight-color:transparent;touch-action:none;box-shadow:0 0 10px rgba(0,255,255,0.3);">⏸</button>
  <!-- Mobile level switcher — visible to admin/owner only -->
  <button id="_lvlSwitchBtn" style="display:none;position:absolute;top:6px;left:8px;z-index:20;font-family:'Share Tech Mono',monospace;font-size:9px;letter-spacing:2px;color:rgba(0,255,100,0.7);background:rgba(0,0,0,0.6);border:1px solid rgba(0,255,100,0.25);padding:4px 8px;cursor:pointer;">LVL ▲</button>

  <!-- MAIN MENU -->
  <div class="menu" id="mainMenu">
    <div class="menu-version">ALPHA 1.5 — NEON DIMENSIONS</div>
    <div class="menu-title" data-text="CUBIX">CUBIX</div>
    <div class="menu-sub">BREACH THE GRID</div>
    <div class="menu-line"></div>
    <div id="hiD" style="margin-bottom:8px">HIGH SCORE: <span id="hiV">0</span></div>
    <div class="btn-row">
      <button class="btn" id="bPlay">▶ &nbsp;PLAY</button>
      <button class="btn" id="bResume2" style="display:none">⟳ &nbsp;RESUME</button>
      <button class="btn p" id="bWardrobe">◈ &nbsp;WARDROBE</button>
    </div>
    <div class="btn-row" style="margin-top:4px">
      <button class="btn p" id="bHelp">? &nbsp;CONTROLS</button>
      <button class="btn p" id="bSwitchDevice">⇄ &nbsp;SWITCH DEVICE</button>
    </div>
    <div class="btn-row" style="margin-top:4px">
      <button class="btn p" id="bReboot" style="border-color:rgba(255,60,60,0.5);color:#f66;text-shadow:0 0 8px rgba(255,80,80,0.8);">⚠ &nbsp;REBOOT</button>
    </div>
    <div id="ctrlBox" style="display:none;margin-top:8px;font-size:10px;letter-spacing:2px;line-height:1.8;text-align:center;color:rgba(0,255,255,0.6)">
      A / ◀ &nbsp;&nbsp; ▶ / D &nbsp;&nbsp; MOVE<br>
      SPACE / ↑ &nbsp;&nbsp; JUMP &amp; DOUBLE JUMP<br>
      HOLD JUMP for higher arc &nbsp;|&nbsp; ESC pause
    </div>
  </div>

  <!-- PAUSE -->
  <div class="menu hidden" id="pauseMenu">
    <div class="menu-title" style="font-size:52px" data-text="PAUSED">PAUSED</div>
    <div class="menu-sub" style="margin-bottom:16px">SYSTEM HALTED</div>
    <div class="menu-line"></div>
    <div class="btn-row" style="margin-top:12px">
      <button class="btn" id="bResume">▶ &nbsp;RESUME</button>
      <button class="btn p" id="bQuit">⏹ &nbsp;MAIN MENU</button>
    </div>
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
    <div class="btn-row">
      <button class="btn" id="bNext">NEXT LEVEL ▶</button>
      <button class="btn p" id="bLCQ">MAIN MENU</button>
    </div>
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
    <div class="btn-row">
      <button class="btn" id="bWP">▶ &nbsp;PLAY AGAIN</button>
      <button class="btn p" id="bWM">MAIN MENU</button>
    </div>
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
    <div class="btn-row">
      <button class="btn" id="bRe">↺ &nbsp;RETRY</button>
      <button class="btn p" id="bGOQ">MAIN MENU</button>
    </div>
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

    <!-- Bottom row: immortal shield (left) + down-jump + jump (right) -->
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
      <!-- BLAST DOWN JUMP — all players -->
      <button class="ctrl-btn" id="btnBlast" aria-label="Blast Jump">
        <svg width="32" height="32" viewBox="0 0 32 32" fill="none">
          <defs>
            <filter id="fBlast" x="-60%" y="-60%" width="220%" height="220%">
              <feGaussianBlur stdDeviation="2" result="b"/>
              <feMerge><feMergeNode in="b"/><feMergeNode in="SourceGraphic"/></feMerge>
            </filter>
          </defs>
          <!-- Down arrow = blast down -->
          <polygon points="16,26 6,12 26,12" fill="rgba(255,140,0,0.9)" filter="url(#fBlast)"/>
          <!-- Flame lines -->
          <line x1="16" y1="6" x2="16" y2="11" stroke="rgba(255,200,60,0.8)" stroke-width="2.5" stroke-linecap="round"/>
          <line x1="11" y1="8" x2="13" y2="11" stroke="rgba(255,160,40,0.6)" stroke-width="2" stroke-linecap="round"/>
          <line x1="21" y1="8" x2="19" y2="11" stroke="rgba(255,160,40,0.6)" stroke-width="2" stroke-linecap="round"/>
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
CX.imageSmoothingEnabled = false;

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
function startAmbient(){ ambOn=true; /* ambient removed — was causing audio glitch */ }

// ── Input ────────────────────────────────────
const K={}, JP={};
document.addEventListener('keydown',e=>{
  if(!K[e.code]) JP[e.code]=true;
  K[e.code]=true;
  if(['Space','ArrowUp','ArrowDown','ArrowLeft','ArrowRight','ShiftRight'].includes(e.code)) e.preventDefault();
});
document.addEventListener('keyup',e=>{ K[e.code]=false; });
function clrJP(){ for(const k in JP) delete JP[k]; }

// ── Level data ───────────────────────────────
const p=(x,y,w,o={})=>({x,y,w,h:18,...o});
const ob=(x,y)=>({x,y,r:10});
const sp=(x,y,w)=>({x,y,w,h:16});

const LEVELS=[
  {name:'BOOT SEQUENCE',bgHue:180,
   platforms:[p(0,443,420),p(460,392,120),p(632,342,110),p(802,292,120),p(992,392,100),p(1112,338,200),p(1362,310,160)],
   orbs:[ob(200,430),ob(510,380),ob(850,280),ob(1050,380),ob(1200,325),ob(1440,297)],
   spikes:[],
   start:{x:50,y:418},goal:{x:1490,y:277}},
  {name:'GRID NETWORK',bgHue:200,
   platforms:[p(0,443,300),p(362,392,100),p(512,342,100),p(662,392,80),p(792,313,100),p(942,360,100),p(1092,286,120),p(1262,331,100),p(1412,270,150),p(1612,313,120)],
   orbs:[ob(150,430),ob(410,380),ob(840,301),ob(1000,347),ob(1150,274),ob(1460,257),ob(1660,301)],
   spikes:[sp(702,378,56)],
   start:{x:50,y:418},goal:{x:1680,y:281}},
  {name:'PHASE SHIFT',bgHue:260,
   platforms:[p(0,443,300),p(382,382,100,{type:'moving',moveX:80,speed:1.2}),p(572,333,90),p(712,382,90,{type:'moving',moveX:60,speed:1.5}),p(912,313,120),p(1112,360,80,{type:'moving',moveX:100,speed:1.0}),p(1312,286,100),p(1492,340,80,{type:'moving',moveX:70,speed:1.8}),p(1662,268,150)],
   orbs:[ob(150,430),ob(432,369),ob(617,320),ob(762,369),ob(970,301),ob(1360,274),ob(1710,256)],
   spikes:[sp(616,319,48),sp(962,299,38)],
   start:{x:50,y:418},goal:{x:1740,y:236}},
  {name:'DECAY PROTOCOL',bgHue:300,
   platforms:[p(0,443,300),p(372,392,100,{type:'crumble'}),p(532,351,90,{type:'crumble'}),p(692,392,90),p(832,340,80,{type:'crumble'}),p(972,295,120),p(1132,340,90,{type:'crumble'}),p(1292,295,100),p(1472,340,80,{type:'crumble'}),p(1612,268,180)],
   orbs:[ob(150,430),ob(580,338),ob(730,380),ob(1020,283),ob(1330,283),ob(1680,256)],
   spikes:[sp(740,378,48),sp(1016,281,38)],
   start:{x:50,y:418},goal:{x:1700,y:236}},
  {name:'SPIKE MATRIX',bgHue:0,
   platforms:[p(0,443,300),p(372,378,80),p(512,331,100),p(672,378,80),p(812,322,100),p(972,378,80),p(1112,313,120),p(1292,358,80,{type:'moving',moveX:60,speed:1.6}),p(1462,295,100),p(1652,340,80,{type:'crumble'}),p(1792,277,160)],
   orbs:[ob(560,319),ob(860,310),ob(1160,301),ob(1500,283),ob(1840,265)],
   spikes:[sp(386,364,38),sp(530,317,38),sp(830,308,38),sp(1130,299,38),sp(1670,326,38)],
   start:{x:50,y:418},goal:{x:1880,y:245}},
  {name:'MULTI-PATH',bgHue:140,
   platforms:[p(0,443,280),p(352,349,80,{type:'moving',moveX:80,speed:1.4}),p(532,396,80,{type:'crumble'}),p(672,340,90),p(822,396,80,{type:'crumble'}),p(952,322,120,{type:'moving',moveX:100,speed:1.2}),p(1132,387,80,{type:'crumble'}),p(1272,322,90),p(1432,277,90,{type:'moving',moveX:80,speed:2.0}),p(1612,322,80,{type:'crumble'}),p(1752,250,180)],
   orbs:[ob(140,430),ob(395,337),ob(710,328),ob(1005,310),ob(1460,265),ob(1840,238)],
   spikes:[sp(570,382,38),sp(860,382,38),sp(1170,373,38),sp(1650,308,38)],
   start:{x:50,y:418},goal:{x:1840,y:218}},
  {name:'VELOCITY GRID',bgHue:220,
   platforms:[p(0,421,200),p(292,367,80,{type:'moving',moveX:120,speed:2.5}),p(492,322,70,{type:'moving',moveX:90,speed:2.8}),p(692,367,80),p(832,313,80,{type:'moving',moveX:110,speed:3.0}),p(1032,358,80,{type:'crumble'}),p(1172,295,90,{type:'moving',moveX:100,speed:2.2}),p(1392,340,80,{type:'crumble'}),p(1532,268,100,{type:'moving',moveX:80,speed:2.6}),p(1732,313,80,{type:'crumble'}),p(1872,232,180)],
   orbs:[ob(100,409),ob(332,355),ob(527,310),ob(730,355),ob(1210,283),ob(1575,256),ob(1940,220)],
   spikes:[sp(710,353,48),sp(1070,344,38),sp(1430,326,38),sp(1770,299,38)],
   start:{x:50,y:396},goal:{x:1960,y:200}},
  {name:'CRUMBLE STORM',bgHue:30,
   platforms:[p(0,421,180),p(262,367,70,{type:'crumble'}),p(402,322,70,{type:'crumble'}),p(542,277,70,{type:'crumble'}),p(682,322,80),p(822,277,70,{type:'crumble'}),p(962,322,80,{type:'moving',moveX:100,speed:2.0}),p(1142,268,70,{type:'crumble'}),p(1282,313,80,{type:'moving',moveX:90,speed:2.5}),p(1462,250,70,{type:'crumble'}),p(1602,295,80,{type:'moving',moveX:80,speed:3.0}),p(1782,232,200)],
   orbs:[ob(90,409),ob(295,355),ob(436,310),ob(575,265),ob(715,310),ob(1180,256),ob(1830,220)],
   spikes:[sp(692,308,38),sp(1012,308,38),sp(1652,281,38)],
   start:{x:50,y:396},goal:{x:1880,y:200}},
  {name:'SYSTEM BREACH',bgHue:280,
   platforms:[p(0,421,160),p(232,358,70,{type:'moving',moveX:80,speed:2.5}),p(412,313,60,{type:'crumble'}),p(552,358,70,{type:'moving',moveX:70,speed:3.2}),p(732,295,70,{type:'crumble'}),p(882,349,70,{type:'moving',moveX:100,speed:2.8}),p(1072,277,70,{type:'crumble'}),p(1212,331,70,{type:'moving',moveX:80,speed:3.5}),p(1402,259,70,{type:'crumble'}),p(1542,313,70,{type:'moving',moveX:90,speed:2.4}),p(1732,241,80,{type:'crumble'}),p(1872,196,180)],
   orbs:[ob(80,409),ob(265,346),ob(587,346),ob(766,283),ob(917,337),ob(1245,319),ob(1577,301),ob(1940,184)],
   spikes:[sp(442,299,38),sp(772,281,38),sp(1112,263,38),sp(1442,245,38),sp(1770,227,38)],
   start:{x:50,y:396},goal:{x:1960,y:164}},
  {name:'CORE OVERLOAD',bgHue:350,
   platforms:[p(0,421,140),p(222,349,60,{type:'moving',moveX:100,speed:3.5}),p(412,295,60,{type:'crumble'}),p(552,349,60,{type:'moving',moveX:80,speed:4.0}),p(712,277,60,{type:'crumble'}),p(862,340,60,{type:'moving',moveX:110,speed:3.0}),p(1052,259,60,{type:'crumble'}),p(1192,322,60,{type:'moving',moveX:90,speed:4.2}),p(1382,241,60,{type:'crumble'}),p(1522,304,60,{type:'moving',moveX:80,speed:3.8}),p(1712,214,70,{type:'crumble'}),p(1852,268,60,{type:'moving',moveX:60,speed:4.5}),p(1992,160,250)],
   orbs:[ob(70,409),ob(252,337),ob(582,337),ob(742,265),ob(892,328),ob(1222,310),ob(1552,292),ob(1882,256),ob(2110,148)],
   spikes:[sp(442,281,38),sp(742,263,38),sp(1092,245,38),sp(1422,227,38),sp(1750,200,38)],
   start:{x:50,y:396},goal:{x:2080,y:128}},
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
const GR=1380,JF=-573,DJF=-573,TJF=-520,SPD=225,ACC=2400,DEC=3000,MXF=820;
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
    if(pl.type==='moving'){
      pl.prevX = pl.x;
      pl.x=pl.origX+Math.sin(pl.t*(pl.speed||1))*(pl.moveX||80);
    }
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

  // N: Up/Space/W = jump.  A&O: Up/Space/W = blast jump UP (from ground or air), E = normal double jump
  const jmp = _isAdmin()
    ? JP['KeyE']
    : (JP['ArrowUp']||JP['Space']||JP['KeyW']);

  // ROCKET UP — A&O: HOLD Up/Space/W/Shift = instant smooth rocket, works from ground too
  const rocketUp = _isAdmin() && (K['ArrowUp']||K['Space']||K['KeyW']||K['ShiftLeft']||K['ShiftRight']);
  if(rocketUp){
    P.vy = Math.max(P.vy - 2400*dt, -780);
    P.onGnd = false;
    const _bu1 = (_usingGold)?'#ffd700':(_usingSilver)?'#c8d8e8':_CUBE_COLOR;
    const _bu2 = (_usingGold||_usingSilver)?'#ffffff':'#f0f';
    const _bu3 = (_usingGold)?'#ffe066':(_usingSilver)?'#e8f0f8':'#fff';
    burst(P.x+PW/2,P.y+PH,18,_bu1,6,0.4,6,-0.6);
    burst(P.x+PW/2,P.y+PH,14,_bu2,5,0.35,5,-0.5);
    burst(P.x+PW/2,P.y+PH,10,_bu3,4,0.3,4,-0.4);
    burst(P.x+PW/2,P.y+PH,8,'#fff',3.5,0.28,3,-0.35);
    burst(P.x+PW/2,P.y+PH,6,_bu1,2.5,0.22,2,-0.3);
  }

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


  if(rocketUp){ /* gravity overridden by rocket */ }
  else if(P.vy<0&&!(K['KeyE']||(_isAdmin()&&(K['Space']||K['ArrowUp']||K['KeyW'])))) P.vy+=GR*1.7*dt;
  else if(!_isAdmin()&&(K['ArrowUp']||K['Space']||K['KeyW'])&&P.vy>0) P.vy+=GR*0.999*dt;
  else P.vy+=GR*dt;
  P.vy=Math.min(P.vy,MXF);

  // ── Jump logic ──
  if(P.onGnd){
    P.jumps  = 2;  // everyone = double jump
    P.djDown = _isAdmin() ? 999 : 999;  // everyone gets infinite down-jumps (normal too per request)
    P.coyT   = COY;
  } else {
    if(P.coyT > 0) P.coyT -= dt;
  }

  // S / ArrowDown = down-jump (admin/owner only) — launches downward
  const dnJmp = (K['KeyS']||K['ArrowDown']);
  if(dnJmp && P.djDown > 0 && !P.onGnd){
    P.vy = -DJF * 1.2;
    P.djDown--;
    const _dc1 = (_usingGold) ? '#ffd700' : (_usingSilver) ? '#c8d8e8' : _CUBE_COLOR;
    const _dc2 = (_usingGold||_usingSilver) ? '#ffffff' : '#f0f';
    const _dc3 = (_usingGold) ? '#ffe066' : (_usingSilver) ? '#e8f0f8' : '#fff';
    burst(P.x+PW/2,P.y+PH/2,18,_dc1,6,0.5,6);
    burst(P.x+PW/2,P.y+PH/2,14,_dc2,5,0.45,5);
    burst(P.x+PW/2,P.y+PH/2,10,_dc3,4,0.4,4);
  }

  if(jmp) P.jBufT = JBF;
  else if(P.jBufT > 0) P.jBufT -= dt;

  if(P.jBufT > 0){
    if(P.onGnd || P.coyT > 0){
      // Ground / coyote jump — cube colour + purple for admin/owner
      P.vy = JF; P.coyT = 0; P.jBufT = 0; P.jumps = 1;
      S.jump();
      burst(P.x+PW/2,P.y+PH,14,_CUBE_COLOR,3.5,0.4,5);
      if(_isAdmin()) burst(P.x+PW/2,P.y+PH,8,'#f0f',3,0.35,4);
    } else if(P.jumps > 0){
      const used = 2-P.jumps;
      let c1, c2, c3;
      if(_isAdmin()){
        const seq = ['_CUBE_COLOR','#f0f','#fff'];
        c1 = seq[used%3]==='_CUBE_COLOR'?_CUBE_COLOR:seq[used%3];
        c2 = seq[(used+1)%3]==='_CUBE_COLOR'?_CUBE_COLOR:seq[(used+1)%3];
        c3 = seq[(used+2)%3]==='_CUBE_COLOR'?_CUBE_COLOR:seq[(used+2)%3];
      } else {
        c1 = used%2===0?_CUBE_COLOR:'#f0f';
        c2 = used%2===0?'#f0f':_CUBE_COLOR;
        c3 = '#fff';
      }
      // More particles per jump — jump 2 gets more, jump 3 (triple) gets most
      const n = _isAdmin() ? [22,28,36][used] || 36 : 22;
      P.vy = DJF; P.jumps--; P.jBufT = 0;
      S.dJump();
      burst(P.x+PW/2,P.y+PH/2,n,c1,4.5,0.5,6);
      burst(P.x+PW/2,P.y+PH/2,Math.round(n*0.6),c2,3.5,0.45,5);
      if(_isAdmin()) burst(P.x+PW/2,P.y+PH/2,Math.round(n*0.4),c3,3,0.4,4);
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
      // Carry player with moving platform
      if(pl.type==='moving' && pl.prevX !== undefined){
        P.x += pl.x - pl.prevX;
      }
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

  // Spikes archived (no collision) if immortal, active otherwise
  if(!_immortal){
    spkList.forEach(sk=>{
      if(hit(P.x+3,P.y+4,PW-6,PH-5,sk.x,sk.y,sk.w,sk.h)) killP();
    });
  }

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
      // Swirl zoom-out then show level complete screen
      setTimeout(showLC, 400);
    }
  }

  if(P.y>H+160){ if(_immortal){ P.y=H-60; P.vy=-400; P.x=Math.max(80,P.x); } else killP(); }

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
  // Immortal = fully protected from void and spikes
  if(_immortal){
    // Bounce back from void
    if(P.y > H+160){ P.y = H-60; P.vy = -400; }
    return;
  }
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
  // Lives — offset right to avoid pause button at top-left
  const totalW = lives * 26 - 8;
  const startX = W/2 - totalW/2 + 20; // shift right slightly
  for(let i=0;i<lives;i++){
    const lx=startX+i*26, ly=14;
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
  CX.fillText(`LVL ${lvlIdx+1}  ${LEVELS[lvlIdx].name}`,W/2,52);
  CX.fillStyle='#ff0'; CX.shadowColor='#ff0';
  CX.font="10px 'Share Tech Mono',monospace";
  CX.fillText(`◆ ${colOrbs}/${totOrbs}`,W/2,66);
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

let _menuTransTimer = null;
function showMenu(id, instant){
  // Don't show any game menu while device picker is still open
  const picker = document.getElementById('devicePicker');
  if (picker && picker.style.display !== 'none' && id) return;
  const all = ['mainMenu','pauseMenu','lcMenu','goMenu','winMenu'];
  if(instant){
    all.forEach(m=>{ const el=document.getElementById(m); if(el){ el.classList.add('hidden'); el.classList.remove('menu-entering','menu-leaving'); } });
    if(id){ const el=document.getElementById(id); if(el) el.classList.remove('hidden'); }
    return;
  }
  // Zoom-out any currently visible menu, then zoom-in new one
  const visible = all.map(m=>document.getElementById(m)).filter(el=>el&&!el.classList.contains('hidden'));
  if(visible.length && id){
    visible.forEach(el=>{ el.classList.add('menu-leaving'); });
    if(_menuTransTimer) clearTimeout(_menuTransTimer);
    _menuTransTimer = setTimeout(()=>{
      visible.forEach(el=>{ el.classList.add('hidden'); el.classList.remove('menu-leaving','menu-entering'); });
      const next = document.getElementById(id);
      if(next){ next.classList.remove('hidden','menu-leaving'); next.classList.add('menu-entering');
        setTimeout(()=>next.classList.remove('menu-entering'), 400); }
    }, 260);
  } else {
    all.forEach(m=>{ const el=document.getElementById(m); if(el){ el.classList.add('hidden'); el.classList.remove('menu-entering','menu-leaving'); } });
    if(id){ const el=document.getElementById(id); if(el){ el.classList.remove('hidden'); el.classList.add('menu-entering');
      setTimeout(()=>el.classList.remove('menu-entering'), 400); } }
  }
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
  // Force show lcMenu directly — bypass all guards
  ['mainMenu','pauseMenu','lcMenu','goMenu','winMenu'].forEach(m=>{
    const el=document.getElementById(m);
    if(el) el.classList.add('hidden');
  });
  const lc=document.getElementById('lcMenu');
  if(lc){ lc.classList.remove('hidden'); }
}
function showGO(){
  document.getElementById('goSc').textContent=score;
  document.getElementById('goBe').textContent=hiScore;
  showMenu('goMenu');
}
function startGame(from=0){
  lvlIdx=from; score=0; lives=3; combo=1; comboT=0; dying=false;
  _cpX=null; _cpY=null; _cpLvl=null;
  initLvl(lvlIdx); STATE='playing'; showMenu(null, true); startAmbient();
  _gameStarted=true; _saveResume();
}

document.getElementById('bPlay').onclick=()=>startGame(0);
document.getElementById('bHelp').onclick=()=>{
  const b=document.getElementById('ctrlBox');
  b.style.display=b.style.display==='none'?'block':'none';
};
document.getElementById('bResume').onclick=()=>{ STATE='playing'; showMenu(null, true); };
document.getElementById('bQuit').onclick=()=>{ STATE='menu'; _showMainMenu(); };
/* bNext, bRe, bLCQ, bGOQ, bWM, bQuit handlers defined below */

document.addEventListener('keydown',e=>{
  if(e.code==='Escape'){
    if(STATE==='playing'){ STATE='paused'; showMenu('pauseMenu'); }
    else if(STATE==='paused'){ STATE='playing'; showMenu(null); }
  }
});

document.getElementById('hiV').textContent=hiScore;

// Always hide menu on load — device picker (script below) will show menu after choice
showMenu(null, true);
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

// ── Early constants needed before any IIFE runs ──
let _priv_early = 'none';
function _isOwner_early(){ return _priv_early==='owner'; }
const _SILVER_EARNED = 'cubix_silver_earned';
const _GOLD_EARNED   = 'cubix_gold_earned';

const _SILVER_KEY = 'cubix_silver_unlocked';
const _GOLD_KEY   = 'cubix_gold_unlocked';
function _isSilverUnlocked(){ return localStorage.getItem(_SILVER_KEY)==='1' || (typeof _isOwner==='function'?_isOwner():_isOwner_early()); }
function _isGoldUnlocked()  { return localStorage.getItem(_GOLD_KEY)==='1'   || (typeof _isOwner==='function'?_isOwner():_isOwner_early()); }

// On load: strip owner-granted keys if not earned through gameplay
(function(){
  if(localStorage.getItem(_SILVER_KEY)==='1' && localStorage.getItem(_SILVER_EARNED)!=='1')
    localStorage.removeItem(_SILVER_KEY);
  if(localStorage.getItem(_GOLD_KEY)==='1' && localStorage.getItem(_GOLD_EARNED)!=='1')
    localStorage.removeItem(_GOLD_KEY);
})();

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
  0: [{x:200,platY:443},{x:1200,platY:338}],
  1: [{x:150,platY:443},{x:1140,platY:286}],
  2: [{x:150,platY:443},{x:1350,platY:286}],
  3: [{x:150,platY:443},{x:1680,platY:268}],
  4: [{x:1005,platY:378},{x:1840,platY:277}],
  5: [{x:710,platY:340},{x:1820,platY:250}],
  6: [{x:100,platY:421},{x:1950,platY:232}],
  7: [{x:90,platY:421},{x:1860,platY:232}],
  8: [{x:80,platY:421},{x:1940,platY:196}],
  9: [{x:70,platY:421},{x:2080,platY:160}],
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

let _gameStarted = false; try{ _gameStarted = localStorage.getItem('cubix_started')==='1'; }catch(e){}

function _saveResume(){
  localStorage.setItem('cubix_started','1');
  localStorage.setItem('cubix_resumeLvl',lvlIdx);
  if(_cpX!==null){ localStorage.setItem('cubix_resumeX',_cpX); localStorage.setItem('cubix_resumeY',_cpY); }
}

function _showMainMenu(){
  // Don't show main menu if device picker hasn't been dismissed yet
  const picker = document.getElementById('devicePicker');
  if (picker && picker.style.display !== 'none') return;
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


document.getElementById('bNext').onclick = null;
const _bNext = document.getElementById('bNext');
function _doNextLevel(){
  if(lvlIdx===6) _unlockSilver();
  if(lvlIdx===9) _unlockGold();
  if(lvlIdx < LEVELS.length-1){
    lvlIdx++; _cpX=null; _cpY=null; _cpLvl=null;
    initLvl(lvlIdx); _initCheckpoints(); STATE='playing';
    showMenu(null, true);
  } else {
    if(score>hiScore){ hiScore=score; localStorage.setItem('cubix_hi',hiScore); }
    document.getElementById('wSc').textContent = score;
    document.getElementById('wBe').textContent = hiScore;
    _unlockGold(); STATE='win'; showMenu('winMenu');
  }
}
_bNext.addEventListener('click', _doNextLevel);
_bNext.addEventListener('touchend', function(e){ e.preventDefault(); _doNextLevel(); }, {passive:false});

['bQuit','bLCQ','bGOQ','bWM'].forEach(id=>{ document.getElementById(id).onclick=()=>{ STATE='menu'; _showMainMenu(); }; });
document.getElementById('bWP').onclick=()=>startGame(0);
document.getElementById('bRe').onclick=()=>{
  // Retry from checkpoint — give 3 lives back and respawn
  dying=false; P.dead=false;
  lives = 3; combo=1; comboT=0;
  _respawnAtCheckpoint(); STATE='playing'; showMenu(null, true);
};
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
  localStorage.setItem(_SILVER_EARNED,'1'); // earned through gameplay
  _rebuildPremiumSlots();
  _unlockBanner('SILVER SKIN UNLOCKED','#c0c0c0');
}
function _unlockGold(){
  if(_isGoldUnlocked())return;
  localStorage.setItem(_GOLD_KEY,'1');
  localStorage.setItem(_GOLD_EARNED,'1'); // earned through gameplay
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

let _priv='none', _immortal=false;
_priv_early = 'none';
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
    <input id="_redeemInput" type="text" placeholder="ENTER ACCESS CODE" autocomplete="off" spellcheck="false" autocorrect="off" autocapitalize="characters" inputmode="text">
    <button id="_redeemSubmit" style="font-family:'Share Tech Mono',monospace;font-size:12px;letter-spacing:3px;color:rgba(0,255,255,0.8);background:rgba(0,255,255,0.07);border:1px solid rgba(0,255,255,0.25);padding:8px 28px;cursor:pointer;margin-top:2px;">SUBMIT</button>
    <div id="_redeemMsg"></div>
    <button id="_redeemClose">✕ &nbsp;CLOSE</button>
  `;
  document.body.appendChild(modal);

  function _tryCode(){
    const code=document.getElementById('_redeemInput').value.trim().toUpperCase();
    const msg=document.getElementById('_redeemMsg');
    if(code==='TDVADMIN'){ _priv='admin'; _applyPriv(); msg.style.color='rgba(0,255,100,0.7)'; msg.textContent='✓ ADMIN ACCESS GRANTED'; setTimeout(()=>modal.classList.add('hidden'),1200); }
    else if(code==='TDVOWNER_2014'){ _priv='owner'; _applyPriv(); msg.style.color='rgba(255,200,0,0.7)'; msg.textContent='✓ OWNER ACCESS GRANTED'; setTimeout(()=>modal.classList.add('hidden'),1200); }
    else if(code==='TDVNORMAL'){ _priv='none'; _immortal=false; _applyPriv(); msg.style.color='rgba(180,180,180,0.5)'; msg.textContent='✓ PRIVILEGES REMOVED'; setTimeout(()=>modal.classList.add('hidden'),1000); }
    else { msg.style.color='rgba(255,50,50,0.7)'; msg.textContent='✗ INVALID CODE'; }
  }

  btn.addEventListener('click',()=>{ modal.classList.remove('hidden'); document.getElementById('_redeemInput').value=''; document.getElementById('_redeemMsg').textContent=''; document.getElementById('_redeemInput').focus(); });
  btn.addEventListener('touchend',e=>{ e.preventDefault(); modal.classList.remove('hidden'); document.getElementById('_redeemInput').value=''; document.getElementById('_redeemMsg').textContent=''; setTimeout(()=>document.getElementById('_redeemInput').focus(),100); });
  document.getElementById('_redeemClose').addEventListener('click',()=>modal.classList.add('hidden'));
  document.getElementById('_redeemClose').addEventListener('touchend',e=>{ e.preventDefault(); modal.classList.add('hidden'); });
  document.getElementById('_redeemSubmit').addEventListener('click', _tryCode);
  document.getElementById('_redeemSubmit').addEventListener('touchend',e=>{ e.preventDefault(); _tryCode(); });
  document.getElementById('_redeemInput').addEventListener('keydown',e=>{ if(e.key==='Enter'){ e.preventDefault(); _tryCode(); } });
})();

function _applyPriv(){ _priv_early = _priv;
  const badge=document.getElementById('_privBadge');
  if(_priv==='owner'){
    badge.textContent='[ OWNER ]';badge.style.color='rgba(255,200,0,0.5)';badge.style.border='1px solid rgba(255,200,0,0.15)';badge.style.opacity='1';
    // Auto-unlock silver and gold for owner
    localStorage.setItem(_SILVER_KEY,'1');
    localStorage.setItem(_GOLD_KEY,'1');
    _rebuildPremiumSlots();
  } else if(_priv==='admin'){
    badge.textContent='[ ADMIN ]';badge.style.color='rgba(0,255,100,0.45)';badge.style.border='1px solid rgba(0,255,100,0.12)';badge.style.opacity='1';
  } else {
    badge.style.opacity='0'; _immortal=false;
    // Revert silver/gold to earned state only
    if(!_isSilverEarned()) localStorage.removeItem(_SILVER_KEY);
    if(!_isGoldEarned())   localStorage.removeItem(_GOLD_KEY);
    // If currently using a locked skin, reset to default
    if(_cubeSwatchIdx===10&&!_isSilverUnlocked()) _applyColor(9);
    if(_cubeSwatchIdx===11&&!_isGoldUnlocked())   _applyColor(9);
    _rebuildPremiumSlots();
  }
}
// Separate keys for earned vs owner-granted
// _SILVER_EARNED and _GOLD_EARNED moved earlier
function _isSilverEarned(){ return localStorage.getItem(_SILVER_EARNED)==='1'; }
function _isGoldEarned()  { return localStorage.getItem(_GOLD_EARNED)==='1'; }

function _flashPriv(txt,col){
  const el=document.getElementById('_privFlash');
  if(!el)return; el.style.color=col; el.textContent=txt; el.style.opacity='1';
  clearTimeout(el._t); el._t=setTimeout(()=>{el.style.opacity='0';},1800);
}

// Keybinds — SHIFT+L admin+ | Right Shift = fly (hold), I = immortal toggle (owner only)
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
      _flashPriv('→ LEVEL '+n,'rgba(0,255,100,0.7)');
    }
  }
  if(!_isOwner())return;
  if(e.code==='KeyI'){
    _immortal=!_immortal;
    _flashPriv(_immortal?'★ IMMORTAL ON':'★ IMMORTAL OFF','rgba(255,200,0,0.7)');
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
  // DO NOT call _showMainMenu() here — initDevicePicker() handles showing the menu
  // after the device is chosen
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
//  11. LEVEL BADGE UPDATER + PAUSE BUTTON
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
  // Show pause button only during gameplay
  const pb=document.getElementById('_pauseBtn');
  if(pb){ pb.style.display=(typeof STATE!=='undefined'&&STATE==='playing')?'flex':'none'; }
},200);

// Wire pause button — deferred so DOM is guaranteed ready
setTimeout(()=>{
  const pb=document.getElementById('_pauseBtn');
  if(!pb) return;
  function doPause(e){ e.preventDefault(); e.stopPropagation();
    if(typeof STATE==='undefined') return;
    if(STATE==='playing'){ STATE='paused'; showMenu('pauseMenu'); pb.textContent='⏸'; }
    else if(STATE==='paused'){ STATE='playing'; showMenu(null,true); pb.textContent='⏸'; }
  }
  pb.addEventListener('touchstart', doPause, {passive:false});
  pb.addEventListener('click', doPause);
},0);

// ══════════════════════════════════════════
// CONFIRM DIALOG ENGINE
// ══════════════════════════════════════════
function cubixConfirm(title, msg, onYes) {
  const dlg   = document.getElementById('cubixConfirm');
  const tEl   = document.getElementById('cfTitle');
  const mEl   = document.getElementById('cfMsg');
  const yBtn  = document.getElementById('cfYes');
  const nBtn  = document.getElementById('cfNo');
  tEl.textContent = title;
  mEl.textContent = msg;
  dlg.classList.add('show');
  // Remove old listeners by cloning
  const yNew = yBtn.cloneNode(true); yBtn.parentNode.replaceChild(yNew, yBtn);
  const nNew = nBtn.cloneNode(true); nBtn.parentNode.replaceChild(nNew, nBtn);
  function close() { document.getElementById('cubixConfirm').classList.remove('show'); }
  document.getElementById('cfYes').addEventListener('click', () => { close(); onYes(); });
  document.getElementById('cfNo').addEventListener('click',  close);
}

// ALL localStorage keys used by the game
const _ALL_KEYS = ['cubix_hi','cubix_swatch','cubix_silver_unlocked','cubix_gold_unlocked',
  'cubix_started','cubix_resumeLvl','cubix_resumeX','cubix_resumeY','cubix_v2','cubix_device'];

// ── REBOOT button ──
document.getElementById('bReboot').addEventListener('click', () => {
  cubixConfirm('⚠ REBOOT ALL DATA?', 'YOUR PROGRESS WILL BE LOST', () => {
    cubixConfirm('ARE YOU SURE?', 'THIS CANNOT BE UNDONE', () => {
      _ALL_KEYS.forEach(k => localStorage.removeItem(k));
      // Clear device choice and show picker again on reload
      location.reload();
    });
  });
});

// ── SWITCH DEVICE button ──
document.getElementById('bSwitchDevice').addEventListener('click', () => {
  cubixConfirm('SWITCH DEVICE?', 'YOUR PROGRESS IS SAVED — ONLY LAYOUT CHANGES', () => {
    localStorage.removeItem('cubix_device');
    window._deviceType = null;
    // Re-wire picker buttons to use window.applyDeviceLayout
    const picker = document.getElementById('devicePicker');
    if (picker) picker.style.display = 'flex';
  });
});

</script>

<!-- ═══════════════════════════════════════════════
     MOBILE CONTROLS SCRIPT
     ═══════════════════════════════════════════════ -->
<script>
(function() {
  'use strict';

  // ═══════════════════════════════════
  // DEVICE SIZES
  // ═══════════════════════════════════
  const DEVICE_SIZES = {
    phone:  { left:75, jump:84, fly:59, immort:68, dj:72, blast:66, ctrlH:90, ctrlBottom:10, gap:10 },
    tablet: { left:110, jump:128, fly:90, immort:103, dj:110, blast:100, ctrlH:150, ctrlBottom:28, gap:18 },
  };

  window._deviceType  = localStorage.getItem('cubix_device') || null;
  window._deviceCtrlH = 110;

  // ═══════════════════════════════════
  // SCALE GAME
  // ═══════════════════════════════════
  function scaleGame() {
    const gc = document.getElementById('gameContainer');
    const gw = document.getElementById('gameWrapper');
    const dtype = window._deviceType;

    if (!dtype || dtype === 'laptop') {
      gc.style.cssText = '';
      gw.style.cssText = '';
      return;
    }

    const GW = 900, GH = 540;
    const vp  = window.visualViewport;
    const vw  = vp ? vp.width  : window.innerWidth;
    const vh  = vp ? vp.height : window.innerHeight;

    let scale, left, top;

    if (dtype === 'phone') {
      // Fill the FULL screen — controls float ON TOP (like every real mobile game)
      scale = Math.min(vw / GW, vh / GH);
      left  = Math.round((vw - GW * scale) / 2);
      top   = Math.round((vh - GH * scale) / 2);
    } else {
      const barH = window._deviceCtrlH || 150;
      const availH = Math.max(80, vh - barH);
      scale = Math.min((vw - 20) / GW, (availH - 10) / GH);
      left  = Math.round((vw - GW * scale) / 2);
      top   = Math.round((availH - GH * scale) / 2);
    }

    gw.style.cssText = `position:fixed;top:0;left:0;width:${vw}px;height:${vh}px;overflow:hidden;display:block;background:#000;`;
    gc.style.cssText = `position:absolute;width:900px;height:540px;top:0;left:0;transform-origin:0 0;transform:translate(${left}px,${top}px) scale(${scale});overflow:hidden;`;

    gw.style.cssText = `position:fixed;top:0;left:0;width:${vw}px;height:${vh}px;overflow:hidden;display:block;background:#000;`;
    gc.style.cssText = `position:absolute;width:900px;height:540px;top:0;left:0;transform-origin:0 0;transform:translate(${left}px,${top}px) scale(${scale});overflow:hidden;`;

    gw.style.cssText = `position:fixed;top:0;left:0;width:${vw}px;height:${vh}px;overflow:hidden;display:block;background:#000;`;
    gc.style.cssText = `position:absolute;width:900px;height:540px;top:0;left:0;transform-origin:0 0;transform:translate(${left}px,${top}px) scale(${scale});overflow:hidden;`;
  }
  window.scaleGame = scaleGame;

  // ═══════════════════════════════════
  // APPLY DEVICE LAYOUT
  // ═══════════════════════════════════
  function applyDeviceLayout(type) {
    window._deviceType  = type;
    window._deviceCtrlH = (DEVICE_SIZES[type] || {}).ctrlH || 110;

    if (type === 'laptop') {
      document.getElementById('mobileControls').style.display = 'none';
      scaleGame();
      return;
    }

    const s = DEVICE_SIZES[type];
    if (!s) return;

    const root = document.documentElement;
    root.style.setProperty('--btn-lr',     s.left   + 'px');
    root.style.setProperty('--btn-jump',   s.jump   + 'px');
    root.style.setProperty('--btn-fly',    s.fly    + 'px');
    root.style.setProperty('--btn-imm',    s.immort + 'px');
    root.style.setProperty('--btn-dj',     (s.dj    || s.immort) + 'px');
    root.style.setProperty('--btn-blast',  (s.blast || s.fly)    + 'px');
    root.style.setProperty('--ctrl-bar-h', s.ctrlH  + 'px');

    document.querySelectorAll('.ctrl-zone').forEach(z => z.style.bottom = s.ctrlBottom + 'px');
    const cl = document.getElementById('ctrlLeft');
    const cr = document.getElementById('ctrlRight');
    if (cl) cl.style.gap = s.gap + 'px';
    if (cr) cr.style.gap = Math.round(s.gap * 0.6) + 'px';

    requestAnimationFrame(() => requestAnimationFrame(scaleGame));
  }
  window.applyDeviceLayout = applyDeviceLayout;

  // ═══════════════════════════════════
  // PORTRAIT WATCH (phone only)
  // ═══════════════════════════════════
  function _checkPortrait() {
    const isPortrait = window.innerHeight > window.innerWidth;
    const pb = document.getElementById('portraitBlock');
    if (pb) pb.style.display = isPortrait ? 'flex' : 'none';
    if (!isPortrait) scaleGame();
  }

  function tryLockLandscape() {
    try {
      if (screen.orientation && screen.orientation.lock)
        screen.orientation.lock('landscape').catch(()=>{});
    } catch(e) {}
  }

  // ═══════════════════════════════════
  // AFTER DEVICE CHOSEN
  // ═══════════════════════════════════
  function _afterDeviceChosen(type) {
    const picker = document.getElementById('devicePicker');
    const pb     = document.getElementById('portraitBlock');
    if (picker) picker.style.display = 'none';
    if (pb)     pb.style.display     = 'none';

    applyDeviceLayout(type);

    if (type === 'phone') {
      tryLockLandscape();
      _checkPortrait();
      window.addEventListener('resize',            _checkPortrait);
      window.addEventListener('orientationchange', () => setTimeout(_checkPortrait, 200));
    }

    if (typeof showMenu === 'function') showMenu('mainMenu');
  }

  // ═══════════════════════════════════
  // INIT — always show picker first
  // ═══════════════════════════════════
  function initDevicePicker() {
    // ALWAYS show picker — no exceptions, no saved-state skipping
    const picker = document.getElementById('devicePicker');
    if (picker) picker.style.display = 'flex';
  }

  function _pick(type) {
    localStorage.setItem('cubix_device', type);
    sessionStorage.setItem('cubix_chosen', '1');
    _afterDeviceChosen(type);
  }

  const bPhone  = document.getElementById('bDevPhone');
  const bIpad   = document.getElementById('bDevIpad');
  const bLaptop = document.getElementById('bDevLaptop');
  if (bPhone)  bPhone.addEventListener('click',  () => _pick('phone'));
  if (bIpad)   bIpad.addEventListener('click',   () => _pick('tablet'));
  if (bLaptop) bLaptop.addEventListener('click', () => _pick('laptop'));

  initDevicePicker();

  // Re-scale on resize
  if (window.visualViewport) {
    window.visualViewport.addEventListener('resize', scaleGame);
  }
  window.addEventListener('resize', scaleGame);
  window.addEventListener('orientationchange', () => {
    setTimeout(scaleGame, 100);
    setTimeout(scaleGame, 400);
  });

  // ═══════════════════════════════════
  // TOUCH CONTROLS
  // ═══════════════════════════════════
  const BTN_MAP = {
    btnLeft:  ['ArrowLeft'],
    btnRight: ['ArrowRight'],
    btnJump:  ['Space'],
  };

  // Direct K/JP manipulation — reliable on all mobile browsers
  function _press(key) {
    if (typeof K !== 'undefined') K[key] = true;
    if (typeof JP !== 'undefined') JP[key] = true;
    // Also dispatch on document for any other listeners
    document.dispatchEvent(new KeyboardEvent('keydown', { code: key, bubbles: true }));
  }
  function _release(key) {
    if (typeof K !== 'undefined') K[key] = false;
    document.dispatchEvent(new KeyboardEvent('keyup', { code: key, bubbles: true }));
  }

  Object.entries(BTN_MAP).forEach(([btnId, keys]) => {
    const el = document.getElementById(btnId);
    if (!el) return;
    el.addEventListener('touchstart', e => { e.preventDefault(); keys.forEach(_press); el.classList.add('pressed'); }, { passive: false });
    el.addEventListener('touchend',   e => { e.preventDefault(); keys.forEach(k => { if(typeof K!=='undefined') K[k]=false; }); keys.forEach(_release); el.classList.remove('pressed'); }, { passive: false });
    el.addEventListener('touchcancel',e => { keys.forEach(k => { if(typeof K!=='undefined') K[k]=false; }); keys.forEach(_release); el.classList.remove('pressed'); });
  });



  // Blast jump button — all players
  const blastBtn = document.getElementById('btnBlast');
  if(blastBtn){
    blastBtn.addEventListener('touchstart', e=>{ e.preventDefault(); blastBtn.classList.add('pressed'); if(typeof K!=='undefined'){ K['KeyS']=true; } }, {passive:false});
    blastBtn.addEventListener('touchend',   e=>{ e.preventDefault(); blastBtn.classList.remove('pressed'); if(typeof K!=='undefined'){ K['KeyS']=false; } }, {passive:false});
    blastBtn.addEventListener('touchcancel',()=>{ blastBtn.classList.remove('pressed'); if(typeof K!=='undefined') K['KeyS']=false; });
  }

  const immBtn = document.getElementById('btnImmort');
  if (immBtn) {
    immBtn.addEventListener('touchstart', e => {
      e.preventDefault(); immBtn.classList.add('pressed');
    }, { passive: false });
    immBtn.addEventListener('touchend', e => {
      e.preventDefault(); immBtn.classList.remove('pressed');
      if(typeof _isOwner==='function' && _isOwner()){
        _immortal=!_immortal;
        _flashPriv(_immortal?'★ IMMORTAL ON':'★ IMMORTAL OFF','rgba(255,200,0,0.7)');
      }
    }, { passive: false });
  }

  // ── Dim controls when not playing ──
  setInterval(() => {
    const ctrl = document.getElementById('mobileControls');
    if (!ctrl) return;
    const dtype = window._deviceType;
    if (!dtype || dtype === 'laptop') { ctrl.style.display = 'none'; return; }
    ctrl.style.display      = 'flex';
    const inPlay = (typeof STATE !== 'undefined' && STATE === 'playing');
    ctrl.style.opacity      = inPlay ? '1' : '0.15';
    ctrl.style.pointerEvents = inPlay ? '' : 'none';
  }, 200);

  // ── Level badge ──
  setInterval(() => {
    const badge = document.getElementById('_lvlBadge');
    if (!badge) return;
    const playing = typeof STATE !== 'undefined' && STATE === 'playing';
    badge.textContent = playing ? 'LVL '+(lvlIdx+1)+'  /  '+(LEVELS[lvlIdx]?LEVELS[lvlIdx].name:'') : '';
    badge.style.opacity = playing ? '1' : '0';
  }, 200);

  // ── Pause button ──
  setTimeout(() => {
    const pb = document.getElementById('_pauseBtn');
    if (!pb) return;
    function doPause(e) {
      e.preventDefault(); e.stopPropagation();
      if (typeof STATE === 'undefined') return;
      if (STATE === 'playing') { STATE = 'paused';  showMenu('pauseMenu'); }
      else if (STATE === 'paused') { STATE = 'playing'; showMenu(null, true); }
    }
    pb.addEventListener('touchstart', doPause, { passive: false });
    pb.addEventListener('click', doPause);
  }, 0);

  setInterval(() => {
    const pb = document.getElementById('_pauseBtn');
    if (!pb) return;
    const playing = typeof STATE !== 'undefined' && STATE === 'playing';
    const paused  = typeof STATE !== 'undefined' && STATE === 'paused';
    pb.style.display = (playing || paused) ? 'flex' : 'none';
    pb.textContent = paused ? '▶' : '⏸';
  }, 200);

  // ── iOS Audio unlock ──
  function _unlockAudio() {
    try {
      const a = AC();
      if (a.state === 'suspended') a.resume();
      const buf = a.createBuffer(1,1,22050);
      const src = a.createBufferSource();
      src.buffer = buf; src.connect(a.destination); src.start(0);
    } catch(e) {}
  }
  document.addEventListener('touchstart', _unlockAudio, { passive: true });

  // ── Mobile sound effects ──
  function _mTone(freq, type, dur, vol, delay) {
    setTimeout(() => {
      try {
        const a=AC(), o=a.createOscillator(), g=a.createGain();
        o.connect(g); g.connect(a.destination);
        o.type=type; o.frequency.value=freq;
        g.gain.setValueAtTime(0, a.currentTime);
        g.gain.linearRampToValueAtTime(vol, a.currentTime+0.012);
        g.gain.exponentialRampToValueAtTime(0.001, a.currentTime+dur);
        o.start(); o.stop(a.currentTime+dur+0.05);
      } catch(e) {}
    }, delay || 0);
  }
  function _sfxTap()        { _mTone(900,'sine',0.04,0.10); }
  function _sfxConfirm()    { _mTone(440,'sine',0.12,0.12); _mTone(660,'sine',0.16,0.13,80); _mTone(880,'sine',0.18,0.11,160); }
  function _sfxLevelClear() { [523,659,784,1047].forEach((f,i)=>_mTone(f,'sine',0.22,0.13,i*90)); }
  function _sfxGameOver()   { _mTone(440,'sawtooth',0.3,0.15); _mTone(280,'sawtooth',0.4,0.15,200); }
  function _sfxPause()      { _mTone(600,'sine',0.10,0.09); _mTone(400,'sine',0.14,0.09,80); }

  ['btnLeft','btnRight','btnJump','btnFly','btnImmort',
   'bPlay','bResume','bResume2','bNext','bWP','bWardrobe','bSwitchDevice','bReboot'].forEach(id => {
    const el = document.getElementById(id);
    if (el) el.addEventListener('touchstart', _sfxTap, { passive: true });
  });
  ['bDevPhone','bDevIpad','bDevLaptop'].forEach(id => {
    const el = document.getElementById(id);
    if (el) el.addEventListener('click', _sfxConfirm);
  });

  setInterval(() => {
    if (typeof STATE === 'undefined') return;
    if (STATE === 'levelcomplete' && !window._lcPlayed) { window._lcPlayed=true; _sfxLevelClear(); }
    else if (STATE !== 'levelcomplete') window._lcPlayed = false;
    if (STATE === 'gameover' && !window._goPlayed) { window._goPlayed=true; _sfxGameOver(); }
    else if (STATE !== 'gameover') window._goPlayed = false;
    if (STATE === 'paused' && !window._pPlayed) { window._pPlayed=true; _sfxPause(); }
    else if (STATE !== 'paused') window._pPlayed = false;
  }, 150);

  // ── lvl switcher ──
  const lvlBtn = document.getElementById('_lvlSwitchBtn');
  if (lvlBtn) {
    lvlBtn.addEventListener('click', () => {
      if (typeof lvlIdx === 'undefined') return;
      lvlIdx = (lvlIdx + 1) % (typeof LEVELS !== 'undefined' ? LEVELS.length : 10);
      if (typeof initLvl === 'function') { initLvl(lvlIdx); if(typeof _initCheckpoints==='function') _initCheckpoints(); }
    });
  }

  // ── Allow scroll on menus only ──
  document.addEventListener('touchmove', e => {
    if (typeof STATE !== 'undefined' && STATE === 'playing') e.preventDefault();
  }, { passive: false });



  // safe-area probe
  (function() {
    const probe = document.createElement('div');
    probe.style.cssText = 'position:fixed;bottom:0;height:env(safe-area-inset-bottom,0px);pointer-events:none;';
    document.body.appendChild(probe);
    const sab = probe.getBoundingClientRect().height || 0;
    document.documentElement.style.setProperty('--sab', sab + 'px');
    document.body.removeChild(probe);
  })();

})();
</script>
</body>
</html>

</body>
</html>
