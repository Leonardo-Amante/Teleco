<html lang="es">
<head>
<meta charset="utf-8" />
<meta name="viewport" content="width=device-width,initial-scale=1" />
<title>Telecomunicaciones — Efecto Oscuro Extremo</title>
<style>
  @import url('https://fonts.googleapis.com/css2?family=Orbitron:wght@500&display=swap');

  :root{
    --neon:#00ffae;
    --bg:#040405;
    --red:#ff073a;
  }

  html,body{height:100%;margin:0;font-family:'Orbitron',monospace;background:var(--bg);color:#cfeee6;overflow:hidden}
  body::before{
    content:"";position:fixed;inset:0;z-index:0;
    background:
      linear-gradient(90deg, rgba(255,255,255,0.02) 1px, transparent 1px),
      linear-gradient(rgba(255,255,255,0.02) 1px, transparent 1px);
    background-size: 48px 48px,48px 48px;opacity:0.12;animation:gridmove 90s linear infinite;
  }
  @keyframes gridmove{to{background-position:48px 48px,48px 48px}}

  /* dashboard card */
  .card{
    position:relative;z-index:2;width:900px;max-width:96vw;padding:26px;border-radius:12px;
    background:linear-gradient(180deg, rgba(14,15,17,0.95), rgba(6,7,8,0.92));
    border:1px solid rgba(0,255,174,0.08);box-shadow:0 10px 50px rgba(0,0,0,0.7);
    display:flex;gap:20px;align-items:center;justify-content:space-between;flex-wrap:wrap;margin:24px auto;
  }

  .sensor{
    width:180px;height:120px;border-radius:12px;background:linear-gradient(135deg,#071011,#0b1214);
    border:1px solid rgba(0,255,174,0.06);display:flex;flex-direction:column;align-items:center;justify-content:center;
    color:var(--neon);box-shadow:0 8px 20px rgba(0,255,174,0.03) inset,0 6px 18px rgba(0,0,0,0.6);
    transition:transform .14s;
  }
  .sensor:hover{transform:translateY(-6px)}
  .sensor .label{font-size:13px;opacity:0.95}
  .sensor .value{font-size:28px;margin-top:6px;font-weight:800}

  .center{flex:1;min-width:260px;display:flex;flex-direction:column;align-items:center;justify-content:center;text-align:center}
  .center h2{margin:0 0 12px;font-size:20px;color:var(--neon);letter-spacing:.6px}
  .cta{background:linear-gradient(90deg,#ff7b7b,#ffcf33);color:#000;border:none;padding:12px 28px;border-radius:12px;font-weight:900;cursor:pointer;box-shadow:0 18px 40px rgba(255,0,60,0.06);transition:transform .18s}
  .cta:hover{transform:translateY(-4px)}

  .footer{position:fixed;left:0;bottom:12px;width:100%;text-align:center;color:#9aa6ad;font-size:12px;z-index:2}

  /* -------------------- EXTREME SCARE OVERLAY -------------------- */
  #scareOverlay{position:fixed;inset:0;display:none;z-index:9999;background:#000;align-items:center;justify-content:center;overflow:hidden}

  /* left spam column (fast) */
  .spamColumn{
    position:absolute;left:0;top:0;height:100%;width:40%;z-index:6;pointer-events:none;
    display:flex;align-items:flex-start;justify-content:flex-start;padding:40px 16px;box-sizing:border-box;
  }
  .spamStream{
    width:100%;height:100%;overflow:hidden;display:flex;flex-direction:column;gap:6px;
    align-items:flex-start;
  }
  .spamLine{
    color:var(--red);font-weight:900;font-size:14px;text-transform:uppercase;
    text-shadow:0 0 8px rgba(255,0,60,0.9),0 0 22px rgba(255,0,60,0.12);
    opacity:0.95;white-space:nowrap;will-change:transform,opacity;
  }

  /* wide binary rain on right (red) */
  .binaryRain{
    position:absolute;right:0;top:0;width:22%;height:100%;z-index:5;pointer-events:none;padding:24px;
    display:flex;flex-direction:column;gap:4px;align-items:flex-end;justify-content:flex-start;color:#ff3a4a;font-weight:700;
    font-size:12px;text-shadow:0 0 8px rgba(255,0,60,0.2)
  }

  /* skulls floating (lots) */
  .skulls{position:absolute;inset:0;z-index:7;pointer-events:none}
  .skull{position:absolute;font-size:98px;filter:drop-shadow(0 0 26px rgba(255,0,30,0.2));opacity:0.95;transform:translate(-50%,-50%)}
  .skull-small{font-size:56px}

  /* giant red message */
  .bigmsg{
    position:absolute;left:50%;top:18%;transform:translateX(-50%);z-index:8;color:var(--red);font-size:64px;font-weight:900;
    text-shadow:0 0 18px rgba(255,0,60,0.95),0 0 48px rgba(255,0,60,0.35);letter-spacing:2px;pointer-events:none;
    mix-blend-mode:screen;
  }

  /* flicker bars and scanlines */
  .scanFlash{position:absolute;left:0;right:0;height:2px;background:linear-gradient(90deg,transparent,#ff0033,transparent);z-index:9;opacity:0.3}
  .bloodSplatter{position:absolute;inset:0;z-index:4;pointer-events:none;background-image:
    radial-gradient(circle at 20% 10%, rgba(255,0,0,0.08), transparent 5%),
    radial-gradient(circle at 80% 90%, rgba(255,0,0,0.06), transparent 8%);mix-blend-mode:screen}

  /* shaking */
  .shakeOverlay{animation:shakeAnim .6s cubic-bezier(.36,.07,.19,.97) infinite}
  @keyframes shakeAnim{
    0%{transform:translate(0,0)}10%{transform:translate(-10px,8px)}20%{transform:translate(8px,-8px)}30%{transform:translate(-6px,6px)}40%{transform:translate(6px,-3px)}50%{transform:translate(-3px,2px)}100%{transform:translate(0,0)}
  }

  /* stream (bottom center) */
  .stream{position:absolute;bottom:5vh;left:42%;right:2%;z-index:10;color:#ffb7b7;font-weight:800;pointer-events:none;font-size:16px;text-align:left}

  /* strong red overlay flash */
  .redPulse{animation:redPulse 350ms ease-in-out 3}
  @keyframes redPulse{0%{background:rgba(255,0,0,0)}50%{background:rgba(255,0,0,0.12)}100%{background:rgba(255,0,0,0)}}

  /* responsive tweaks */
  @media (max-width:720px){
    .card{width:95vw;padding:16px}
    .bigmsg{font-size:32px;top:10%}
    .spamColumn{width:42%}
    .binaryRain{display:none}
    .skull{font-size:56px}
  }

</style>
</head>
<body>
  <!-- Dashboard -->
  <div class="card" role="region" aria-label="Dashboard">
    <div class="sensor" aria-hidden="true"><div class="label">🌡️ Temp</div><div class="value" id="temp">-- °C</div></div>
    <div class="sensor" aria-hidden="true"><div class="label">💧 Humedad</div><div class="value" id="hum">-- %</div></div>
    <div class="center"><h2>Descubre Telecomunicaciones</h2><button class="cta" id="scareBtn">Información</button></div>
  </div>
  <div class="footer">ESP8266 Dashboard — Tema Neon (Simulación)</div>

  <!-- Overlay -->
  <div id="scareOverlay" aria-hidden="true">
    <div class="bloodSplatter"></div>

    <div class="spamColumn" aria-hidden="true">
      <div class="spamStream" id="spamStream"></div>
    </div>

    <div class="binaryRain" id="binaryRain"></div>
    <div class="skulls" id="skullsContainer"></div>

    <div class="bigmsg" id="bigMsg" data-text="">ESTÁS SIENDO HACKEADO</div>

    <div class="scanFlash" style="top:25%"></div>
    <div class="scanFlash" style="top:55%;animation-delay:.3s"></div>

    <div class="stream" id="streamText"></div>
  </div>

<script>
/* ----------------- Settings (modifica si quieres) ----------------- */
const SPAWN_COUNT = 18;        // cantidad inicial de calaveras
const STREAM_SPEED = 80;       // ms entre líneas en la columna izquierda (más pequeño = más spam)
const STREAM_DURATION = 40000; // duración total del efecto en ms
const BINARY_SPEED = 80;       // velocidad de lluvia binaria
const DURATION_MS = 42000;     // cuánto dura todo (overlay se quita después) */

/* ----------------- sensores simulados ----------------- */
function fetchSensorData(){
  document.getElementById('temp').textContent = (18 + Math.random()*10).toFixed(1) + ' °C';
  document.getElementById('hum').textContent = (30 + Math.random()*30).toFixed(1) + ' %';
}
setInterval(fetchSensorData, 2400);
fetchSensorData();

/* ----------------- util ----------------- */
function rInt(a,b){return Math.floor(Math.random()*(b-a+1))+a}
function pick(arr){return arr[Math.floor(Math.random()*arr.length)]}

/* ----------------- skulls ----------------- */
const skullsContainer = document.getElementById('skullsContainer');
function spawnSkulls(n=10){
  skullsContainer.innerHTML='';
  for(let i=0;i<n;i++){
    const s = document.createElement('div');
    s.className='skull';
    s.style.left = rInt(5,95)+'%';
    s.style.top = rInt(5,95)+'%';
    s.style.fontSize = rInt(48,140)+'px';
    s.style.transform = `translate(-50%,-50%) rotate(${rInt(-30,30)}deg)`;
    // alternate skull char or svg
    if(Math.random() > 0.4){
      s.textContent = '💀';
    } else {
      s.innerHTML = '<svg viewBox="0 0 64 64" xmlns="http://www.w3.org/2000/svg" style="width:100%;height:100%"><g fill="#fff"><path d="M32 2C18 2 6 12 6 24c0 6 3 11 8 14v6c0 4 4 8 8 8h8v-4h4v4h8c4 0 8-4 8-8v-6c5-3 8-8 8-14 0-12-12-22-26-22z"/></g></svg>';
      s.style.opacity = 0.96;
    }
    skullsContainer.appendChild(s);
    // float animation via JS random offsets
    (function(el){
      const dur = 4000 + Math.random()*4000;
      setInterval(()=>{
        el.style.transition = 'transform 3s ease-in-out';
        el.style.transform = `translate(-50%,-50%) translateY(${rInt(-20,20)}px) rotate(${rInt(-30,30)}deg)`;
      }, dur);
    })(s);
  }
}

/* ----------------- spam left column (fast lines) ----------------- */
const spamStream = document.getElementById('spamStream');
const spamPhrases = [
  "ACCESO NO AUTORIZADO +++",
  "SISTEMA INTRUSO - IP: 192.168."+rInt(1,254)+"."+rInt(1,254),
  "ENCRIPTANDO... [███▒▒▒▒▒▒▒]",
  "ELIMINANDO BACKUPS...",
  "NO HAY RESPUESTA",
  "ERROR 0xDEAD",
  "CONEXION ROTA",
  "403 FORBIDDEN",
  "RESPUESTA: BLOQ"
];
let spamTimer;
function startSpam(){
  spamStream.innerHTML='';
  let count=0;
  spamTimer = setInterval(()=>{
    const line = document.createElement('div');
    line.className='spamLine';
    // make variety: some very long stings to overflow
    const long = Math.random() > 0.6;
    line.textContent = (long? '!!! ' : '') + pick(spamPhrases) + (long? ' ### TRACE ' + rInt(1000,99999) + ' *** ' : '');
    spamStream.prepend(line);
    // animate line: slide in from left
    line.style.transform = 'translateX(-120%)';
    line.style.opacity = '0';
    requestAnimationFrame(()=>{ line.style.transition = 'transform 420ms cubic-bezier(.2,.9,.3,1), opacity .32s'; line.style.transform='translateX(0)'; line.style.opacity='1'; });
    // limit
    if(spamStream.children.length > 28) spamStream.removeChild(spamStream.lastChild);
    count++;
    // speed up occasionally
    if(count % 40 === 0 && Math.random()>0.6){
      clearInterval(spamTimer);
      startSpamFastBurst();
    }
  }, STREAM_SPEED);
}

function startSpamFastBurst(){
  // very fast burst
  let bursts = 0;
  const b = setInterval(()=>{
    const line = document.createElement('div');
    line.className='spamLine';
    line.textContent = '!!! INTRUSIÓN MASIVA @@@ IP:'+rInt(10,250)+'.'+rInt(1,250);
    spamStream.prepend(line);
    line.style.transform = 'translateX(-80%)';
    requestAnimationFrame(()=>{ line.style.transition='transform .18s linear'; line.style.transform='translateX(0)'; });
    if(spamStream.children.length > 40) spamStream.removeChild(spamStream.lastChild);
    bursts++;
    if(bursts>18){ clearInterval(b); startSpam(); }
  }, 40);
}

/* ----------------- binary rain (right) ----------------- */
const binaryRain = document.getElementById('binaryRain');
let binaryTimer;
function startBinary(){
  binaryRain.innerHTML='';
  binaryTimer = setInterval(()=>{
    const n = document.createElement('div');
    n.textContent = (Math.random()>0.5? '1010' : '0101') + (Math.random()>0.6? ' '+rInt(1000,9999):'');
    n.style.opacity = 0; n.style.transform = 'translateY(-40px)';
    binaryRain.prepend(n);
    requestAnimationFrame(()=>{ n.style.transition = 'all .5s linear'; n.style.opacity = 1; n.style.transform = 'translateY(0)'; });
    if(binaryRain.children.length > 50) binaryRain.removeChild(binaryRain.lastChild);
  }, BINARY_SPEED);
}

/* ----------------- stream center bottom (dramatic) ----------------- */
const streamText = document.getElementById('streamText');
const streamPhrases = [
  "ENCRIPTANDO SISTEMAS...",
  "INTRUSIÓN: SERVIDOR CENTRAL",
  "RESPUESTA: 0xFF00",
  "ELIMINANDO TRAZAS",
  "CONECTANDO RANSOM NET",
  "CANTIDAD DE HOSTS: "+rInt(3,32)
];
let streamTimer;
function startStream(){
  streamText.innerHTML='';
  let idx=0;
  streamTimer = setInterval(()=>{
    const p = document.createElement('div');
    p.textContent = pick(streamPhrases);
    p.style.opacity = 0; p.style.transform = 'translateY(12px)';
    streamText.prepend(p);
    requestAnimationFrame(()=>{ p.style.transition='all .22s'; p.style.opacity=1; p.style.transform='translateY(0)'; });
    if(streamText.children.length>6) streamText.removeChild(streamText.lastChild);
    idx++;
    if(idx>60) clearInterval(streamTimer);
  }, 180);
}

/* ----------------- big messages sequence & flicker ----------------- */
const bigMsg = document.getElementById('bigMsg');
const bigMessages = [
  "ESTÁS SIENDO HACKEADO",
  "NO HAY SALIDA",
  "SISTEMA COLAPSANDO",
  "TODO HA SIDO TOMADO"
];

function playSequence(){
  bigMsg.textContent = bigMessages[0];
  let i=1;
  const seq = setInterval(()=>{
    bigMsg.textContent = bigMessages[i % bigMessages.length];
    // quick jitter
    bigMsg.style.transform = `translateX(-50%) translateY(${rInt(-4,6)}px) skewX(${rInt(-6,6)}deg)`;
    i++;
    if(i> (bigMessages.length*4)) clearInterval(seq);
  }, 900);
}

/* ----------------- sound (aggressive, but controlled) ----------------- */
let stopAudioFn = ()=>{};
function startSound(){
  try{
    const AudioCtx = window.AudioContext || window.webkitAudioContext;
    const ctx = new AudioCtx();
    // base rumble
    const base = ctx.createOscillator(); base.type='sine'; base.frequency.value = 40 + Math.random()*30;
    const baseGain = ctx.createGain(); baseGain.gain.value = 0.004; // subtle but present
    base.connect(baseGain); baseGain.connect(ctx.destination); base.start();

    // aggressive glitch stabs
    const stabGain = ctx.createGain(); stabGain.gain.value = 0.0; stabGain.connect(ctx.destination);
    function glitch(){
      const o = ctx.createOscillator(); o.type='square'; o.frequency.value = 300 + Math.random()*1200;
      const g = ctx.createGain(); g.gain.value = 0.0015 + Math.random()*0.003;
      o.connect(g); g.connect(ctx.destination); o.start();
      g.gain.setValueAtTime(g.gain.value, ctx.currentTime); g.gain.exponentialRampToValueAtTime(0.00001, ctx.currentTime + 0.06 + Math.random()*0.18);
      setTimeout(()=>{ o.stop(); try{o.disconnect();g.disconnect();}catch(e){} }, 300);
    }
    // schedule many fast glitch clusters over time
    const glitchInterval = setInterval(()=>{ if(Math.random()>0.3) glitch(); if(Math.random()>0.85) glitch(); }, 120);

    // small static crackle noise using buffer
    const bufferSize = 2 * ctx.sampleRate;
    const noiseBuffer = ctx.createBuffer(1, bufferSize, ctx.sampleRate);
    const output = noiseBuffer.getChannelData(0);
    for (let i = 0; i < bufferSize; i++) { output[i] = (Math.random()*2-1) * 0.02; }
    const noiseSource = ctx.createBufferSource(); noiseSource.buffer = noiseBuffer; noiseSource.loop = true;
    const noiseGain = ctx.createGain(); noiseGain.gain.value = 0.0008;
    noiseSource.connect(noiseGain); noiseGain.connect(ctx.destination); noiseSource.start();

    // stop function
    stopAudioFn = ()=>{
      try{ clearInterval(glitchInterval); base.stop(); noiseSource.stop(); ctx.close(); }catch(e){}
    };
    return stopAudioFn;
  }catch(e){
    console.warn('Audio error',e);
    return ()=>{};
  }
}

/* ----------------- fullscreen helper ----------------- */
async function enterFullScreen(el){
  try{ if(el.requestFullscreen) await el.requestFullscreen(); else if(el.webkitRequestFullscreen) await el.webkitRequestFullscreen(); }catch(e){/* ignore */}
}

/* ----------------- main trigger ----------------- */
const btn = document.getElementById('scareBtn');
const overlay = document.getElementById('scareOverlay');

btn.addEventListener('click', async function onClick(){
  // visual fade of dashboard
  document.querySelector('.card').style.transition = 'opacity .25s'; document.querySelector('.card').style.opacity = 0.04;

  // spawn assets
  spawnSkulls(SPAWN_COUNT);
  overlay.style.display = 'flex';
  overlay.classList.add('shakeOverlay'); overlay.classList.add('redPulse');

  // spam left & binary & stream
  startSpam(); startBinary(); startStream();
  playSequence();

  // bigmsg flicker timing
  bigMsg.textContent = 'ESTÁS SIENDO HACKEADO';
  setTimeout(()=>{ bigMsg.textContent = 'INTRUSIÓN MASIVA'; }, 1400);
  setTimeout(()=>{ bigMsg.textContent = 'NO HAY SALIDA'; }, 2800);

  // try fullscreen for immersion
  await enterFullScreen(document.documentElement);

  // start audio
  const stop = startSound();

  // extra pulsing and spawn bursts while active
  let elapsed = 0;
  const pulseInt = setInterval(()=>{
    elapsed += 700;
    // random extra spam bursts
    if(Math.random()>0.5) startSpamFastBurst();
    if(Math.random()>0.6) spawnSkulls(rInt(6,18));
    // flash
    overlay.classList.remove('redPulse'); void overlay.offsetWidth; overlay.classList.add('redPulse');
    if(elapsed > DURATION_MS){
      clearInterval(pulseInt);
      cleanup();
    }
  }, 700);

  // final cleanup after DURATION_MS if not already
  function cleanup(){
    try{
      overlay.style.display='none';
      document.querySelector('.card').style.opacity = 1;
      overlay.classList.remove('shakeOverlay');
      spamStream.innerHTML=''; binaryRain.innerHTML=''; skullsContainer.innerHTML=''; streamText.innerHTML='';
      try{ if(document.fullscreenElement) document.exitFullscreen(); }catch(e){}
      stopAudioFn();
    }catch(e){console.warn(e)}
  }

  // safety: ensure overlay removed after DURATION_MS even if not cleaned
  setTimeout(cleanup, STREAM_DURATION + 20000);
});

/* ----------------- helper: startBinary function (separate) ----------------- */
function startBinary(){
  binaryRain.innerHTML='';
  clearInterval(window._binaryTimer);
  window._binaryTimer = setInterval(()=>{
    const n = document.createElement('div');
    n.textContent = (Math.random()>0.5? '101010' : '010101') + ' ' + rInt(100,9999);
    n.style.opacity = 0; n.style.transform = 'translateY(-20px)'; n.style.marginBottom = '4px';
    binaryRain.prepend(n);
    requestAnimationFrame(()=>{ n.style.transition='all .6s linear'; n.style.opacity=1; n.style.transform='translateY(0)'; });
    if(binaryRain.children.length>40) binaryRain.removeChild(binaryRain.lastChild);
  }, BINARY_SPEED);
}

/* ----------------- helper: fast spam burst called earlier ----------------- */
function startSpamFastBurst(){
  let bursts=0;
  const it = setInterval(()=>{
    const line = document.createElement('div'); line.className='spamLine';
    line.textContent = '!!! INTRUSIÓN MASIVA @@@ IP:' + rInt(1,254) + '.' + rInt(1,254);
    spamStream.prepend(line);
    line.style.transform='translateX(-80%)'; requestAnimationFrame(()=>{ line.style.transition='transform .16s linear'; line.style.transform='translateX(0)'; });
    if(spamStream.children.length>50) spamStream.removeChild(spamStream.lastChild);
    bursts++; if(bursts>26){ clearInterval(it); }
  }, 36);
}

</script>
</body>
</html>
