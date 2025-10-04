<html lang="es">
<head>
<meta charset="utf-8"/>
<meta name="viewport" content="width=device-width,initial-scale=1"/>
<title>Simulación Realista — Telecomunicaciones</title>
<style>
  @import url('https://fonts.googleapis.com/css2?family=Orbitron:wght@500&display=swap');

  :root{
    --neon: #00ffae;
    --bg: #020305;
    --danger: #ff0033;
    --muted: #9aa6ad;
  }
  html,body{height:100%;margin:0;background:var(--bg);font-family:'Orbitron',monospace;color:#dbeee6;overflow:hidden}
  /* Dashboard (keeps green boxes) */
  .wrap{height:100%;display:flex;align-items:center;justify-content:center;padding:16px;box-sizing:border-box}
  .card{display:flex;gap:36px;align-items:center;background:linear-gradient(180deg,#0f1516,#071011);border-radius:12px;padding:20px 28px;border:1px solid rgba(0,255,174,0.06);box-shadow:0 10px 40px rgba(0,0,0,0.7);z-index:1;max-width:94vw;width:920px}
  .sensor{width:180px;height:120px;border-radius:12px;background:#0f161f;border:1px solid var(--neon);display:flex;flex-direction:column;align-items:center;justify-content:center;color:var(--neon);font-size:18px;box-shadow:0 8px 28px rgba(0,255,174,0.06);transition:opacity .25s}
  .center{display:flex;flex-direction:column;align-items:center;gap:10px}
  .cta{background:var(--neon);color:#000;border:none;padding:10px 18px;border-radius:8px;font-weight:800;cursor:pointer}
  .footer{position:fixed;left:0;bottom:8px;width:100%;text-align:center;color:var(--muted);font-size:12px;z-index:1}

  /* Consent modal (must confirm) */
  #consent{position:fixed;inset:0;display:flex;align-items:center;justify-content:center;background:rgba(0,0,0,0.72);z-index:99999}
  .consentBox{width:min(760px,94vw);background:linear-gradient(#071014,#041012);padding:22px;border-radius:12px;border:1px solid rgba(0,255,174,0.06);color:#eafff0}
  .consentBox h3{margin:0 0 8px;color:var(--neon)}
  .consentButtons{display:flex;gap:12px;margin-top:14px}

  /* SIMULATED FULLSCREEN OVERLAY */
  #simOverlay{
    position:fixed;inset:0;display:none;z-index:200000;background:#000;overflow:hidden;color:#fff;
    display:flex;align-items:stretch;justify-content:flex-start;
  }
  .overlay-left{width:42%;padding:28px 20px;box-sizing:border-box;display:flex;flex-direction:column;gap:6px;pointer-events:none;overflow:hidden}
  .overlay-right{width:24%;padding:28px 18px;box-sizing:border-box;display:flex;flex-direction:column;gap:6px;align-items:flex-end;pointer-events:none;overflow:hidden}
  .overlay-center{flex:1;display:flex;align-items:center;justify-content:center;position:relative;pointer-events:none}

  /* Visual elements */
  .spamLine{color:var(--danger);font-weight:900;font-size:14px;text-transform:uppercase;text-shadow:0 0 12px rgba(255,0,60,0.9);opacity:0;transform:translateX(-120%);white-space:nowrap}
  .binLine{color:#ff6372;font-weight:700;font-size:12px;margin-bottom:6px;opacity:0;transform:translateY(-20px)}
  .consoleLine{font-family:monospace;font-size:13px;color:#9fffbf;opacity:0;transform:translateY(6px)}
  .bigText{font-size:64px;font-weight:900;color:var(--danger);text-shadow:0 0 30px rgba(255,0,60,0.9);text-align:center}
  .ransomBox{background: linear-gradient(180deg, rgba(20,0,0,0.18), rgba(0,0,0,0.45));border: 1px solid rgba(255,0,40,0.06);padding:18px;border-radius:8px;width:86%;max-width:980px;text-align:center}
  .consolePanel{background:#071013;border:1px solid rgba(0,255,174,0.04);padding:12px;border-radius:8px;height:36vh;overflow:auto}
  .consoleStream{height:100%;overflow:auto;padding-right:6px}
  .streamArea{position:absolute;left:44%;right:26%;bottom:6vh;color:#ffb7b7;font-weight:800;pointer-events:none;padding-left:12px}
  .scanline{position:absolute;left:0;right:0;height:2px;background:linear-gradient(90deg,transparent,#ff0033,transparent);opacity:0.25}
  .tint{position:absolute;inset:0;background:radial-gradient(circle at 50% 20%, rgba(255,0,0,0.06), transparent 25%);mix-blend-mode:screen;pointer-events:none}
  /* popup windows */
  .popup{position:fixed;background:#121212;border:2px solid rgba(255,0,40,0.08);color:#fff;padding:12px;border-radius:6px;box-shadow:0 12px 40px rgba(0,0,0,0.8);min-width:260px;z-index:210000;animation:popupIn .18s ease-out}
  .popup .title{font-weight:900;color:#ff9ba0;margin-bottom:6px}
  .popup .closeBtn{position:absolute;right:8px;top:6px;background:transparent;border:none;color:#fff;cursor:pointer;font-weight:900}
  @keyframes popupIn{from{transform:translateY(-10px) scale(.98);opacity:0}to{transform:translateY(0) scale(1);opacity:1}}

  /* utility transitions */
  .showLine{opacity:1;transform:translateX(0);transition:all .28s cubic-bezier(.2,.9,.3,1)}
  .showBin{opacity:1;transform:translateY(0);transition:all .45s linear}
  .showConsole{opacity:1;transform:translateY(0);transition:all .26s ease-out}
  .showPopup{opacity:1;transform:translateY(0)}
  .shake{animation:shakeAnim .6s cubic-bezier(.36,.07,.19,.97) infinite}
  @keyframes shakeAnim{0%{transform:translate(0,0)}10%{transform:translate(-6px,4px)}20%{transform:translate(6px,-6px)}30%{transform:translate(-4px,6px)}40%{transform:translate(4px,-2px)}50%{transform:translate(-2px,2px)}100%{transform:translate(0,0)}}

  /* exit button */
  #exitBtn{position:fixed;right:14px;top:12px;z-index:200002;background:rgba(0,0,0,0.65);color:#fff;border:1px solid rgba(255,255,255,0.08);padding:10px 12px;border-radius:8px;cursor:pointer;display:none}

  @media (max-width:720px){
    .bigText{font-size:36px}
    .overlay-left{width:48%}
    .overlay-right{display:none}
    .overlay-center{left:48%;right:0}
    .streamArea{left:50%;right:3%}
  }
</style>
</head>
<body>
  <!-- dashboard -->
  <div class="wrap">
    <div class="card" id="card">
      <div class="sensor" id="sensorTemp">🌡️<div id="temp" style="margin-top:8px;font-size:18px">-- °C</div></div>
      <div class="center">
        <h2 style="color:var(--neon);margin:0">Infórmate sobre Telecomunicaciones</h2>
        <button class="cta" id="startBtn">Iniciar simulación</button>
      </div>
      <div class="sensor" id="sensorHum">💧<div id="hum" style="margin-top:8px;font-size:18px">-- %</div></div>
    </div>
  </div>

  <div class="footer">ESP8266 Dashboard — Simulación</div>

  <!-- consent modal (required) -->
  <div id="consent">
    <div class="consentBox" role="dialog" aria-modal="true">
      <h3>Confirmación — Simulación educativa</h3>
      <p>Vas a iniciar una simulación intensiva (visual y sonora). Esta experiencia es <strong>ficticia</strong> y no afecta archivos reales. Asegúrate que la audiencia está informada.</p>
      <div class="consentButtons">
        <button class="cta" id="consentYes">Sí, iniciar</button>
        <button class="cta" id="consentNo" style="background:#444;color:#fff">Cancelar</button>
      </div>
    </div>
  </div>

  <!-- simulated fullscreen overlay -->
  <div id="simOverlay" aria-hidden="true">
    <div class="overlay-left" id="overlayLeft"></div>

    <div class="overlay-center">
      <div class="ransomBox">
        <div class="bigText" id="bigText">ACCESO NO AUTORIZADO</div>
        <div style="height:12px"></div>
        <div class="consolePanel">
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

  <button id="exitBtn">SALIR</button>

<script>
/* ---------- settings ---------- */
const EXIT_DELAY_MS = 7000;   // exit button shown after this ms
const SPAM_SPEED_MS = 36;     // spam frequency (left)
const BIN_SPEED_MS = 64;      // binary right
const LOG_SPEED_MS = 120;     // console log speed

/* ---------- ui elements ---------- */
const consent = document.getElementById('consent');
const consentYes = document.getElementById('consentYes');
const consentNo = document.getElementById('consentNo');
const startBtn = document.getElementById('startBtn');
const simOverlay = document.getElementById('simOverlay');
const overlayLeft = document.getElementById('overlayLeft');
const overlayRight = document.getElementById('overlayRight');
const consoleStream = document.getElementById('consoleStream');
const streamArea = document.getElementById('streamArea');
const bigText = document.getElementById('bigText');
const exitBtn = document.getElementById('exitBtn');
const sensorBoxes = document.querySelectorAll('.sensor');

/* ---------- sensor demo values ---------- */
function updateSensors(){
  document.getElementById('temp').textContent = (18 + Math.random()*9).toFixed(1) + ' °C';
  document.getElementById('hum').textContent  = (30 + Math.random()*30).toFixed(1) + ' %';
}
setInterval(updateSensors, 2400);
updateSensors();

/* ---------- content banks ---------- */
function rand(a,b){ return Math.floor(Math.random()*(b-a+1))+a; }
function pick(arr){ return arr[Math.floor(Math.random()*arr.length)]; }

const spamPhrases = [
  "ACCESO NO AUTORIZADO +++",
  "INTRUSION: IP 192.168."+rand(1,254)+"."+rand(1,254),
  "ENCRIPTANDO... [█████▒▒▒▒▒]",
  "ELIMINANDO BACKUPS...",
  "ERROR CRITICO: 0xDEAD",
  "SERVICIO 403 FORBIDDEN",
  "CONEXION ROTA",
  "KERNEL PANIC",
  "FALLO ENTRADA/SALIDA",
  "MÓDULO CORRUPTO DETECTADO"
];

const binChoices = ['10101010','01010101','11001100','00110011','01100110','11110000'];

const consolePhrases = [
  ()=> new Date().toLocaleTimeString() + " | root@server: acceso detectado",
  ()=> new Date().toLocaleTimeString() + " | ssh: intento escalada privilegios",
  ()=> new Date().toLocaleTimeString() + " | fs: fopen('/etc/passwd') -> SUCCESS",
  ()=> new Date().toLocaleTimeString() + " | backup: cifrado iniciado",
  ()=> new Date().toLocaleTimeString() + " | net: conexiones externas:"+rand(4,256),
  ()=> new Date().toLocaleTimeString() + " | alerta: integridad comprometida",
  ()=> new Date().toLocaleTimeString() + " | process: rgx.exe terminated unexpectedly"
];

const streamPhrases = [
  "ENCRIPTANDO SISTEMAS...",
  "INTRUSIÓN: NÚCLEO DE RED",
  "RESPUESTA: 0xFF00",
  "ELIMINANDO TRAZAS",
  "CONECTANDO RANSOMNET",
  "HOSTS COMPROMETIDOS: " + (3 + rand(1,50))
];

/* ---------- webaudio: realistic layered sounds ---------- */
let audioCtx = null;
let activeNodes = [];
function ensureAudio(){
  if(!audioCtx) audioCtx = new (window.AudioContext || window.webkitAudioContext)();
}

/* low rumble (continuous) */
function startRumble(){
  ensureAudio();
  const osc = audioCtx.createOscillator();
  const gain = audioCtx.createGain();
  osc.type = 'sine';
  osc.frequency.value = 36 + Math.random()*8; // very low
  gain.gain.value = 0.0025;
  osc.connect(gain);
  gain.connect(audioCtx.destination);
  osc.start();
  activeNodes.push({type:'osc', node:osc, gain});
  // slow LFO to modulate frequency
  const lfo = audioCtx.createOscillator();
  const lfoGain = audioCtx.createGain();
  lfo.frequency.value = 0.07;
  lfoGain.gain.value = 15;
  lfo.connect(lfoGain);
  lfoGain.connect(osc.frequency);
  lfo.start();
  activeNodes.push({type:'lfo', node:lfo});
}

/* glitch / beeps */
function glitchBeepCluster(){
  ensureAudio();
  // create several short beeps
  for(let i=0;i< (3 + Math.floor(Math.random()*4)); i++){
    const delay = i * (20 + Math.random()*60);
    setTimeout(()=>{
      const o = audioCtx.createOscillator();
      const g = audioCtx.createGain();
      o.type = Math.random()>0.5 ? 'square' : 'sawtooth';
      o.frequency.value = 400 + Math.random()*1400;
      g.gain.value = 0.0009 + Math.random()*0.003;
      o.connect(g); g.connect(audioCtx.destination);
      o.start();
      g.gain.setValueAtTime(g.gain.value, audioCtx.currentTime);
      g.gain.exponentialRampToValueAtTime(0.00001, audioCtx.currentTime + 0.06 + Math.random()*0.18);
      setTimeout(()=>{ try{ o.stop(); }catch(e){}}, 260);
    }, delay);
  }
}

/* static crackle (looping buffer from noise) */
let staticNode = null;
function startStatic(){
  ensureAudio();
  // white noise via buffer source
  const bufferSize = 2 * audioCtx.sampleRate;
  const buffer = audioCtx.createBuffer(1, bufferSize, audioCtx.sampleRate);
  const data = buffer.getChannelData(0);
  for(let i=0;i<bufferSize;i++){ data[i] = (Math.random()*2 - 1) * 0.02; }
  const src = audioCtx.createBufferSource();
  src.buffer = buffer;
  src.loop = true;
  const g = audioCtx.createGain(); g.gain.value = 0.0009;
  src.connect(g); g.connect(audioCtx.destination);
  src.start();
  staticNode = {src,g};
}

/* stop all audio nodes */
function stopAllAudio(){
  try{
    activeNodes.forEach(n=>{ try{ if(n.node.stop) n.node.stop(); }catch(e){} });
    activeNodes = [];
    if(staticNode){ try{ staticNode.src.stop(); }catch(e){} staticNode=null; }
    if(audioCtx){ /* keep audio context open for reuse */ }
  }catch(e){}
}

/* ---------- visual generators ---------- */
let spamInterval, binInterval, consoleInterval, streamInterval, popupInterval, bigFlickerInterval;

function addSpamLine(text){
  const el = document.createElement('div'); el.className='spamLine'; el.textContent=text;
  overlayLeft.prepend(el);
  requestAnimationFrame(()=> el.classList.add('showLine'));
  if(overlayLeft.children.length > 220) overlayLeft.removeChild(overlayLeft.lastChild);
}

function addBinLine(text){
  const el = document.createElement('div'); el.className='binLine'; el.textContent=text;
  overlayRight.prepend(el);
  requestAnimationFrame(()=> el.classList.add('showBin'));
  if(overlayRight.children.length > 220) overlayRight.removeChild(overlayRight.lastChild);
}

function addConsoleLine(text){
  const el = document.createElement('div'); el.className='consoleLine'; el.textContent=text;
  consoleStream.appendChild(el);
  consoleStream.scrollTop = consoleStream.scrollHeight;
  requestAnimationFrame(()=> el.classList.add('showConsole'));
  if(consoleStream.children.length > 1000) consoleStream.removeChild(consoleStream.firstChild);
}

function addStreamLine(text){
  const el = document.createElement('div'); el.textContent = text;
  streamArea.prepend(el);
  if(streamArea.children.length > 12) streamArea.removeChild(streamArea.lastChild);
}

/* popup windows */
function spawnPopup(title, body, ttl=7000){
  const p = document.createElement('div');
  p.className='popup';
  const left = rand(4,78);
  const top = rand(6,78);
  p.style.left = left + 'vw';
  p.style.top = top + 'vh';
  p.innerHTML = `<button class="closeBtn" aria-hidden="true">×</button><div class="title">${title}</div><div class="body" style="margin-top:6px">${body}</div>`;
  document.body.appendChild(p);
  requestAnimationFrame(()=> p.classList.add('showPopup'));
  // small sound
  if(Math.random() > 0.55) glitchBeepCluster(); else glitchBeepCluster();
  // remove later
  const cb = p.querySelector('.closeBtn');
  cb.addEventListener('click', ()=> { try{ p.remove(); }catch(e){} });
  setTimeout(()=> { try{ p.remove(); }catch(e){} }, ttl + rand(-1200,1200));
}

/* popup bursts */
function popupBurst(count){
  const examples = [
    {t:'ERROR CRÍTICO', b:'KERNEL PANIC — reboot recommendado'},
    {t:'ACCESO BLOQUEADO', b:'Cuenta root: falló autenticación'},
    {t:'FALLO ENCRIPTACIÓN', b:'/home/* cifrado'},
    {t:'SERVICIO CAÍDO', b:'ssh: cerrado por actividad sospechosa'},
    {t:'ADVERTENCIA', b:'CONSOLA: integridad comprometida'}
  ];
  for(let i=0;i<count;i++){
    setTimeout(()=> {
      const e = pick(examples);
      spawnPopup(e.t, e.b, 6000 + rand(-1000,1000));
    }, i * rand(80,260));
  }
}

/* sequences */
function startStreams(){
  // spam left
  spamInterval = setInterval(()=> {
    addSpamLine(pick(spamPhrases));
    if(Math.random() > 0.86) glitchBeepCluster();
  }, SPAM_SPEED_MS);

  // binary right
  binInterval = setInterval(()=> {
    addBinLine(pick(binChoices) + ' ' + rand(10,9999));
    if(Math.random() > 0.9) glitchBeepCluster();
  }, BIN_SPEED_MS);

  // console logs
  consoleInterval = setInterval(()=> {
    addConsoleLine( (typeof pick(consolePhrases) === 'function' ? pick(consolePhrases)() : pick(consolePhrases)) );
    if(Math.random() > 0.88) glitchBeepCluster();
  }, LOG_SPEED_MS);

  // bottom stream
  streamInterval = setInterval(()=> {
    addStreamLine(pick(streamPhrases));
  }, 150);

  // popup bursts occasional
  popupInterval = setInterval(()=> { popupBurst(rand(3,9)); }, 2600);
  // initial burst
  popupBurst(8);

  // big text flicker sequence
  const messages = ["ACCESO NO AUTORIZADO","SISTEMA COMPROMETIDO","ENCRIPTACIÓN EN PROGRESO","RESPUESTA: 0xDEAD"];
  let idx = 0;
  bigFlickerInterval = setInterval(()=> {
    bigText.textContent = messages[idx % messages.length];
    bigText.style.transform = `translateY(${rand(-16,16)}px) skewX(${rand(-6,6)}deg)`;
    idx++;
    if(idx > messages.length * 8){ clearInterval(bigFlickerInterval); }
  }, 900);
}

/* stop everything and cleanup */
function stopStreams(){
  try{
    clearInterval(spamInterval); clearInterval(binInterval); clearInterval(consoleInterval); clearInterval(streamInterval); clearInterval(popupInterval); clearInterval(bigFlickerInterval);
    // remove popups
    document.querySelectorAll('.popup').forEach(n=>n.remove());
  }catch(e){}
}

/* ---------- activate / deactivate ---------- */
let active = false;
function activateSimulation(){
  if(active) return;
  active = true;
  // hide sensor boxes visually
  sensorBoxes.forEach(s=>{ s.style.opacity = '0'; s.style.pointerEvents='none'; });
  // show overlay
  simOverlay.style.display = 'flex';
  simOverlay.classList.add('shake');
  // attempt to enter fullscreen (optional, will not trap)
  try{ if(document.documentElement.requestFullscreen) document.documentElement.requestFullscreen(); }catch(e){}
  // audio
  ensureAudio();
  startRumble(); startStatic(); // static is in startStatic -> sets staticNode
  // start streams + popups
  startStreams();
  // play occasional glitch/beeps automatically too
  const beepLoop = setInterval(()=> { if(Math.random()>0.6) glitchBeepCluster(); }, 1100);
  // show exit after safety delay
  setTimeout(()=> exitBtn.style.display = 'block', EXIT_DELAY_MS);
  // store beepLoop handle so we can clear later via stopAll
  activeBeepLoop = beepLoop;
  // prevent scroll
  document.documentElement.style.overflow='hidden'; document.body.style.overflow='hidden';
}
let activeBeepLoop = null;

function deactivateSimulation(){
  if(!active) return;
  active = false;
  // restore sensors
  sensorBoxes.forEach(s=>{ s.style.opacity = ''; s.style.pointerEvents=''; });
  simOverlay.style.display = 'none';
  simOverlay.classList.remove('shake');
  // stop streams and audio
  stopStreams();
  if(activeBeepLoop) clearInterval(activeBeepLoop);
  stopAllAudio();
  // clear visual contents
  overlayLeft.innerHTML=''; overlayRight.innerHTML=''; consoleStream.innerHTML=''; streamArea.innerHTML='';
  // remove any remaining popups
  document.querySelectorAll('.popup').forEach(n=>n.remove());
  exitBtn.style.display = 'none';
  // exit fullscreen if active
  try{ if(document.fullscreenElement) document.exitFullscreen(); }catch(e){}
  document.documentElement.style.overflow=''; document.body.style.overflow='';
}

/* ---------- bindings ---------- */
startBtn.addEventListener('click', ()=> { consent.style.display = 'flex'; });
consentNo.addEventListener('click', ()=> { consent.style.display = 'none'; });
consentYes.addEventListener('click', ()=> { consent.style.display = 'none'; activateSimulation(); });

exitBtn.addEventListener('click', ()=> { deactivateSimulation(); });

document.addEventListener('keydown', (e)=>{
  if(e.key === 'Escape' && active){ deactivateSimulation(); }
});

/* prevent pointer events reaching background while overlay active */
simOverlay.addEventListener('click', (e)=> { e.stopPropagation(); e.preventDefault(); }, true);

/* helpers (start static uses same name) */
function startStatic(){
  ensureAudio();
  // white noise buffer
  const bufferSize = 2 * audioCtx.sampleRate;
  const buffer = audioCtx.createBuffer(1, bufferSize, audioCtx.sampleRate);
  const data = buffer.getChannelData(0);
  for(let i=0;i<bufferSize;i++){ data[i] = (Math.random()*2 -1) * 0.02; }
  const src = audioCtx.createBufferSource();
  src.buffer = buffer;
  src.loop = true;
  const g = audioCtx.createGain(); g.gain.value = 0.0009;
  src.connect(g); g.connect(audioCtx.destination);
  src.start();
  // keep reference to stop later
  staticNode = {src,g};
}
let staticNode = null;

</script>
</body>
</html>
