<html lang="es">
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<title>Telecomunicaciones — Dashboard</title>
<style>
@import url('https://fonts.googleapis.com/css2?family=Orbitron:wght@500&display=swap');

body {
  margin:0; padding:0;
  font-family: 'Orbitron', monospace;
  background:#0a0f14;
  color:#c9d1d9;
  height:100vh;
  display:flex;
  justify-content:center;
  align-items:center;
  overflow:hidden;
  position:relative;
}

body::before {
  content:"";
  position:absolute;
  top:0; left:0; right:0; bottom:0;
  background:
    linear-gradient(90deg, rgba(0,255,174,0.05) 1px, transparent 1px),
    linear-gradient(rgba(0,255,174,0.05) 1px, transparent 1px);
  background-size:60px 60px,60px 60px;
  z-index:0;
}

/* Card & sensors (green boxes) */
.card {
  position:relative;
  background:#161b22;
  border:1px solid #00ffae;
  border-radius:15px;
  padding:40px 50px;
  box-shadow:0 0 15px #00ffae80,0 0 30px #00ffae50,0 0 45px #00ffae30;
  display:flex;
  align-items:center;
  justify-content:center;
  gap:60px;
  width:800px;
  max-width:95vw;
  z-index:1;
}

.sensor-column {
  display:flex;
  flex-direction:column;
  max-width:180px;
  min-width:180px;
  justify-content:center;
  align-items:center;
}

.sensor-box {
  background:#0f161f;
  border:1px solid #00ffae;
  border-radius:15px;
  padding:20px 15px;
  box-shadow:0 0 10px #00ffae,0 0 20px #00ffae88;
  color:#00ffae;
  font-size:20px;
  text-shadow:0 0 8px #00ffae,0 0 12px #00ffae70;
  text-align:center;
  width:180px;
  height:120px;
  display:flex;
  flex-direction:column;
  justify-content:center;
  transition: opacity .35s ease, transform .2s ease;
}

.sensor-box.hidden {
  opacity: 0 !important;
  pointer-events: none;
}

.center {
  display:flex;
  flex-direction:column;
  align-items:center;
  justify-content:center;
  text-align:center;
  min-width:220px;
}

.center h2 {
  margin-bottom:20px;
  color:#00ffae;
}

.cta {
  background:#00ffae;
  color:#000;
  border:none;
  padding:12px 25px;
  font-size:17px;
  border-radius:7px;
  cursor:pointer;
  font-weight:700;
  letter-spacing:1px;
  box-shadow:0 0 15px #00ffae;
  transition: transform .2s;
}
.cta:hover{transform:translateY(-3px)}

.footer {
  margin-top:25px;
  font-size:13px;
  color:#8b949e;
  text-align:center;
  position:absolute;
  bottom:15px;
  width:100%;
  left:0;
  z-index:1;
}

/* ---------------- EXTREME SCARE OVERLAY ---------------- */
#scareOverlay {
  position:fixed; inset:0;
  display:none;
  z-index:9999;
  background:#000;
  align-items:center;
  justify-content:center;
  overflow:hidden;
}

/* spam left */
.spamColumn {
  position:absolute; left:0; top:0; height:100%; width:40%; z-index:6; pointer-events:none;
  display:flex; align-items:flex-start; justify-content:flex-start; padding:40px 16px; box-sizing:border-box;
  flex-direction:column;
  gap:6px;
}
.spamLine {
  color:#ff073a;
  font-weight:900;
  font-size:14px;
  text-transform:uppercase;
  text-shadow:0 0 8px rgba(255,0,60,0.9),0 0 22px rgba(255,0,60,0.12);
  opacity:0.95;
  white-space:nowrap;
}

/* binary right */
.binaryRain{
  position:absolute; right:0; top:0; width:22%; height:100%; z-index:5; pointer-events:none; padding:24px;
  display:flex; flex-direction:column; gap:4px; align-items:flex-end; justify-content:flex-start;
  color:#ff3a4a; font-weight:700; font-size:12px; text-shadow:0 0 8px rgba(255,0,60,0.2)
}

/* big message */
.bigmsg{
  position:absolute; left:50%; top:18%; transform:translateX(-50%);
  z-index:8; color:#ff073a; font-size:64px; font-weight:900;
  text-shadow:0 0 18px rgba(255,0,60,0.95),0 0 48px rgba(255,0,60,0.35);
  letter-spacing:2px;
}

