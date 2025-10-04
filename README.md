<!DOCTYPE html>
<html lang="es">
<head>
<meta charset="utf-8" />
<meta name="viewport" content="width=device-width,initial-scale=1" />
<title>Telecomunicaciones Dashboard — Efecto Oscuro</title>
<style>
  @import url('https://fonts.googleapis.com/css2?family=Orbitron:wght@500&display=swap');

  :root{
    --neon: #00ffae;
    --bg: #070809;
    --red: #ff0b0b;
  }

  html,body{
    height:100%;
    margin:0;
    font-family:'Orbitron', monospace;
    background:var(--bg);
    color:#cfeee6;
    overflow:hidden;
    display:flex;
    align-items:center;
    justify-content:center;
  }

  /* subtle animated grid behind */
  body::before{
    content:"";
    position:fixed; inset:0; z-index:0;
    background:
      linear-gradient(90deg, rgba(255,255,255,0.02) 1px, transparent 1px),
      linear-gradient(rgba(255,255,255,0.02) 1px, transparent 1px);
    background-size: 60px 60px, 60px 60px;
    opacity:0.15;
    animation: gridmove 90s linear infinite;
  }
  @keyframes gridmove { to { background-position:60px 60px,60px 60px; } }

  .card{
    position:relative;
    z-index:2;
    width:840px;
    max-width:95vw;
    padding:28px;
    border-radius:14px;
    background:linear-gradient(180deg, rgba(20,24,28,0.9), rgba(12,14,16,0.85));
    border:1px solid rgba(0,255,174,0.12);
    box-shadow: 0 8px 40px rgba(0,0,0,0.6), inset 0 0 60px rgba(0,255,174,0.02);
    display:flex;
    gap:24px;
    align-items:center;
    justify-content:space-between;
    flex-wrap:wrap;
  }

  .sensor{
    width:180px;
    height:120px;
    border-radius:12px;
    background:linear-gradient(145deg,#0c1112,#0e1416);
    border:1px solid rgba(0,255,174,0.08);
    display:flex;
    flex-direction:column;
    align-items:center;
    justify-content:center;
    color:var(--neon);
    box-shadow: 0 6px 18px rgba(0,255,174,0.06);
    transition: transform .15s;
  }
  .sensor:hover{ transform:translateY(-6px); }

  .sensor .label{ font-size:14px; opacity:0.9; }
  .sensor .value{ font-size:28px; margin-top:6px; font-weight:700; }

  .center{
    flex:1;
    min-width:240px;
    display:flex;
    flex-direction:column;
    align-items:center;
    justify-content:center;
    text-align:center;
  }

  .center h2{
    margin:0 0 12px;
    font-size:20px;
    color:var(--neon);
    letter-spacing:0.5px;
    text-shadow: 0 0 8px rgba(0,255,174,0.15);
  }

  .cta{
    background:linear-gradient(90deg,#00ffae,#00ffaa);
    color:#000;
    border:none;
    padding:12px 28px;
    border-radius:12px;
    font-weight:800;
    cursor:pointer;
    font-size:16px;
    box-shadow: 0 8px 28px rgba(0,255,174,0.12);
    transition: transform .18s, box-shadow .18s;
  }
  .cta:hover{ transform:translateY(-4px); box-shadow: 0 20px 40px rgba(0,255,174,0.18); }

  .footer{
    position:fixed; left:0; bottom:12px; width:100%; text-align:center; z-index:2;
    color:#9aa6ad; font-size:12px;
  }

  /* -------------- HACK EFFECT OVERLAY -------------- */
  #scareOverlay{
    position:fixed; inset:0; background:#000; z-index:9999; display:none;
    align-items:center; justify-content:center; overflow:hidden;
  }

  /* static tinted scanlines */
  #scareOverlay::before{
    content:""; position:absolute; inset:0;
    background: repeating-linear-gradient(180deg, rgba(255,255,255,0.02) 0 1px, transparent 1px 3px);
    mix-blend-mode:overlay; pointer-events:none;
  }

  /* red vignette */
  #scareOverlay::after{
    content:""; position:absolute; inset:0;
    background: radial-gradient(ellipse at center, rgba(255,0,0,0.06) 0%, rgba(0,0,0,0.9) 60%);
    pointer-events:none;
  }

  .scare-center{
    position:relative; z-index:3; width:100%; height:100%; display:flex; align-items:center; justify-content:center;
  }

  /* skulls & glitch text container */
  .skulls{
    position:absolute; inset:0; z-index:4; pointer-events:none;
  }
  .skulls .skull{
    position:absolute; font-size:80px; opacity:0.9; filter: drop-shadow(0 0 20px rgba(255,20,20,0.25));
    transform:translate(-50%,-50%) rotate(-10deg);
    animation: float 6s ease-in-out infinite;
  }
  @keyframes float{
    0%{ transform:translate(-50%,-50%) translateY(-8px) rotate(-8deg);}
    50%{ transform:translate(-50%,-50%) translateY(8px) rotate(8deg);}
    100%{ transform:translate(-50%,-50%) translateY(-8px) rotate(-8deg);}
  }

  /* big blinking alert message */
  .bigmsg{
    position:relative; z-index:5; color:var(--red); font-size:46px; font-weight:900; text-transform:uppercase;
    text-shadow:
      0 0 6px rgba(255,0,0,0.9),
      0 0 22px rgba(255,0,0,0.5);
    letter-spacing:2px;
    animation: blink 1s steps(2) infinite;
    pointer-events:none;
  }
  @keyframes blink{ 50%{ opacity:0.06; transform:translateX(6px) skewX(3deg); } }

  /* glitch copies */
  .bigmsg::before, .bigmsg::after{
    content:attr(data-text);
    position:absolute; left:0; top:0; width:100%;
    clip-path: polygon(0 0, 100% 0, 100% 40%, 0 40%);
    opacity:0.85;
  }
  .bigmsg::before{ color:#fff; transform:translate(-6px,-3px); mix-blend-mode:screen; opacity:0.08; }
  .bigmsg::after{ color:var(--red); transform:translate(6px,3px); mix-blend-mode:overlay; opacity:0.06; }

  /* moving red lines */
  .scanline{
    position:absolute; left:0; right:0; height:2px; background:linear-gradient(90deg,transparent,#ff0040,transparent);
    top:30%; opacity:0.25; animation: scan 3s linear infinite;
  }
  @keyframes scan{ 0%{ transform:translateX(-120%);} 100%{ transform:translateX(120%);} }

  /* typing stream smaller messages */
  .stream{
    position:absolute; bottom:8vh; left:4vw; right:4vw; color:#9fffbf; z-index:6; font-size:18px;
    font-weight:700; text-align:left; pointer-events:none;
    text-shadow: 0 0 10px rgba(0,255,160,0.06);
  }

  .red-flood{
    position:absolute; inset:0; background:linear-gradient(180deg, rgba(255,0,0,0.03), rgba(0,0,0,0));
    mix-blend-mode:screen; z-index:2; pointer-events:none;
  }

  /* screen shake */
  .shake{
    animation: shakeAnim .6s cubic-bezier(.36,.07,.19,.97) infinite;
  }
  @keyframes shakeAnim{
    0%{ transform:translate(0,0) }
    10%{ transform:translate(-8px,6px) }
    20%{ transform:translate(6px,-6px) }
    30%{ transform:translate(-6px,6px) }
    40%{ transform:translate(6px,-3px) }
    50%{ transform:translate(-2px,2px) }
    100%{ transform:translate(0,0) }
  }

  /* small skull style customization */
  .skull-svg{
    width:120px; height:120px;
    filter: drop-shadow(0 0 18px rgba(255,0,0,0.25));
    opacity:0.95;
  }

  /* ensure responsive */
  @media (max-width:600px){
    .bigmsg{ font-size:28px; }
    .skulls .skull{ font-size:48px; }
    .sensor{ width:140px; height:100px; }
  }

</style>
</head>
<body>
  <!-- Dashboard (keeps the original vibe) -->
  <div class="card" role="region" aria-label="Dashboard">
    <div class="sensor" aria-hidden="true">
      <div class="label">🌡️ Temp</div>
      <div class="value" id="temp">-- °C</div>
    </div>

    <div class="sensor" aria-hidden="true">
      <div class="label">💧 Humedad</div>
      <div class="value" id="hum">-- %</div>
    </div>

    <div class="center">
      <h2>Descubre Telecomunicaciones</h2>
      <button class="cta" id="scareBtn">Información</button>
    </div>
  </div>

  <div class="footer">ESP8266 Dashboard — Tema Neon</div>

  <!-- Fullscreen Scare Overlay -->
  <div id="scareOverlay" aria-hidden="true">
    <div class="red-flood"></div>
    <div class="skulls" id="skullsContainer"></div>

    <div class="scare-center">
      <div class="bigmsg" id="bigMsg" data-text=""> </div>
      <div class="scanline" id="scan1" style="animation-delay:0s;"></div>
      <div class="scanline" id="scan2" style="top:60%; animation-delay:.6s;"></div>
      <div class="stream" id="streamText"></div>
    </div>
  </div>

<script>
/* ------------------ sensors (simulated) ------------------ */
function fetchSensorData(){
  document.getElementById('temp').textContent = (20 + Math.random()*7).toFixed(1) + ' °C';
  document.getElementById('hum').textContent = (35 + Math.random()*20).toFixed(1) + ' %';
}
setInterval(fetchSensorData, 2500);
fetchSensorData();

/* ------------------ scary overlay logic ------------------ */
const btn = document.getElementById('scareBtn');
const overlay = document.getElementById('scareOverlay');
const skullsContainer = document.getElementById('skullsContainer');
const bigMsg = document.getElementById('bigMsg');
const streamText = document.getElementById('streamText');

let audioCtx;

/* utility: random int */
function rInt(min,max){ return Math.floor(Math.random()*(max-min+1))+min; }

/* Build several floating skull elements (emoji + SVG fallback) */
function spawnSkulls(count=8){
  skullsContainer.innerHTML='';
  for(let i=0;i<count;i++){
    const el = document.createElement('div');
    el.className='skull';
    // choose emoji or small SVG skull for variety
    if(Math.random() > 0.35){
      el.textContent = '💀'; // emoji skull
      el.style.fontSize = rInt(56,140) + 'px';
    } else {
      // small SVG skull (inline)
      el.innerHTML = `<svg class="skull-svg" viewBox="0 0 64 64" xmlns="http://www.w3.org/2000/svg">
        <g fill="#fff">
          <path d="M32 2C18 2 6 12 6 24c0 6 3 11 8 14v6c0 4 4 8 8 8h8v-4h4v4h8c4 0 8-4 8-8v-6c5-3 8-8 8-14 0-12-12-22-26-22z" fill="#fff"/>
          <circle cx="22" cy="26" r="4" fill="#000"/>
          <circle cx="42" cy="26" r="4" fill="#000"/>
          <path d="M22 42c3 2 10 2 14 0" stroke="#000" stroke-width="2" fill="none" stroke-linecap="round"/>
        </g></svg>`;
      el.style.opacity = 0.95;
    }
    // random position
    el.style.left = (Math.random()*100) + '%';
    el.style.top = (Math.random()*100) + '%';
    el.style.transform = `translate(-50%,-50%) rotate(${rInt(-25,25)}deg)`;
    skullsContainer.appendChild(el);
  }
}

/* Create the streaming random "hacker" lines (customizable text list) */
const streamPhrases = [
  "ACCESO NO AUTORIZADO DETECTADO",
  "SISTEMA COMPROMETIDO",
  "INTRUSIÓN EN PROCESO",
  "ENCRIPTANDO ARCHIVOS",
  "RUTA DE RED INSEGURA",
  "INTERRUPCIÓN DE SERVICIOS",
  "ELIMINANDO BACKUPS",
  "RESPUESTA: 0xDEAD"
];

function startStream(){
  // rapidly add phrases to the stream area
  streamText.innerHTML = '';
  let idx=0;
  const timer = setInterval(()=>{
    const p = document.createElement('div');
    p.textContent = streamPhrases[Math.floor(Math.random()*streamPhrases.length)];
    p.style.opacity = 0; p.style.transform = 'translateY(8px)';
    streamText.prepend(p);
    // animate in
    requestAnimationFrame(()=>{ p.style.transition='all .18s'; p.style.opacity=1; p.style.transform='translateY(0)'; });
    // limit lines
    if(streamText.children.length > 8) streamText.removeChild(streamText.lastChild);
    idx++;
    if(idx>40) clearInterval(timer);
  }, 150);
}

/* big pulsating messages sequence */
const bigMessages = [
  "ESTÁS SIENDO HACKEADO",
  "NO RESPONDE",
  "SISTEMA FALLANDO",
  "CONEXIÓN ROTA"
];

function playSequence(){
  let i=0;
  bigMsg.dataset.text = bigMessages[0];
  bigMsg.textContent = bigMessages[0];
  bigMsg.classList.add('shake');
  const seq = setInterval(()=>{
    i++;
    if(i < bigMessages.length){
      bigMsg.dataset.text = bigMessages[i];
      bigMsg.textContent = bigMessages[i];
      // occasional heavy shake
      if(i%2===0) bigMsg.classList.add('shake');
      else bigMsg.classList.remove('shake');
    } else {
      clearInterval(seq);
      // after messages, keep repeating with flicker
      setInterval(()=>{
        bigMsg.style.opacity = (Math.random()>0.5?1:0.06);
        bigMsg.style.transform = `translateX(${rInt(-6,6)}px) skewX(${rInt(-3,3)}deg)`;
      }, 220);
    }
  }, 1100);
}

/* SOUND: create creepy rumble + glitch beeps via WebAudio */
function startSound(){
  try{
    audioCtx = new (window.AudioContext || window.webkitAudioContext)();
    // low rumble
    const osc = audioCtx.createOscillator();
    const gain = audioCtx.createGain();
    osc.type = 'sine'; osc.frequency.value = 40 + Math.random()*30; // very low
    gain.gain.value = 0.0015; // subtle
    osc.connect(gain);
    gain.connect(audioCtx.destination);
    osc.start();

    // slowly modulate the low freq for tension
    const lfo = audioCtx.createOscillator();
    const lfoGain = audioCtx.createGain();
    lfo.frequency.value = 0.08;
    lfoGain.gain.value = 20;
    lfo.connect(lfoGain);
    lfoGain.connect(osc.frequency);
    lfo.start();

    // glitch beeps cluster
    function glitchBeep(){
      const o = audioCtx.createOscillator();
      const g = audioCtx.createGain();
      o.type = 'square';
      o.frequency.value = 600 + Math.random()*1200;
      g.gain.value = 0.0015 + Math.random()*0.0025;
      o.connect(g); g.connect(audioCtx.destination);
      o.start();
      // short decay
      g.gain.setValueAtTime(g.gain.value, audioCtx.currentTime);
      g.gain.exponentialRampToValueAtTime(0.00001, audioCtx.currentTime + 0.12 + Math.random()*0.25);
      setTimeout(()=>{ o.stop(); o.disconnect(); g.disconnect(); }, 400);
    }
    // schedule random beeps
    const beepInt = setInterval(()=>{ glitchBeep(); }, 250 + Math.random()*600);

    // stop after some time to avoid infinite noise (you can remove limit if you want)
    setTimeout(()=>{ clearInterval(beepInt); }, 25000);

    // return function to stop base oscillators
    return ()=>{ osc.stop(); lfo.stop(); try{ audioCtx.close(); }catch(e){} };
  }catch(e){
    console.warn('Audio failed',e);
    return ()=>{};
  }
}

/* FULLSCREEN mode request (improve immersion) */
async function enterFullScreen(el){
  try{
    if(el.requestFullscreen) await el.requestFullscreen();
    else if(el.webkitRequestFullscreen) await el.webkitRequestFullscreen();
    else if(el.msRequestFullscreen) await el.msRequestFullscreen();
  }catch(e){ console.log('fullscreen blocked',e); }
}

/* main trigger */
btn.addEventListener('click', async function(){
  // hide dashboard visuals subtly
  document.querySelector('.card').style.opacity = 0.06;
  // spawn skulls and show overlay
  spawnSkulls(12);
  overlay.style.display = 'flex';
  overlay.classList.add('shake');

  // set initial big message
  bigMsg.dataset.text = bigMessages[0];
  bigMsg.textContent = bigMessages[0];

  // start visuals
  startStream();
  playSequence();

  // try to go fullscreen
  await enterFullScreen(document.documentElement);

  // start sound (returns stopper)
  const stopAudio = startSound();

  // progressively increase terror: add random extra skulls, flashes
  let pulses = 0;
  const pulseInt = setInterval(()=>{
    pulses++;
    // flash background red for a beat
    overlay.style.background = (pulses%2===0)?'radial-gradient(circle at 50% 40%, rgba(255,0,0,0.06), rgba(0,0,0,0.95))':'#000';
    // add small new skull occasionally
    if(Math.random()>0.6) spawnSkulls(8 + rInt(0,6));
    if(pulses>40){
      clearInterval(pulseInt);
      // restore dashboard after a while (clean up)
      setTimeout(()=>{
        overlay.style.display='none';
        document.querySelector('.card').style.opacity = 1;
        try{ if(document.fullscreenElement) document.exitFullscreen(); }catch(e){}
        stopAudio();
      }, 24000); // overlay lasts ~24s total
    }
  }, 600);
});

</script>
</body>
</html>
