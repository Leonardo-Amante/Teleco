<html lang="es">
<head>
<meta charset="utf-8" />
<meta name="viewport" content="width=device-width,initial-scale=1" />
<title>Dashboard — Simulación</title>
<style>
  @import url('https://fonts.googleapis.com/css2?family=Orbitron:wght@500&display=swap');

  :root{
    --neon: #00ffae;
    --bg: #0a0f14;
    --danger: #ff073a;
    --muted: #98a6ad;
  }

  /* -------- Base / Layout -------- */
  html,body{height:100%;margin:0;background:var(--bg);font-family:'Orbitron',monospace;color:#cfeee6;overflow:hidden}
  .center-wrap{height:100%;display:flex;align-items:center;justify-content:center;padding:20px;box-sizing:border-box}

  .card{
    display:flex;
    align-items:center;
    justify-content:center;
    gap:48px;
    background:#161b22;
    border:1px solid var(--neon);
    border-radius:14px;
    padding:34px 44px;
    box-shadow: 0 8px 30px rgba(0,0,0,0.6);
    width:880px;
    max-width:94vw;
    z-index:1;
    transition:opacity .28s ease;
  }

  .sensor {
    width:180px;
    height:120px;
    border-radius:12px;
    background:#0f161f;
    border:1px solid var(--neon);
    color:var(--neon);
    box-shadow: 0 6px 18px rgba(0,255,174,0.06), inset 0 2px 8px rgba(0,0,0,0.6);
    display:flex;
    flex-direction:column;
    align-items:center;
    justify-content:center;
    font-size:18px;
    text-align:center;
    transition:opacity .35s ease, transform .18s ease;
  }
  .sensor.hidden{ opacity: 0; pointer-events:none; transform: scale(.98); }

  .center-col{display:flex;flex-direction:column;align-items:center;gap:12px}
  .btn{
    background:var(--neon);
    color:#000;
    border:none;
    padding:10px 18px;
    border-radius:8px;
    font-weight:800;
    cursor:pointer;
  }

  .footer{position:fixed;left:0;bottom:8px;width:100%;text-align:center;color:var(--muted);font-size:12px;z-index:1}

  /* -------- Simulated overlay (covers viewport) -------- */
  #overlay {
    position:fixed;
    inset:0;
    display:none;            /* show when active */
    z-index:99999;
    background:#000;        /* black background for realism */
    color:#fff;
    align-items:stretch;
    justify-content:flex-start;
    overflow:hidden;
  }

  .overlay-left{
    width:40%;
    padding:28px 18px;
    box-sizing:border-box;
    display:flex;
    flex-direction:column;
    gap:8px;
    pointer-events:none;
    overflow:hidden;
  }

  .overlay-right{
    width:22%;
    padding:22px;
    box-sizing:border-box;
    display:flex;
    flex-direction:column;
    gap:6px;
    align-items:flex-end;
    pointer-events:none;
    overflow:hidden;
  }

  .overlay-center{
    flex:1;
    display:flex;
    align-items:center;
    justify-content:center;
    position:relative;
    padding:20px;
  }

  .big-alert{
    color:var(--danger);
    font-size:56px;
    font-weight:900;
    text-align:center;
    text-shadow: 0 0 18px rgba(255,0,60,0.9);
    margin-bottom:18px;
  }

  .console-panel{
    width:86%;
    max-width:900px;
    background:#071014;
    border:1px solid rgba(0,255,174,0.04);
    padding:12px;
    border-radius:10px;
    height:36vh;
    overflow:auto;
    font-family:monospace;
    color:#9fffbf;
    font-size:13px;
  }

  .console-line{
    opacity:0;
    transform:translateY(6px);
    margin-bottom:6px;
  }
  .console-line.show{
    opacity:1;
    transform:translateY(0);
    transition: all .22s ease-out;
  }

  .spam-line{
    color:var(--danger);
    font-weight:900;
    font-size:14px;
    text-transform:uppercase;
    opacity:0;
    transform:translateX(-120%);
    text-shadow:0 0 8px rgba(255,0,60,0.9);
    white-space:nowrap;
  }
  .spam-line.show{
    opacity:1;
    transform:translateX(0);
    transition: all .28s cubic-bezier(.2,.9,.3,1);
  }

  .bin-line{
    color:#ff6b74;
    font-weight:700;
    font-size:12px;
    opacity:0;
    transform:translateY(-18px);
  }
  .bin-line.show{
    opacity:1;
    transform:translateY(0);
    transition: all .45s linear;
  }

  .stream-area{
    position:absolute;
    bottom:6vh;
    left:46%;
    right:8%;
    pointer-events:none;
    color:#ffb7b7;
    font-weight:800;
  }

  /* scanlines / tint for cinematic feel (subtle) */
  .scanline{position:absolute;left:0;right:0;height:2px;background:linear-gradient(90deg,transparent,#ff0033,transparent);opacity:0.18}
  .tint{position:absolute;inset:0;background:radial-gradient(circle at 50% 20%, rgba(255,0,0,0.04), transparent 25%);mix-blend-mode:screen;pointer-events:none}

  /* popup-lite: small inline message (non-blocking) */
  .mini-popup{
    position:fixed;
    right:20%;
    top:12%;
    background:rgba(20,20,20,0.95);
    border:1px solid rgba(255,0,60,0.06);
    color:#fff;
    padding:10px 12px;
    border-radius:8px;
    z-index:100000;
    box-shadow:0 8px 30px rgba(0,0,0,0.6);
    display:none;
  }

  /* exit button */
  #exitBtn{
    position:fixed;
    top:14px;
    right:14px;
    z-index:100001;
    background:rgba(255,255,255,0.06);
    color:#fff;
    border:1px solid rgba(255,255,255,0.06);
    padding:8px 12px;
    border-radius:8px;
    cursor:pointer;
    display:none;
  }

  @media (max-width:720px){
    .big-alert{font-size:32px}
    .overlay-left{width:46%}
    .overlay-right{display:none}
    .stream-area{left:50%;right:2%}
  }

</style>
</head>
<body>
  <!-- Dashboard -->
  <div class="center-wrap">
    <div class="card" id="card">
      <div class="sensor" id="sensorTemp">🌡️<div id="temp" style="margin-top:8px;font-size:18px">-- °C</div></div>

      <div class="center-col">
        <h2 style="color:var(--neon);margin:0">Infórmate sobre Telecomunicaciones</h2>
        <button class="btn" id="infoBtn">Información</button>
      </div>

      <div class="sensor" id="sensorHum">💧<div id="hum" style="margin-top:8px;font-size:18px">-- %</div></div>
    </div>
  </div>

  <div class="footer">ESP8266 Dashboard — Tema Neon</div>

  <!-- Simulated overlay (covers viewport) -->
  <div id="overlay" aria-hidden="true">
    <div class="overlay-left" id="leftCol"></div>

    <div class="overlay-center">
      <div>
        <div class="big-alert" id="bigAlert">ACCESO NO AUTORIZADO</div>
        <div class="console-panel console-panel" >
          <div class="console-panel-inner console-panel-inner">
            <div class="console-panel" id="console"></div>
          </div>
        </div>
      </div>

      <div class="tint" aria-hidden="true"></div>
    </div>

    <div class="overlay-right" id="rightCol"></div>

    <div class="scanline" style="top:22%"></div>
    <div class="scanline" style="top:56%"></div>

    <div class="stream-area" id="streamArea"></div>
  </div>

  <button id="exitBtn" aria-label="Salir de la simulación">SALIR</button>

  <div class="mini-popup" id="miniPopup">ERROR CRÍTICO</div>

<script>
/* ====== Config ====== */
const SPAM_INTERVAL_MS = 60;   // speed of left spam
const BIN_INTERVAL_MS  = 80;   // speed of right binary
const CONSOLE_MS       = 150;  // console log speed
const STREAM_MS        = 200;  // bottom stream speed
const POPUP_INTERVAL_MS= 4000; // mini-popup frequency
/* ==================== */

/* Elements */
const infoBtn = document.getElementById('infoBtn');
const overlay = document.getElementById('overlay');
const leftCol = document.getElementById('leftCol');
const rightCol = document.getElementById('rightCol');
const consoleEl = document.getElementById('console');
const streamArea = document.getElementById('streamArea');
const bigAlert = document.getElementById('bigAlert');
const exitBtn = document.getElementById('exitBtn');
const sensorTemp = document.getElementById('sensorTemp');
const sensorHum  = document.getElementById('sensorHum');
const card = document.getElementById('card');
const miniPopup = document.getElementById('miniPopup');

/* Helpers */
function rnd(a,b){ return Math.floor(Math.random()*(b-a+1))+a; }
function pick(arr){ return arr[Math.floor(Math.random()*arr.length)]; }

/* Content banks */
const spamPhrases = [
  "ACCESO NO AUTORIZADO +++",
  "ENCRIPTANDO... [████▒▒▒▒▒]",
  "INTENTO INTRUSIÓN: IP 192.168."+rnd(1,254)+"."+rnd(1,254),
  "ELIMINANDO BACKUPS...",
  "ERROR 0xDEAD",
  "SERVICIO 403 FORBIDDEN",
  "KERNEL PANIC",
  "MÓDULO CORRUPTO"
];

const binChoices = ['10101010','01010101','11001100','00110011','01100110','11110000'];

const consolePhrases = [
  ()=> new Date().toLocaleTimeString() + " | root@server: acceso detectado",
  ()=> new Date().toLocaleTimeString() + " | ssh: intento escalada privilegios",
  ()=> new Date().toLocaleTimeString() + " | fs: fopen('/etc/passwd') -> SUCCESS",
  ()=> new Date().toLocaleTimeString() + " | backup: cifrado iniciado",
  ()=> new Date().toLocaleTimeString() + " | net: conexiones externas: "+rnd(2,200),
  ()=> new Date().toLocaleTimeString() + " | alerta: integridad comprometida"
];

const streamPhrases = [
  "ENCRIPTANDO SISTEMAS...",
  "INTRUSIÓN: NÚCLEO DE RED",
  "RESPUESTA: 0xFF00",
  "ELIMINANDO TRAZAS",
  "CONECTANDO RANSOMNET"
];

/* Intervals handles */
let spamTimer = null, binTimer = null, consoleTimer = null, streamTimer = null, popupTimer = null;

/* Simulated sensor updater (keeps green boxes lively) */
function updateSensors(){
  document.getElementById('temp').textContent = (18 + Math.random()*9).toFixed(1) + ' °C';
  document.getElementById('hum').textContent  = (30 + Math.random()*30).toFixed(1) + ' %';
}
setInterval(updateSensors, 2400);
updateSensors();

/* Add functions */
function addSpamLine(txt){
  const d = document.createElement('div');
  d.className = 'spam-line';
  d.textContent = txt;
  leftCol.prepend(d);
  // animate in
  requestAnimationFrame(()=> d.classList.add('show'));
  // cleanup
  if(leftCol.children.length > 220) leftCol.removeChild(leftCol.lastChild);
}
function addBin(txt){
  const d = document.createElement('div');
  d.className = 'bin-line';
  d.textContent = txt;
  rightCol.prepend(d);
  requestAnimationFrame(()=> d.classList.add('show'));
  if(rightCol.children.length > 220) rightCol.removeChild(rightCol.lastChild);
}
function addConsole(txt){
  const d = document.createElement('div');
  d.className = 'console-line';
  d.textContent = txt;
  consoleEl.appendChild(d);
  consoleEl.scrollTop = consoleEl.scrollHeight;
  requestAnimationFrame(()=> d.classList.add('show'));
  if(consoleEl.children.length > 800) consoleEl.removeChild(consoleEl.firstChild);
}
function addStream(txt){
  const d = document.createElement('div'); d.textContent = txt;
  streamArea.prepend(d);
  if(streamArea.children.length > 12) streamArea.removeChild(streamArea.lastChild);
}

/* Start simulation (simple, stable) */
function startSimulation(){
  // hide the green sensor boxes smoothly
  sensorTemp.classList.add('hidden');
  sensorHum.classList.add('hidden');

  // slight fade card
  card.style.opacity = '0.08';

  // reveal overlay
  overlay.style.display = 'flex';
  exitBtn.style.display = 'inline-block';
  miniPopup.style.display = 'block';
  setTimeout(()=> miniPopup.style.display = 'none', 1600);

  // start intervals
  spamTimer = setInterval(()=> addSpamLine(pick(spamPhrases)), SPAM_INTERVAL_MS);
  binTimer  = setInterval(()=> addBin(pick(binChoices) + ' ' + rnd(100,9999)), BIN_INTERVAL_MS);
  consoleTimer = setInterval(()=> addConsole( typeof pick(consolePhrases) === 'function' ? pick(consolePhrases)() : pick(consolePhrases) ), CONSOLE_MS);
  streamTimer = setInterval(()=> addStream(pick(streamPhrases)), STREAM_MS);
  popupTimer = setInterval(()=> {
    // small visible flash near center (non-blocking)
    miniPopup.textContent = pick(['ERROR CRÍTICO','ACCESO BLOQUEADO','FALLO ENCRIPTACIÓN']); 
    miniPopup.style.display = 'block';
    setTimeout(()=> miniPopup.style.display = 'none', 1200);
  }, POPUP_INTERVAL_MS);

  // small bigAlert flicker sequence
  const seq = ["ACCESO NO AUTORIZADO","SISTEMA COMPROMETIDO","ENCRIPTACIÓN EN PROGRESO"];
  let i=0;
  const flick = setInterval(()=>{
    bigAlert.textContent = seq[i % seq.length];
    bigAlert.style.transform = `translateY(${rnd(-8,8)}px) skewX(${rnd(-6,6)}deg)`;
    i++;
    if(i > seq.length * 6) clearInterval(flick);
  }, 900);
}

/* Stop simulation and clean up */
function stopSimulation(){
  // stop intervals
  clearInterval(spamTimer); clearInterval(binTimer); clearInterval(consoleTimer); clearInterval(streamTimer); clearInterval(popupTimer);
  spamTimer = binTimer = consoleTimer = streamTimer = popupTimer = null;

  // hide overlay
  overlay.style.display = 'none';
  exitBtn.style.display = 'none';
  miniPopup.style.display = 'none';

  // restore sensors and card
  sensorTemp.classList.remove('hidden');
  sensorHum.classList.remove('hidden');
  card.style.opacity = '';

  // clear content (light)
  leftCol.innerHTML = '';
  rightCol.innerHTML = '';
  consoleEl.innerHTML = '';
  streamArea.innerHTML = '';
  bigAlert.textContent = 'ACCESO NO AUTORIZADO';
}

/* Bind buttons */
infoBtn.addEventListener('click', startSimulation);
exitBtn.addEventListener('click', stopSimulation);

/* Allow Esc to exit as safety */
document.addEventListener('keydown', (e)=>{
  if(e.key === 'Escape' && overlay.style.display === 'flex') stopSimulation();
});
</script>
</body>
</html>