/* flicker bars and scanlines */
.scanFlash{position:absolute;left:0;right:0;height:2px;background:linear-gradient(90deg,transparent,#ff0033,transparent);z-index:9;opacity:0.3}
.scanFlash{top:25%}
.scanFlash:nth-of-type(2){top:55%;animation-delay:.3s}

/* blood tint */
.bloodSplatter{position:absolute;inset:0;z-index:4;pointer-events:none;background-image:
  radial-gradient(circle at 20% 10%, rgba(255,0,0,0.08), transparent 5%),
  radial-gradient(circle at 80% 90%, rgba(255,0,0,0.06), transparent 8%);mix-blend-mode:screen}

/* shake animation */
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
}
</style>
</head>
<body>

<div class="card">
  <div class="sensor-column">
    <div class="sensor-box" id="sensorTemp">🌡️<br><span id="temp">-- °C</span></div>
  </div>

  <div class="center">
    <h2>Infórmate sobre nuestra carrera</h2>
    <button class="cta" id="scareBtn">Información</button>
  </div>

  <div class="sensor-column">
    <div class="sensor-box" id="sensorHum">💧<br><span id="hum">-- %</span></div>
  </div>
</div>

<div class="footer">ESP8266 Dashboard — Tema Neon</div>

<!-- overlay -->
<div id="scareOverlay">
  <div class="bloodSplatter"></div>
  <div class="spamColumn" id="spamStream"></div>
  <div class="binaryRain" id="binaryRain"></div>
  <div class="bigmsg" id="bigMsg">ESTÁS SIENDO HACKEADO</div>
  <div class="scanFlash"></div>
  <div class="scanFlash"></div>
  <div class="stream" id="streamText"></div>
</div>

<script>
/* ---------------- sensors (simulated) ---------------- */
function fetchSensorData(){
  document.getElementById('temp').textContent=(18+Math.random()*10).toFixed(1)+' °C';
  document.getElementById('hum').textContent=(30+Math.random()*30).toFixed(1)+' %';
}
setInterval(fetchSensorData,2400);
fetchSensorData();

/* ---------------- spam left ---------------- */
const spamStream=document.getElementById('spamStream');
const spamPhrases=[
  "ACCESO NO AUTORIZADO +++",
  "SISTEMA INTRUSO - IP: 192.168."+Math.floor(Math.random()*255)+"."+Math.floor(Math.random()*255),
  "ENCRIPTANDO... [███▒▒▒▒]",
  "ELIMINANDO BACKUPS...",
  "ERROR 0xDEAD",
  "CONEXION ROTA",
  "403 FORBIDDEN"
];
function startSpam(){
  spamStream.innerHTML='';
  let count=0;
  const spamTimer=setInterval(()=>{
    const line=document.createElement('div');
    line.className='spamLine';
    line.textContent=spamPhrases[Math.floor(Math.random()*spamPhrases.length)];
    spamStream.prepend(line);
    if(spamStream.children.length>40) spamStream.removeChild(spamStream.lastChild);
    count++; if(count>400) clearInterval(spamTimer);
  },50);
}

/* ---------------- binary rain ---------------- */
const binaryRain=document.getElementById('binaryRain');
function startBinary(){
  binaryRain.innerHTML='';
  const binTimer = setInterval(()=>{
    const n=document.createElement('div');
    n.textContent = (Math.random()>0.5? '1010' : '0101') + " "+Math.floor(Math.random()*9999);
    n.style.opacity=0; n.style.transform='translateY(-40px)';
    binaryRain.prepend(n);
    requestAnimationFrame(()=>{ n.style.transition='all .5s linear'; n.style.opacity=1; n.style.transform='translateY(0)'; });
    if(binaryRain.children.length>60) binaryRain.removeChild(binaryRain.lastChild);
  },80);
}

/* ---------------- stream bottom center ---------------- */
const streamText = document.getElementById('streamText');
const streamPhrases = [
  "ENCRIPTANDO SISTEMAS...",
  "INTRUSIÓN: SERVIDOR CENTRAL",
  "RESPUESTA: 0xFF00",
  "ELIMINANDO TRAZAS",
  "CONECTANDO RANSOM NET",
  "CANTIDAD DE HOSTS: "+Math.floor(Math.random()*30+3)
];
function startStream(){
  streamText.innerHTML='';
  const t = setInterval(()=>{
    const p=document.createElement('div');
    p.textContent=streamPhrases[Math.floor(Math.random()*streamPhrases.length)];
    streamText.prepend(p);
    if(streamText.children.length>8) streamText.removeChild(streamText.lastChild);
  },140);
}

/* ---------------- big messages ---------------- */
const bigMsg=document.getElementById('bigMsg');
function playSequence(){
  const msgs=["ESTÁS SIENDO HACKEADO","NO HAY SALIDA","SISTEMA COLAPSANDO","TODO HA SIDO TOMADO"];
  let i=0;
  const seq = setInterval(()=>{
    bigMsg.textContent = msgs[i % msgs.length];
    bigMsg.style.transform = `translateX(-50%) translateY(${Math.floor(Math.random()*12-6)}px) skewX(${Math.floor(Math.random()*12-6)}deg)`;
    i++;
    if(i> (msgs.length*6)) clearInterval(seq);
  },800);
}

/* ---------------- full screen "almost forced" ---------------- */
const btn=document.getElementById('scareBtn');
const overlay=document.getElementById('scareOverlay');
const sensorBoxes = document.querySelectorAll('.sensor-box');

async function enterFullScreen() {
  try {
    if(document.documentElement.requestFullscreen) await document.documentElement.requestFullscreen();
    document.addEventListener('fullscreenchange', () => {
      if (!document.fullscreenElement) {
        // try to re-enter quickly
        setTimeout(()=>{ if(document.documentElement.requestFullscreen) document.documentElement.requestFullscreen(); }, 60);
      }
    });
    document.addEventListener('keydown', (e) => {
      if (e.key === 'Escape' || e.key === 'F11') {
        e.preventDefault();
        if(document.documentElement.requestFullscreen) document.documentElement.requestFullscreen();
      }
    });
  } catch(e){ console.warn('fullscreen blocked', e); }
}

btn.addEventListener('click', async function(){
  // hide/transparent the green sensor boxes (animate)
  sensorBoxes.forEach(el=>{
    el.classList.add('hidden'); // transitions to opacity:0
  });

  // optionally slightly fade entire card too (keeps center button area hidden while overlay visible)
  document.querySelector('.card').style.transition='opacity .3s';
  document.querySelector('.card').style.opacity=0.06;

  // show overlay and start effects
  overlay.style.display='flex';
  overlay.classList.add('shakeOverlay'); overlay.classList.add('redPulse');

  startSpam(); startBinary(); startStream(); playSequence();

  // attempt to enter fullscreen and keep re-entering if user tries to exit
  await enterFullScreen();
});
</script>
</body>
</html>
