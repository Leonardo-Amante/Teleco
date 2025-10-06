<html lang="es">
<head>
<meta charset="utf-8" />
<meta name="viewport" content="width=device-width,initial-scale=1" />
<title>¿Quieres celebrar conmigo?</title>
<style>
  /* Estilo inspirado en la página referida */
  :root{
    --bg:#080808;
    --card-bg:rgba(255,255,255,0.03);
    --accent:#f03b3b;
    --muted:#ddd;
    --btn-bg:#111;
    --btn-border:#444;
  }
  html,body{height:100%;margin:0;font-family:system-ui,-apple-system,Segoe UI,Roboto,'Helvetica Neue',Arial;color:var(--muted);background:linear-gradient(180deg,#000,#060606);display:flex;align-items:center;justify-content:center;overflow:hidden}
  .stage{position:relative;z-index:2;text-align:center;padding:28px;border-radius:10px}
  h1{font-size:28px;margin-bottom:18px;color:#fff;letter-spacing:0.4px}
  p.lead{color:#bbb;margin-bottom:22px}
  .buttons{display:flex;gap:18px;justify-content:center;align-items:center}
  .btn{
    background:var(--btn-bg);
    color:#fff;
    padding:12px 22px;
    border-radius:8px;
    border:2px solid var(--btn-border);
    cursor:pointer;
    font-weight:700;
    box-shadow: 0 6px 18px rgba(0,0,0,0.6), 0 0 14px rgba(255,0,0,0.02) inset;
    transition:transform .18s ease, background .18s ease;
    user-select:none;
  }
  .btn:active{transform:translateY(2px) scale(.995)}
  .btn.yes{background:linear-gradient(90deg,#13dea0,#06b1ff); color:#00101a; border-color: rgba(255,255,255,0.06)}
  .btn.no{background:linear-gradient(90deg,#222,#101010); border-color:#333}

  /* Overlay that appears after pressing Yes */
  #overlay{
    position:fixed;inset:0;display:flex;align-items:center;justify-content:center;flex-direction:column;
    background:rgba(0,0,0,0.95);backdrop-filter: blur(2px);opacity:0;pointer-events:none;transition:opacity .35s ease;z-index:50;
  }
  #overlay.show{opacity:1;pointer-events:auto}
  #overlay .message{
    color:#fff;text-align:center;max-width:90vw;padding:12px;border-radius:10px;
  }
  #overlay .big{
    font-size:48px;font-weight:800;margin-bottom:12px;text-shadow:0 0 18px rgba(255,255,255,0.06);
    background: linear-gradient(90deg,#ff6ec7,#00eaff);
    -webkit-background-clip:text;-webkit-text-fill-color:transparent;
  }

  /* image that pops in */
  #overlay img {
    max-width: min(520px, 78vw);
    width: 100%;
    border-radius:12px;
    box-shadow: 0 30px 90px rgba(0,0,0,0.7), 0 0 40px rgba(0,170,255,0.08);
    transform: scale(.92);
    opacity:0;
    transition:transform .45s cubic-bezier(.2,.9,.2,1),opacity .25s ease;
  }
  #overlay.show img{ transform: scale(1); opacity:1 }

  /* quick flash effect */
  .flash {
    position:fixed;inset:0;background:#fff;opacity:0;pointer-events:none;mix-blend-mode:screen;transition:opacity .08s linear;z-index:60;
  }

  /* small moving background shapes (subtle) */
  .bg-shapes{
    position:fixed;inset:0;z-index:0;pointer-events:none;overflow:hidden;
  }
  .shape{
    position:absolute;border-radius:50%;opacity:.06;filter:blur(28px);
  }
  .shape.s1{width:520px;height:520px;background:#00eaff;left:-120px;top:-80px}
  .shape.s2{width:420px;height:420px;background:#ff6ec7;right:-100px;bottom:-100px}
  .shape.s3{width:260px;height:260px;background:#a46bff;left:10%;bottom:10%}

  /* small responsive tweaks */
  @media (max-width:520px){
    h1{font-size:20px}
    #overlay .big{font-size:34px}
    .btn{padding:10px 16px}
  }
</style>
</head>
<body>
  <div class="bg-shapes" aria-hidden="true">
    <div class="shape s1"></div>
    <div class="shape s2"></div>
    <div class="shape s3"></div>
  </div>

  <div class="stage" role="main" aria-labelledby="q" aria-describedby="desc">
    <h1 id="q">¿Quieres celebrar Halloween conmigo?</h1>
    <p id="desc" class="lead">Será divertido. ¿Te animas?</p>

    <div class="buttons" id="btns">
      <button class="btn yes" id="yesBtn">Sí</button>
      <button class="btn no" id="noBtn">No</button>
    </div>
  </div>

  <!-- overlay / efecto sorpresa -->
  <div id="overlay" role="dialog" aria-hidden="true">
    <div class="message">
      <div class="big">¡Bienvenido! 🎃</div>
      <div style="color:#d9e6ff;margin-bottom:18px">Gracias por aceptar. Disfruta esto con cuidado 😉</div>
      <!-- imagen de impacto -->
      <img id="impactImg" src="https://images.unsplash.com/photo-1508873696983-2dfd5898f3b5?auto=format&fit=crop&w=1200&q=80" alt="Imagen sorpresa">
      <div style="margin-top:18px"><button class="btn" id="closeOverlay">Cerrar</button></div>
    </div>
  </div>

  <!-- flash -->
  <div class="flash" id="flash"></div>

<script>
/* Comportamiento:
 - Botón NO se mueve cuando el cursor se acerca (imitando la página original).
 - Botón SÍ muestra overlay + flash visual + sonido corto (sintetizado).
 - Sonido generado por WebAudio (burst corto de ruido + filtro) para evitar archivos externos.
*/

const yesBtn = document.getElementById('yesBtn');
const noBtn = document.getElementById('noBtn');
const overlay = document.getElementById('overlay');
const flash = document.getElementById('flash');
const closeOverlay = document.getElementById('closeOverlay');
const stage = document.querySelector('.stage');
const btns = document.getElementById('btns');

let moved = false;

// ------------- "No" button: huye cuando el cursor se acerca -------------
noBtn.addEventListener('mouseenter', () => {
  // move to random position within the button container area
  const containerRect = btns.getBoundingClientRect();
  const btnRect = noBtn.getBoundingClientRect();
  const padding = 8;
  // compute new position relative to container
  const maxX = Math.max(0, containerRect.width - btnRect.width - padding*2);
  const maxY = 0; // keep same row for simplicity
  const newX = Math.floor(Math.random() * (maxX + 1));
  // apply transform
  noBtn.style.transform = `translateX(${newX - (btnRect.left - containerRect.left - padding)}px) translateY(0)`;
  noBtn.style.transition = 'transform .22s ease';
});

// keep Yes button stationary; also reset No button on window resize
window.addEventListener('resize', ()=> {
  noBtn.style.transform = '';
});

// If user clicks No, show playful message
noBtn.addEventListener('click', () => {
  // small playful fallback if they manage to click
  alert("¿En serio? ¡Anímate! 🎃");
});

// ------------- "Yes" button: show overlay + play sound -------------
yesBtn.addEventListener('click', async () => {
  // show overlay
  overlay.classList.add('show');
  overlay.setAttribute('aria-hidden','false');

  // small visual flash
  flash.style.opacity = '0.9';
  setTimeout(()=> flash.style.opacity = '0', 120);

  // play short glitchy burst (safety: short + controlled volume)
  try {
    await playGlitchSound({volume:0.18, duration: 400});
  } catch(e){
    // if audio blocked, resume AudioContext on user gesture
    console.warn('Audio issue:', e);
  }
});

// close overlay
closeOverlay.addEventListener('click', ()=>{
  overlay.classList.remove('show');
  overlay.setAttribute('aria-hidden','true');
});

// ========== WebAudio synth for a short glitch/noise burst ==========
let audioCtx = null;
async function ensureAudioCtx(){
  if (!audioCtx) {
    audioCtx = new (window.AudioContext || window.webkitAudioContext)();
  }
  // resume if suspended
  if (audioCtx.state === 'suspended') await audioCtx.resume();
  return audioCtx;
}

/**
 * playGlitchSound
 * options: {volume: 0..1, duration: ms}
 */
async function playGlitchSound(opts = {}) {
  const { volume = 0.2, duration = 350 } = opts;
  const ctx = await ensureAudioCtx();

  // create noise buffer
  const bufferSize = ctx.sampleRate * 0.2; // 0.2s buffer reused
  const noiseBuffer = ctx.createBuffer(1, bufferSize, ctx.sampleRate);
  const data = noiseBuffer.getChannelData(0);
  for (let i = 0; i < bufferSize; i++) data[i] = (Math.random() * 2 - 1) * 0.7;

  // source for noise (short burst)
  const noise = ctx.createBufferSource();
  noise.buffer = noiseBuffer;
  noise.loop = false;

  // bandpass filter for "glitchy" tone
  const band = ctx.createBiquadFilter();
  band.type = 'bandpass';
  band.frequency.value = 1000 + Math.random()*4000;
  band.Q.value = 1 + Math.random()*6;

  // gain envelope
  const g = ctx.createGain();
  g.gain.value = 0.0001;

  // connect nodes
  noise.connect(band);
  band.connect(g);
  g.connect(ctx.destination);

  // volume control
  const master = ctx.createGain();
  master.gain.value = volume;
  g.connect(master);
  master.connect(ctx.destination);

  // start and ramp
  const now = ctx.currentTime;
  noise.start(now);
  // quick attack
  master.gain.setValueAtTime(0.0001, now);
  master.gain.exponentialRampToValueAtTime(Math.max(0.06, volume), now + 0.02);
  // decay
  master.gain.exponentialRampToValueAtTime(0.0001, now + duration/1000);

  // stop nodes after duration + small tail
  setTimeout(()=> {
    try { noise.stop(); } catch(e){}
  }, duration + 80);
}

/* Accessibility: allow keyboard activation of buttons */
yesBtn.addEventListener('keydown', (e)=>{ if(e.key === 'Enter' || e.key === ' ') yesBtn.click(); });
noBtn.addEventListener('keydown', (e)=>{ if(e.key === 'Enter' || e.key === ' ') noBtn.click(); });

</script>
</body>
</html>
