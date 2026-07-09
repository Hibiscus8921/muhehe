<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0">
<title>For You</title>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link href="https://fonts.googleapis.com/css2?family=Great+Vibes&family=Cormorant+Garamond:ital,wght@0,400;0,500;0,600;1,400&family=EB+Garamond:ital@0;1&display=swap" rel="stylesheet">
<style>
  :root{
    --maroon: #4a1420;
    --maroon-deep: #2a0b12;
    --blush: #f2c9c9;
    --gold: #cda45e;
    --gold-bright: #e8c98a;
    --paper: #fbf1de;
    --paper-shadow: #e9dcc0;
    --ink: #3a1c1c;
  }
  *{ box-sizing: border-box; }
  html, body{
    margin:0; padding:0; width:100%; height:100%;
    background: radial-gradient(ellipse at center, var(--maroon) 0%, var(--maroon-deep) 75%);
    overflow: hidden;
    font-family: 'EB Garamond', serif;
  }
  #stage{
    position: relative;
    width: 100vw; height: 100vh;
    display: flex; align-items: center; justify-content: center;
    perspective: 1800px;
  }

  /* ---------- Ambient vignette & grain ---------- */
  .vignette{
    position: absolute; inset: 0; pointer-events: none; z-index: 5;
    background: radial-gradient(ellipse at center, transparent 40%, rgba(0,0,0,0.55) 100%);
  }
  .grain{
    position: absolute; inset: 0; pointer-events: none; z-index: 6; opacity: 0.05; mix-blend-mode: overlay;
    background-image: url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' width='120' height='120'%3E%3Cfilter id='n'%3E%3CfeTurbulence type='fractalNoise' baseFrequency='0.9' numOctaves='2' stitchTiles='stitch'/%3E%3C/filter%3E%3Crect width='100%25' height='100%25' filter='url(%23n)'/%3E%3C/svg%3E");
  }

  /* ---------- Title flourish ---------- */
  .prelude{
    position: absolute; top: 12%; left: 50%; transform: translate(-50%, -10px);
    text-align: center; z-index: 4; color: var(--gold-bright);
    font-family: 'Great Vibes', cursive; font-size: 2.6rem;
    opacity: 0; letter-spacing: 1px;
    text-shadow: 0 0 18px rgba(232,201,138,0.5);
    animation: preludeIn 2.5s ease-out 0.4s forwards, preludeOut 1.5s ease-in 6.5s forwards;
  }
  @keyframes preludeIn{ from{ opacity:0; transform: translate(-50%,10px);} to{ opacity:1; transform: translate(-50%,0);} }
  @keyframes preludeOut{ to{ opacity:0; } }

  /* ---------- Floating hearts & sparkles ---------- */
  .float-layer{ position:absolute; inset:0; z-index: 3; pointer-events:none; overflow:hidden; }
  .heart, .spark{ position:absolute; opacity:0; }
  .heart{ color: var(--blush); font-size: 22px; filter: drop-shadow(0 0 6px rgba(242,201,201,0.5)); animation: floatUp linear infinite; }
  .spark{ width:5px; height:5px; background: var(--gold-bright); border-radius:50%; box-shadow: 0 0 8px 2px rgba(232,201,138,0.8); animation: floatUp linear infinite; }
  @keyframes floatUp{
    0%{ opacity:0; transform: translateY(0) translateX(0) rotate(0deg) scale(0.6); }
    10%{ opacity: 0.9; }
    90%{ opacity: 0.7; }
    100%{ opacity:0; transform: translateY(-100vh) translateX(var(--drift,20px)) rotate(180deg) scale(1); }
  }

  /* ---------- Rose petals ---------- */
  .petal{
    position:absolute; top:-40px; z-index: 7; pointer-events:none;
    width: 16px; height: 12px; border-radius: 60% 20% 60% 20%;
    background: linear-gradient(135deg, #b8324a, #6f1626);
    opacity: 0.9; animation: petalFall linear infinite;
  }
  @keyframes petalFall{
    0%{ transform: translateY(-5vh) translateX(0) rotate(0deg); opacity: 0; }
    8%{ opacity: 0.9; }
    100%{ transform: translateY(110vh) translateX(var(--sway, 60px)) rotate(360deg); opacity: 0.3; }
  }

  /* ---------- Envelope ---------- */
  #envelope-wrap{
    position: relative; width: 340px; height: 220px; z-index: 2;
    transform-style: preserve-3d;
    animation: envRise 1.6s cubic-bezier(.2,.8,.2,1) 0.2s both, envDrift 4s ease-in-out 1.8s infinite;
  }
  @keyframes envRise{ from{ opacity:0; transform: translateY(60px) scale(0.85);} to{ opacity:1; transform: translateY(0) scale(1);} }
  @keyframes envDrift{ 0%,100%{ transform: translateY(0);} 50%{ transform: translateY(-8px);} }

  .env-shadow{
    position:absolute; bottom:-38px; left:50%; width:280px; height:26px; margin-left:-140px;
    background: radial-gradient(ellipse at center, rgba(0,0,0,0.5), transparent 70%);
    animation: shadowPulse 4s ease-in-out 1.8s infinite;
  }
  @keyframes shadowPulse{ 0%,100%{ opacity:0.6; transform: scale(1);} 50%{ opacity:0.4; transform: scale(0.92);} }

  .env-body{
    position:absolute; inset:0; border-radius: 4px;
    background: linear-gradient(155deg, #f6ead2, #e9d7ae 60%, #ddc794);
    box-shadow: 0 25px 50px rgba(0,0,0,0.5), inset 0 0 30px rgba(120,80,30,0.15);
  }
  .env-body::before{ /* subtle paper fiber texture */
    content:""; position:absolute; inset:0; border-radius:4px; opacity:0.5;
    background-image: repeating-linear-gradient(115deg, rgba(255,255,255,0.06) 0 2px, transparent 2px 5px);
  }

  .env-pocket-left, .env-pocket-right{
    position:absolute; top:0; width:0; height:0; z-index:3;
    border-style: solid;
  }
  .env-pocket-left{
    left:0; border-width: 110px 0 110px 170px;
    border-color: transparent transparent transparent #ecdcb8;
    filter: brightness(0.96);
  }
  .env-pocket-right{
    right:0; border-width: 110px 170px 110px 0;
    border-color: transparent #ecdcb8 transparent transparent;
    filter: brightness(0.9);
  }

  .letter{
    position:absolute; left: 50%; top: -6px;
    width: min(560px, 88vw); max-height: 78vh;
    transform: translateX(-50%);
    background: var(--paper);
    background-image:
      repeating-linear-gradient(0deg, rgba(0,0,0,0.015) 0 1px, transparent 1px 26px);
    border-radius: 3px;
    box-shadow: 0 8px 25px rgba(0,0,0,0.35), inset 0 0 40px rgba(160,120,60,0.08);
    z-index: 2;
    padding: 34px 34px 26px;
    overflow-y: auto;
    opacity: 0;
  }
  .letter.rise{
    animation: letterRise 1.8s cubic-bezier(.16,.9,.28,1) 6.4s forwards,
               letterToFront 0.1s linear 6.4s forwards;
  }
  @keyframes letterToFront{
    to{ z-index: 20; }
  }
  @keyframes letterRise{
    0%{ opacity:0; transform: translateX(-50%) translateY(10px) scale(0.98); }
    15%{ opacity:1; }
    100%{ opacity:1; transform: translateX(-50%) translateY(-140px) scale(1.12); }
  }
  .letter-heading{
    font-family:'Great Vibes', cursive; color: var(--maroon); font-size: 2rem;
    text-align:center; margin: 0 0 6px; opacity:0;
  }
  .letter-body{
    font-family:'Cormorant Garamond', serif; font-style: italic; color: var(--ink);
    font-size: 1.18rem; line-height: 1.75; text-align: center;
    white-space: pre-wrap; min-height: 170px;
  }
  .letter-sign{
    font-family:'Great Vibes', cursive; color: var(--maroon); font-size: 1.7rem;
    text-align:right; margin-top: 14px; opacity: 0;
  }
  .caret{ display:inline-block; width: 2px; background: var(--maroon); margin-left: 2px; animation: blink 0.9s steps(1) infinite; }
  @keyframes blink{ 50%{ opacity:0; } }

  /* Envelope flap (triangle) with 3D fold */
  .env-flap{
    position:absolute; top:0; left:0; width:100%; height:112px;
    transform-origin: top center; transform-style: preserve-3d;
    z-index: 4;
    animation: flapOpen 1.4s cubic-bezier(.5,-0.2,.3,1.2) 4.4s forwards;
  }
  .flap-face{
    position:absolute; inset:0; clip-path: polygon(0 0, 100% 0, 50% 100%);
    background: linear-gradient(160deg, #efdfb9, #ddc48f);
    backface-visibility: hidden;
    box-shadow: inset 0 -6px 14px rgba(120,80,30,0.15);
  }
  .flap-back{
    position:absolute; inset:0; clip-path: polygon(0 0, 100% 0, 50% 100%);
    background: linear-gradient(160deg, #d8c39a, #c7a96f);
    transform: rotateX(180deg); backface-visibility: hidden;
  }
  @keyframes flapOpen{
    from{ transform: rotateX(0deg); }
    to{ transform: rotateX(178deg); }
  }

  /* Wax seal */
  .wax-seal{
    position:absolute; top: 92px; left:50%; width:58px; height:58px; margin-left:-29px;
    border-radius:50%; z-index: 6;
    background: radial-gradient(circle at 35% 30%, #b8324a, #7a1626 70%, #5c0f1c);
    box-shadow: 0 4px 10px rgba(0,0,0,0.5), inset 0 2px 4px rgba(255,255,255,0.25), inset 0 -3px 6px rgba(0,0,0,0.4);
    display:flex; align-items:center; justify-content:center;
    animation: sealGlow 2s ease-in-out 2.2s 2, sealCrack 0.5s ease-in 4.35s forwards, sealFall 1s cubic-bezier(.5,-0.3,.6,1) 4.5s forwards;
  }
  .wax-seal::before{
    content:"♥"; color: rgba(255,230,220,0.85); font-size: 22px; font-family: Georgia, serif;
    text-shadow: 0 1px 1px rgba(0,0,0,0.4);
  }
  .wax-seal::after{
    content:""; position:absolute; inset:0; border-radius:50%; opacity:0;
  }
  @keyframes sealGlow{
    0%,100%{ box-shadow: 0 4px 10px rgba(0,0,0,0.5), inset 0 2px 4px rgba(255,255,255,0.25), inset 0 -3px 6px rgba(0,0,0,0.4), 0 0 0 rgba(232,201,138,0); }
    50%{ box-shadow: 0 4px 16px rgba(0,0,0,0.5), inset 0 2px 4px rgba(255,255,255,0.35), inset 0 -3px 6px rgba(0,0,0,0.4), 0 0 22px 4px rgba(232,201,138,0.55); }
  }
  @keyframes sealCrack{
    0%{ clip-path: circle(50% at 50% 50%); }
    100%{ clip-path: polygon(0 0,45% 0,50% 40%,55% 0,100% 0,100% 45%,60% 50%,100% 55%,100% 100%,55% 100%,50% 60%,45% 100%,0 100%,0 55%,40% 50%,0 45%); }
  }
  @keyframes sealFall{
    0%{ opacity:1; transform: translateY(0) rotate(0deg) scale(1); }
    60%{ opacity:1; }
    100%{ opacity:0; transform: translateY(120px) rotate(120deg) scale(0.6); }
  }

  /* Skip hint */
  .skip-hint{
    position:absolute; bottom: 26px; left:50%; transform: translateX(-50%);
    color: rgba(232,201,138,0.55); font-family:'EB Garamond', serif; font-size:0.78rem;
    letter-spacing: 2px; text-transform: uppercase; z-index: 8; opacity:0;
    animation: hintIn 1s ease 8.2s forwards;
  }
  @keyframes hintIn{ to{ opacity:1; } }

  @media (prefers-reduced-motion: reduce){
    *{ animation-duration: 0.01ms !important; animation-iteration-count: 1 !important; }
  }

  @media (max-width: 480px){
    #envelope-wrap{ width: 280px; height: 180px; }
    .env-pocket-left{ border-width: 90px 0 90px 140px; }
    .env-pocket-right{ border-width: 90px 140px 90px 0; }
    .env-flap{ height: 92px; }
    .letter{ height: 220px; }
    .letter-body{ font-size: 1.02rem; }
    .prelude{ font-size: 2rem; top: 10%; }
  }
</style>
</head>
<body>

<div id="stage">
  <div class="vignette"></div>
  <div class="grain"></div>
  <div class="prelude">a letter, just for you</div>
  <div class="float-layer" id="floatLayer"></div>
  <div class="float-layer" id="petalLayer"></div>

  <div id="envelope-wrap">
    <div class="env-shadow"></div>
    <div class="letter" id="letter">
      <div class="letter-heading" id="letterHeading">Dear Bilal,</div>
      <div class="letter-body" id="letterBody"></div>
      <div class="letter-sign" id="letterSign">With love, Hibiscus</div>
    </div>
    <div class="env-body"></div>
    <div class="env-pocket-left"></div>
    <div class="env-pocket-right"></div>
    <div class="env-flap">
      <div class="flap-face"></div>
      <div class="flap-back"></div>
    </div>
    <div class="wax-seal"></div>
  </div>

  <div class="skip-hint">tap anywhere to replay</div>
</div>

<audio id="bgm" loop preload="auto">
  <source src="https://actions.google.com/sounds/v1/ambiences/coffee_shop.ogg" type="audio/ogg">
</audio>

<script>
(function(){
  const floatLayer = document.getElementById('floatLayer');
  const petalLayer = document.getElementById('petalLayer');

  function spawnFloats(){
    for(let i=0;i<18;i++){
      const el = document.createElement('div');
      const isHeart = Math.random() > 0.45;
      el.className = isHeart ? 'heart' : 'spark';
      if(isHeart) el.textContent = Math.random() > 0.5 ? '♥' : '❤';
      const left = Math.random()*100;
      const delay = Math.random()*8;
      const dur = 6 + Math.random()*6;
      const drift = (Math.random()*140 - 70) + 'px';
      el.style.left = left + 'vw';
      el.style.bottom = '-5vh';
      el.style.setProperty('--drift', drift);
      el.style.animationDelay = delay + 's';
      el.style.animationDuration = dur + 's';
      if(isHeart) el.style.fontSize = (14 + Math.random()*14) + 'px';
      floatLayer.appendChild(el);
    }
  }

  function spawnPetals(){
    for(let i=0;i<14;i++){
      const p = document.createElement('div');
      p.className = 'petal';
      p.style.left = (Math.random()*100) + 'vw';
      p.style.setProperty('--sway', (Math.random()*160-80)+'px');
      p.style.animationDelay = (Math.random()*10) + 's';
      p.style.animationDuration = (7 + Math.random()*6) + 's';
      p.style.transform = `scale(${0.7 + Math.random()*0.8})`;
      petalLayer.appendChild(p);
    }
  }

  spawnFloats();
  spawnPetals();

  // Typewriter letter content — edit this text freely
  const message = "In your arms and in your heart — I feel like I'm born again\n\nSnuggling in neck and creasing till peck — all I adore is the soul of your's\n\nPatting the back and fidgeting with hairs; the weight of your's feel like seashore\n\nAn edge for the drowning and seat for the swimmer, height for the seabed and Amethyst for the explorers\n\nHands in hands and hearts far from dusty land — when I'm with you I feel like I'm born again\n\nAbsorbing presence and dissolving in love. Oh what a misery to have you but still yearn\n\nIn your arms and in your heart — I feel like I'm born again";

  const bodyEl = document.getElementById('letterBody');
  const headingEl = document.getElementById('letterHeading');
  const signEl = document.getElementById('letterSign');
  const letterEl = document.getElementById('letter');

  function typewrite(el, text, speed, startDelay, onDone){
    setTimeout(()=>{
      let i = 0;
      const caret = document.createElement('span');
      caret.className = 'caret';
      caret.textContent = '\u00A0';
      function step(){
        if(i <= text.length){
          el.textContent = text.slice(0, i);
          el.appendChild(caret);
          i++;
          setTimeout(step, speed);
        } else {
          caret.remove();
          if(onDone) onDone();
        }
      }
      step();
    }, startDelay);
  }

  function runSequence(){
    letterEl.classList.add('rise');
    setTimeout(()=>{ headingEl.style.transition = 'opacity 1s ease'; headingEl.style.opacity = 1; }, 7600);
    typewrite(bodyEl, message, 28, 8100, ()=>{
      signEl.style.transition = 'opacity 1.2s ease';
      signEl.style.opacity = 1;
    });
  }

  runSequence();

  // Attempt background music autoplay (soft romantic ambience). Browsers may block
  // audio until a user gesture — falls back silently if blocked.
  const bgm = document.getElementById('bgm');
  bgm.volume = 0.25;
  const playPromise = bgm.play();
  if(playPromise !== undefined){
    playPromise.catch(()=>{ /* autoplay blocked — will start on first tap */ });
  }
  document.addEventListener('click', ()=>{ if(bgm.paused){ bgm.play().catch(()=>{}); } }, { once:true });

  // Tap anywhere after the reveal to replay the whole cinematic sequence
  let replayReady = false;
  setTimeout(()=>{ replayReady = true; }, 9000);
  document.getElementById('stage').addEventListener('click', ()=>{
    if(!replayReady) return;
    location.reload();
  });
})();
</script>
</body>
</html>
