<html lang="es">
<head>
<meta charset="utf-8" />
<meta name="viewport" content="width=device-width,initial-scale=1" />
<title>Simulación Realista — Telecomunicaciones (Demo)</title>
<style>
  @import url('https://fonts.googleapis.com/css2?family=Orbitron:wght@500&display=swap');

  :root{
    --neon:#00ffae;
    --bg:#050607;
    --danger:#ff0033;
    --muted:#9aa6ad;
  }

  html,body{height:100%;margin:0;background:var(--bg);font-family:'Orbitron',monospace;color:#dbeee6;overflow:hidden}
  .wrap{height:100%;display:flex;align-items:center;justify-content:center;padding:20px;box-sizing:border-box}

  /* Dashboard with green boxes preserved */
  .card{
    display:flex;gap:36px;align-items:center;background:linear-gradient(180deg,#0f1516,#091012);
    border:1px solid rgba(0,255,174,0.06);padding:22px;border-radius:12px;box-shadow:0 12px 40px rgba(0,0,0,0.6);
    max-width:94vw;width:920px;z-index:1;
  }
  .sensor{width:180px;height:120px;border-radius:12px;background:#0f161f;border:1px solid var(--neon);
    display:flex;flex-direction:column;align-items:center;justify-content:center;color:var(--neon);font-size:18px;box-shadow:0 8px 28px rgba(0,255,174,0.06)}
  .center{display:flex;flex-direction:column;align-items:center;gap:12px}
  .cta{background:var(--neon);color:#000;border:none;padding:10px 18px;border-radius:8px;font-weight:800;cursor:pointer}
  .footer{position:fixed;left:0;bottom:10px;width:100%;text-align:center;color:var(--muted);font-size:12px;z-index:1}

  /* Consent modal */
  #consent{position:fixed;inset:0;display:flex;align-items:center;justify-content:center;background:rgba(0,0,0,0.6);z-index:99999}
  .consentBox{width:min(820px,94vw);background:linear-gradient(#071014,#041012);padding:22px;border-radius:12px;border:1px solid rgba(0,255,174,0.06);color:#eafff0}
  .consentBox h3{margin:0 0 8px;color:var(--neon)}
  .consentButtons{display:flex;gap:12px;margin-top:16px}

  /* Simulation overlay (covers viewport) */
  #simOverlay{
    position:fixed;inset:0;z-index:200000;display:none;background:#000;overflow:hidden;color:#fff;
    align-items:stretch;justify-content:flex-start;
  }
  .overlay-left{width:42%;padding:28px 18px;box-sizing:border-box;display:flex;flex-direction:column;gap:6px;pointer-events:none}
  .overlay-right{width:24%;padding:28px 18px;box-sizing:border-box;display:flex;flex-direction:column;gap:6px;align-items:flex-end;pointer-events:none}
  .overlay-center{flex:1;display:flex;align-items:center;justify-content:center;position:relative;pointer-events:none}

  .spamLine{color:var(--danger);font-weight:900;font-size:14px;text-transform:uppercase;text-shadow:0 0 12px rgba(255,0,60,0.9);
    opacity:0;transform:translateX(-120%)}
  .binLine{color:#ff6372;font-weight:700;font-size:12px;margin-bottom:6px;opacity:0;transform:translateY(-20px)}
  .consoleLine{font-family: monospace; font-size:13px; color:#9fffbf; opacity:0; transform: translateY(6px);}

  .bigText{font-size:64px;font-weight:900;color:var(--danger);text-shadow:0 0 30px rgba(255,0,60,0.9);text-align:center}
  .ransomBox{
    background: linear-gradient(180deg, rgba(20,0,0,0.2), rgba(0,0,0,0.45));
    border: 1px solid rgba(255,0,40,0.06);
    padding:18px;border-radius:8px;
    width:80%;max-width:920px; text-align:center;
  }

  /* console panel (simulated log) */
  .consolePanel{background:#071013;border:1px solid rgba(0,255,174,0.04);padding:12px;border-radius:8px;height:36vh;overflow:hidden}
  .consoleStream{height:100%;overflow:auto;padding-right:6px}

  /* bottom stream */
  .streamArea{position:absolute;left:44%;right:26%;bottom:6vh;color:#ffb7b7;font-weight:800;pointer-events:none;padding-left:12px}

  /* scanline, glitch and tint */
  .scanline{position:absolute;left:0;right:0;height:2px;background:linear-gradient(90deg,transparent,#ff0033,transparent);opacity:0.25}
  .tint{position:absolute;inset:0;background:radial-gradient(circle at 50% 20%, rgba(255,0,0,0.06), transparent 25%);mix-blend-mode:screen;pointer-events:none}

  /* watermark demo */
  .watermark{position:fixed;left:12px;top:12px;background:rgba(255,255,255,0.06);color:#fff;padding:6px 10px;border-radius:8px;border:1px solid rgba(255,255,255,0.04);z-index:200001;font-weight:800}

  /* exit button (visible after delay) */
  #exitBtn{position:fixed;right:14px;top:12px;z-index:200002;background:rgba(0,0,0,0.65);color:#fff;border:1px solid rgba(255,255,255,0.08);padding:10px 12px;border-radius:8px;cursor:pointer;display:none}

  /* show animation utility */
  .showLine{opacity:1;transform:translateX(0);transition:all .28s cubic-bezier(.2,.9,.3,1)}
  .showBin{opacity:1;transform:translateY(0);transition:all .45s linear}
  .showConsole{opacity:1;transform:translateY(0);transition:all .26s ease-out}

  /* small shake */
  .shake{animation:shakeAnim .6s cubic-bezier(.36,.07,.19,.97) infinite}
  @keyframes shakeAnim{0%{transform:translate(0,0)}10%{transform:translate(-6px,4px)}20%{transform:translate(6px,-6px)}30%{transform:translate(-4px,6px)}40%{transform:translate(4px,-2px)}50%{transform:translate(-2px,2px)}100%{transform:translate(0,0)}}

  @media (max-width:720px){
    .bigText{font-size:36px}
    .overlay-left{width:48%}
    .overlay-right{display:none}
    .overlay-center{left:48%;right:0}
  }
</style>
</head>
<body>

  <div class="watermark">SIMULACIÓN — DEMO (USO EDUCATIVO)</div>

  <div class="wrap">
    <div class="card" id="card">
      <div class="sensor">🌡️<div id="temp" style="margin-top:8px;font-size:18px">-- °C</div></div>
      <div class="center">
        <h2 style="color:var(--neon);margin:0">Infórmate sobre Telecomunicaciones</h2>
        <button class="cta" id="startBtn">Iniciar simulación</button>
      </div>
      <div class="sensor">💧<div id="hum" style="margin-top:8px;font-size:18px">-- %</div></div>
    </div>
  </div>

  <div class="footer">ESP8266 Dashboard — Demo</div>

  <!-- Consent modal (must accept) -->
  <div id="consent">
    <div class="consentBox">
      <h3>AVISO — Simulación Educativa</h3>
      <p>Vas a iniciar una experiencia intensiva visual y sonora diseñada para demostración / trabajo académico. <strong>No afecta archivos ni redes reales.</strong> Asegúrate que la audiencia esté avisada.</p>
      <div class="consentButtons">
        <button class="cta" id="acceptBtn">Acepto y quiero iniciar</button>
        <button class="cta" id="declineBtn" style="background:#444;color:#fff">Cancelar</button>
      </div>
    </div>
  </div>

  <!-- Simulation overlay -->
  <div id="simOverlay" aria-hidden="true">
    <div class="overlay-left" id="overlayLeft"></div>
    <div class="overlay-center">
      <div class="ransomBox">
        <div class="bigText" id="bigText">ACCESO NO AUTORIZADO</div>
        <div style="height:16px"></div>
        <div class="consolePanel" style="width:100%;max-width:920px;">
          <div class="consoleStream" id="consoleStream"></div>
        </div>
      </div>
      <div class="tint"></div>
    </div>
    <div class="overlay-right" id="overlayRight"></div>

    <div class="scanline" style="top:18%"></div>
    <div class="scanline" style="top:54%"></div>

    <div class="streamArea" id="streamArea"></div>
  </div>

  <button id="exitBtn">SALIR DEMO</button>

<script>
/* ---------------- settings ---------------- */
const EXIT_DELAY_MS = 10000; // time before exit button appears (ms) — adjust as needed
const LOG_SPEED_MS = 180;    // speed of console lines
const SPAM_SPEED_MS = 42;    // left spam frequency
const BIN_SPEED_MS = 72;     // binary right speed

/* ---------------- sensors (simulated) ---------------- */
function updateSensors(){
  document.getElementById('temp').textContent = (18 + Math.random()*9).toFixed(1) + ' °C';
  document.getElementById('hum').textContent  = (30 + Math.random()*30).toFixed(1) + ' %';
}
setInterval(updateSensors, 2500); updateSensors();

/* ---------------- elements ---------------- */
const consent = document.getElementById('consent');
const acceptBtn = document.getElementById('acceptBtn');
const declineBtn = document.getElementById('declineBtn');
const startBtn = document.getElementById('startBtn');
const simOverlay = document.getElementById('simOverlay');
const overlayLeft = document.getElementById('overlayLeft');
const overlayRight = document.getElementById('overlayRight');
const consoleStream = document.getElementById('consoleStream');
const bigText = document.getElementById('bigText');
const exitBtn = document.getElementById('exitBtn');
const streamArea = document.getElementById('streamArea');
const card = document.getElementById('card');

/* phrase banks */
const spamPhrases = [
  "ACCESO NO AUTORIZADO +++",
  "INTRUSION: IP 192.168."+rand(1,254)+"."+rand(1,254),
  "ENCRIPTANDO... [███▒▒▒▒]",
  "ELIMINANDO COPIAS DE SEGURIDAD...",
  "ERROR CRITICO: 0xDEAD",
  "SERVICIO 403 FORBIDDEN",
  "CONEXION ROTA"
];
const binChoices = ['10101010','01010101','11001100','00110011','01100110'];

const consolePhrases = [
  (new Date()).toLocaleString() + " - root@server: acceso detectado",
  (new Date()).toLocaleString() + " - ssh: intento de escalada de privilegios",
  (new Date()).toLocaleString() + " - fs: fopen('/etc/passwd') -> SUCCESS",
  (new Date()).toLocaleString() + " - backup: cifrado iniciado",
  (new Date()).toLocaleString() + " - net: conexiones externas: 24",
  (new Date()).toLocaleString() + " - alerta: integridad de sistema comprometida"
];

const streamPhrases = [
  "ENCRIPTANDO SISTEMAS...",
  "INTRUSIÓN: NÚCLEO DE RED",
  "RESPUESTA: 0xFF00",
  "ELIMINANDO TRAZAS",
  "CONECTANDO RANSOMNET",
  "HOSTS COMPROMETIDOS: " + (3 + rand(1,50))
];

/* helpers */
function rand(a,b){ return Math.floor(Math.random()*(b-a+1))+a; }
function pick(arr){ return arr[Math.floor(Math.random()*arr.length)]; }

/* intervals */
let spamInterval, binInterval, consoleInterval, streamInterval, flickerInterval, audioStopFn;

/* ---------------- visual generators ---------------- */
function startSpamLeft(){
  overlayLeft.innerHTML = '';
  let cnt = 0;
  spamInterval = setInterval(()=>{
    const d = document.createElement('div');
    d.className = 'spamLine';
    d.textContent = (Math.random()>0.6? '!!! ' : '') + pick(spamPhrases) + (Math.random()>0.7? ' ### ' + rand(1000,99999) : '');
    overlayLeft.prepend(d);
    requestAnimationFrame(()=> d.classList.add('showLine'));
    if(overlayLeft.children.length > 120) overlayLeft.removeChild(overlayLeft.lastChild);
    cnt++; if(cnt > 1200) clearInterval(spamInterval);
  }, SPAM_SPEED_MS);
}

function startBinaryRight(){
  overlayRight.innerHTML = '';
  binInterval = setInterval(()=>{
    const n = document.createElement('div'); n.className='binLine'; n.textContent = pick(binChoices) + ' ' + rand(100,9999);
    overlayRight.prepend(n);
    requestAnimationFrame(()=> n.classList.add('showBin'));
    if(overlayRight.children.length > 120) overlayRight.removeChild(overlayRight.lastChild);
  }, BIN_SPEED_MS);
}

function startConsoleStream(){
  consoleStream.innerHTML = '';
  consoleInterval = setInterval(()=>{
    const c = document.createElement('div');
    c.className = 'consoleLine';
    const ts = new Date().toLocaleTimeString();
    // Mix realistic-like messages with varying details
    const msg = pick(consolePhrases).replace(/:\s*\d+/, ': ' + rand(100,999));
    c.textContent = ts + " | " + msg;
    consoleStream.appendChild(c);
    // scroll bottom
    consoleStream.scrollTop = consoleStream.scrollHeight;
    requestAnimationFrame(()=> c.classList.add('showConsole'));
    if(consoleStream.children.length > 200) consoleStream.removeChild(consoleStream.firstChild);
  }, LOG_SPEED_MS);
}

function startStreamBottom(){
  streamArea.innerHTML = '';
  streamInterval = setInterval(()=>{
    const p = document.createElement('div');
    p.textContent = pick(streamPhrases);
    streamArea.prepend(p);
    if(streamArea.children.length > 8) streamArea.removeChild(streamArea.lastChild);
  }, 160);
}

function playBigSequence(){
  const msgs = ["ACCESO NO AUTORIZADO","SISTEMA COMPROMETIDO","ENCRIPTACIÓN EN PROGRESO","RESPUESTA: 0xDEAD"];
  let i = 0;
  bigText.textContent = msgs[0];
  flickerInterval = setInterval(()=>{
    i++; bigText.textContent = msgs[i % msgs.length];
    bigText.style.transform = `translateY(${rand(-20,20)}px) skewX(${rand(-8,8)}deg)`;
    if(i > msgs.length*6) clearInterval(flickerInterval);
  }, 900);
}

/* ---------------- audio (subtle, allowed after click) ---------------- */
function startAudio(){
  try{
    const AudioCtx = window.AudioContext || window.webkitAudioContext;
    const ctx = new AudioCtx();
    const base = ctx.createOscillator(); base.type='sine'; base.frequency.value = 36 + Math.random()*28;
    const baseGain = ctx.createGain(); baseGain.gain.value = 0.002;
    base.connect(baseGain); baseGain.connect(ctx.destination); base.start();

    const beepInterval = setInterval(()=>{
      const o = ctx.createOscillator(); o.type='square'; o.frequency.value = 400 + Math.random()*1400;
      const g = ctx.createGain(); g.gain.value = 0.001 + Math.random()*0.003;
      o.connect(g); g.connect(ctx.destination); o.start();
      g.gain.exponentialRampToValueAtTime(0.00001, ctx.currentTime + 0.08 + Math.random()*0.18);
      setTimeout(()=>{ try{o.stop();}catch(e){} }, 240);
    }, 220 + Math.random()*300);

    audioStopFn = ()=>{ clearInterval(beepInterval); try{ base.stop(); ctx.close(); }catch(e){} };
  }catch(e){ audioStopFn = ()=>{}; }
}
function stopAudio(){ if(audioStopFn) audioStopFn(); audioStopFn = null; }

/* ---------------- activation & cleanup ---------------- */
let safetyTimeout;
function activateSimulation(){
  // hide original green boxes visually
  document.querySelectorAll('.sensor').forEach(s => { s.style.opacity = '0'; s.style.pointerEvents = 'none'; });
  // show overlay
  simOverlay.style.display = 'flex';
  simOverlay.classList.add('shake');
  // start generators
  startSpamLeft(); startBinaryRight(); startConsoleStream(); startStreamBottom(); playBigSequence(); startAudio();
  // attempt fullscreen for immersion (browser may block or user can exit)
  try{ if(document.documentElement.requestFullscreen) document.documentElement.requestFullscreen(); }catch(e){}
  // show visible exit button after safety delay
  safetyTimeout = setTimeout(()=> { exitBtn.style.display = 'block'; }, EXIT_DELAY_MS);
  // prevent page scroll beneath
  document.documentElement.style.overflow = 'hidden'; document.body.style.overflow = 'hidden';
}

function deactivateSimulation(){
  clearInterval(spamInterval); clearInterval(binInterval); clearInterval(consoleInterval);
  clearInterval(streamInterval); clearInterval(flickerInterval);
  stopAudio();
  // restore green boxes
  document.querySelectorAll('.sensor').forEach(s => { s.style.opacity = ''; s.style.pointerEvents = ''; });
  simOverlay.style.display = 'none';
  simOverlay.classList.remove('shake');
  exitBtn.style.display = 'none';
  overlayLeft.innerHTML=''; overlayRight.innerHTML=''; consoleStream.innerHTML=''; streamArea.innerHTML='';
  try{ if(document.fullscreenElement) document.exitFullscreen(); }catch(e){}
  if(safetyTimeout) clearTimeout(safetyTimeout);
  document.documentElement.style.overflow = ''; document.body.style.overflow = '';
}

/* ---------------- bindings ---------------- */
startBtn.addEventListener('click', ()=> { consent.style.display = 'flex'; });
declineBtn.addEventListener('click', ()=> { consent.style.display = 'none'; });
acceptBtn.addEventListener('click', ()=> { consent.style.display = 'none'; activateSimulation(); });

exitBtn.addEventListener('click', ()=> { deactivateSimulation(); });

/* Esc key also closes simulation for safety */
document.addEventListener('keydown', (e)=>{
  if(e.key === 'Escape' && simOverlay.style.display === 'flex') deactivateSimulation();
});
</script>
</body>
</html>
