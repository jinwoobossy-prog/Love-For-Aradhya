<!doctype html>
<html lang="en">
<head>
<meta charset="utf-8" />
<meta name="viewport" content="width=device-width,initial-scale=1" />
<title>Debasish 💖 Aradhya</title>
<style>
/* Basic layout */
:root{
  --bg1:#ffb6c1; --bg2:#ffe4e1; --accent:#ff4d6d;
}
*{box-sizing:border-box}
html,body{height:100%;margin:0;font-family:'Segoe UI',sans-serif;background:linear-gradient(135deg,var(--bg1),var(--bg2));}

/* Respect reduced motion preference */
@media (prefers-reduced-motion:reduce){
  .heart, .kiss, .bear, canvas, .proposal-box { animation: none !important; transition: none !important; }
}

/* Floating hearts */
.heart {
  position:absolute;
  font-size:22px;
  animation:float linear infinite;
  opacity:.95;
  will-change:transform;
}
@keyframes float {
  from { transform: translateY(110vh) scale(0.9); opacity:0; }
  25% { opacity:0.9; }
  to { transform: translateY(-12vh) scale(1); opacity:0; }
}

/* Card */
.container{min-height:100vh;display:flex;align-items:center;justify-content:center;padding:20px}
.card {
  position:relative;
  z-index:5;
  background:rgba(255,255,255,0.97);
  padding:28px;
  border-radius:24px;
  width:min(720px,96%);
  text-align:center;
  box-shadow:0 18px 40px rgba(0,0,0,0.18);
}
#typing{white-space:pre-line;font-size:1.05rem;line-height:1.6;color:#5a1a2f;min-height:160px}

/* Buttons */
button {
  background:var(--accent); color:#fff; border:none; padding:12px 26px; border-radius:999px;
  font-size:1rem; cursor:pointer;
}
button:focus{outline:3px solid #ffd6e0}

/* Kiss */
.kiss{position:absolute;pointer-events:none;font-size:24px;animation:fade 1.1s forwards}
@keyframes fade { to { opacity:0; transform:translateY(-24px) scale(1.4); } }

/* Bear */
.bear{position:fixed;bottom:10px;left:20px;font-size:56px;animation:wave 2s infinite;z-index:4}
@keyframes wave{50%{transform:rotate(8deg)}}

/* Proposal modal */
.proposal{position:fixed;inset:0;background:rgba(0,0,0,0.6);display:none;align-items:center;justify-content:center;z-index:20}
.proposal[open]{display:flex}
.proposal-box{background:#fff;padding:28px;border-radius:16px;text-align:center;min-width:280px;max-width:92%;box-shadow:0 14px 40px rgba(0,0,0,.25)}
.proposal-box h1{color:var(--accent);margin:0 0 10px}

/* Canvas and music */
canvas{position:fixed;inset:0;pointer-events:none;z-index:1}
.music-btn{position:fixed;top:18px;right:18px;background:#ff6f91;color:#fff;border:none;border-radius:50%;width:56px;height:56px;font-size:22px;z-index:6;cursor:pointer}
.music-btn:focus{outline:3px solid #ffd6e0}

/* Small screens */
@media (max-width:420px){
  .bear{display:none}
  #typing{font-size:0.98rem}
}
</style>
</head>
<body>

<canvas id="fireworks" aria-hidden="true"></canvas>

<!-- Hearts (randomized in JS) -->
<div id="hearts" aria-hidden="true"></div>

<div class="bear" aria-hidden="true">🧸💗</div>

<!-- Audio -->
<audio id="bgMusic" loop preload="auto" aria-hidden="true">
  <source src="https://cdn.pixabay.com/download/audio/2022/03/15/audio_9c0b1b7a2e.mp3" type="audio/mpeg">
  <!-- Fallback text for non-supporting browsers -->
  Your browser does not support the audio element.
</audio>
<button class="music-btn" id="musicBtn" aria-pressed="false" aria-label="Play background music">🎶</button>

<div class="container">
  <main class="card" role="main" aria-labelledby="title">
    <h1 id="title">Aradhya 🥰</h1>
    <h3>You are my forever 💌</h3>

    <div id="typing" aria-live="polite" aria-atomic="true"></div>

    <div style="margin-top:18px">
      <button id="loveBtn">I Love You 💖</button>
    </div>

    <p style="margin-top:14px"><i>Forever yours,<br><b>Debasish</b></i></p>
  </main>
</div>

<!-- Proposal modal -->
<div class="proposal" id="proposal" role="dialog" aria-modal="true" aria-labelledby="proposalTitle" tabindex="-1">
  <div class="proposal-box" role="document">
    <h1 id="proposalTitle">💍 Will you be mine forever? 💖</h1>
    <p>I choose you. Today. Tomorrow. Always.</p>
    <div style="display:flex;gap:12px;justify-content:center;margin-top:12px">
      <button id="acceptBtn">YES 💕</button>
      <button id="closeProposal" aria-label="Close proposal" style="background:#ddd;color:#333">Maybe later</button>
    </div>
  </div>
</div>

<script>
/* Utility to create hearts with randomized size/delay */
(function populateHearts(){
  const container = document.getElementById('hearts');
  const heartChars = ['💖','💞','💕','💗','💘'];
  for(let i=0;i<6;i++){
    const s = document.createElement('span');
    s.className='heart';
    const left = 6 + i*14 + (Math.random()*6 - 3);
    s.style.left = left + '%';
    s.style.fontSize = (16 + Math.random()*26) + 'px';
    const dur = 6 + Math.random()*6;
    s.style.animationDuration = dur + 's';
    s.style.animationDelay = (Math.random()*-6) + 's';
    s.textContent = heartChars[i % heartChars.length];
    container.appendChild(s);
  }
})();

/* Typing effect (explicit element references) */
const typingEl = document.getElementById('typing');
const text = `Dear Aradhya,

Every heartbeat of mine
whispers your name 💓

You are my peace,
my madness,
my forever 🌸

No matter what,
I choose you. ❤️
`;
let tIndex = 0;
(function type(){
  if (tIndex < text.length) {
    typingEl.textContent += text[tIndex++];
    setTimeout(type, 38);
  }
})();

/* Kiss follow - throttled to reduce DOM churn */
let lastKiss = 0;
const kissThrottle = 50; // ms
function createKiss(x,y){
  const k = document.createElement('div');
  k.className = 'kiss';
  k.textContent = '😘';
  k.style.left = (x - 12) + 'px';
  k.style.top = (y - 12) + 'px';
  document.body.appendChild(k);
  setTimeout(()=>k.remove(), 1200);
}
function onPointerMove(e){
  const now = Date.now();
  if(now - lastKiss < kissThrottle) return;
  lastKiss = now;
  const p = e.touches ? e.touches[0] : e;
  createKiss(p.clientX, p.clientY);
}
document.addEventListener('mousemove', onPointerMove, {passive:true});
document.addEventListener('touchmove', onPointerMove, {passive:true});

/* Music handling with play() promise safety */
const bgMusic = document.getElementById('bgMusic');
const musicBtn = document.getElementById('musicBtn');
let musicPlaying = false;
async function toggleMusic(){
  try{
    if(!musicPlaying){
      await bgMusic.play();
      musicPlaying = true;
      musicBtn.setAttribute('aria-pressed','true');
      musicBtn.textContent = '🔊';
    } else {
      bgMusic.pause();
      musicPlaying = false;
      musicBtn.setAttribute('aria-pressed','false');
      musicBtn.textContent = '🎶';
    }
  } catch(err){
    console.warn('Audio play failed:', err);
  }
}
musicBtn.addEventListener('click', toggleMusic);

/* Fireworks canvas: responsive + basic particle bursts */
const canvas = document.getElementById('fireworks');
const ctx = canvas.getContext('2d');
let DPR = Math.max(1, window.devicePixelRatio || 1);
function resizeCanvas(){
  DPR = Math.max(1, window.devicePixelRatio || 1);
  canvas.width = Math.floor(window.innerWidth * DPR);
  canvas.height = Math.floor(window.innerHeight * DPR);
  canvas.style.width = window.innerWidth + 'px';
  canvas.style.height = window.innerHeight + 'px';
  ctx.setTransform(DPR,0,0,DPR,0,0);
}
window.addEventListener('resize', resizeCanvas);
resizeCanvas();

const particles = [];
function spawnBurst(x,y,color){
  const count = 28 + Math.floor(Math.random()*18);
  for(let i=0;i<count;i++){
    const angle = Math.random()*Math.PI*2;
    const speed = 1.6 + Math.random()*3.6;
    particles.push({
      x, y,
      vx: Math.cos(angle)*speed,
      vy: Math.sin(angle)*speed,
      life: 60 + Math.floor(Math.random()*40),
      age: 0,
      color
    });
  }
}
function draw(){
  ctx.clearRect(0,0,canvas.width/DPR,canvas.height/DPR);
  for(let i = particles.length-1; i>=0; i--){
    const p = particles[i];
    p.age++;
    p.vy += 0.04; // gravity
    p.x += p.vx;
    p.y += p.vy;
    const alpha = Math.max(0, 1 - p.age / p.life);
    ctx.fillStyle = `hsla(${p.color}, 100%, 60%, ${alpha})`;
    ctx.beginPath();
    ctx.arc(p.x, p.y, Math.max(1, 2 - p.age/30), 0, Math.PI*2);
    ctx.fill();
    if(p.age >= p.life) particles.splice(i,1);
  }
  requestAnimationFrame(draw);
}
requestAnimationFrame(draw);

/* Simpler firework trigger */
function firework(){
  const cx = Math.random() * window.innerWidth * 0.9 + window.innerWidth*0.05;
  const cy = Math.random() * window.innerHeight * 0.6 + window.innerHeight*0.1;
  const hue = Math.floor(Math.random()*360);
  spawnBurst(cx, cy, hue);
}

/* Love button -> show proposal modal */
const loveBtn = document.getElementById('loveBtn');
const proposal = document.getElementById('proposal');
const acceptBtn = document.getElementById('acceptBtn');
const closeProposal = document.getElementById('closeProposal');

function openProposal(){
  // show modal and move focus into it for accessibility
  proposal.setAttribute('open','');
  proposal.style.display = 'flex';
  proposal.focus();
  firework();
}
function closeProposalModal(){
  proposal.removeAttribute('open');
  proposal.style.display = 'none';
}

loveBtn.addEventListener('click', openProposal);
closeProposal.addEventListener('click', closeProposalModal);

/* Accept: celebrate and replace modal content gracefully */
acceptBtn.addEventListener('click', async function accept(){
  // Play music if not already
  if(!musicPlaying){
    try{ await bgMusic.play(); musicPlaying=true; musicBtn.textContent='🔊'; musicBtn.setAttribute('aria-pressed','true'); }catch(e){}
  }
  // Small celebration: multiple bursts
  for(let i=0;i<6;i++){
    setTimeout(firework, i*150);
  }
  // Replace modal with confirmation
  const box = proposal.querySelector('.proposal-box');
  box.innerHTML = "<h1 style='color:white;background:var(--accent);padding:12px;border-radius:8px'>💖 SHE SAID YES 💖</h1><p style='margin-top:10px'>Forever starts now.</p>";
  setTimeout(()=>closeProposalModal(), 2200);
});

/* Keyboard accessibility: Enter to open/accept, Escape to close */
document.addEventListener('keydown', e=>{
  if(e.key === 'Escape'){
    if(proposal.hasAttribute('open')) closeProposalModal();
  }
  // If focused on love button and Enter pressed -> open handled by click
});

/* Small autospark every few seconds to draw attention (but respect reduced-motion) */
if(!window.matchMedia('(prefers-reduced-motion:reduce)').matches){
  setInterval(()=>{ firework(); }, 4500);
}
</script>
</body>
</html>
