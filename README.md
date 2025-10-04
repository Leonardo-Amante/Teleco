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

  /* giant red message */
  .bigmsg{
    position:absolute;left:50%;top:18%;transform:translateX(-50%);z-index:8;color:var(--red);font-size:64px;font-weight:900;
    text-shadow:0 0 18px rgba(255,0,60,0.95),0 0 48px rgba(255,0,60,0.35);letter-spacing:2px;pointer-events:none;
    mix-blend-mode:screen;
  }

  /* flicker bars and scanlines */
  .scanFlash{position:absolute;left:0;right:0;height:2px;background:linear-gradient(90deg,transparent,#ff0033,transparent);z-index:9;opacity:0.3}
  .scanFlash{top:25%}
  .scanFlash:nth-of-type(2){top:55%;animation-delay:.3s}

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

  @media (max-width:720px){
    .card{width:95vw;padding:16px}
    .bigmsg{font-size:32px;top:10%}
    .spamColumn{width:42%}
    .binaryRain{display:none}
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
    <div class="bigmsg" id="bigMsg" data-text="">ESTÁS SIENDO HACKEADO</div>
    <div class="scanFlash"></div>
    <div class="scanFlash"></div>
    <div class="stream" id="streamText"></div>
  </div>

<script>
function fetchSensorData(){
  document.getElementById('temp').textContent = (18 + Math.random()*10).toFixed(1) + ' °C';
  document.getElementById('hum').textContent = (30 + Math.random()*30).toFixed(1) + ' %';
}
setInterval(fetchSensorData, 2400);
fetchSensorData();

/* ----------------- spam left column ----------------- */
const spamStream = document.getElementById('spamStream');
const spamPhrases = [
  "ACCESO NO AUTORIZADO +++",
  "SISTEMA INTRUSO - IP: 192.168."+Math.floor(Math.random()*255)+"."+Math.floor(Math.random()*255),
  "ENCRIPTANDO... [███▒▒▒▒▒▒▒]",
  "ELIMINANDO BACKUPS...",
  "NO HAY RESPUESTA",
  "ERROR 0xDEAD",
  "CONEXION ROTA",
  "403 FORBIDDEN",
  "RESPUESTA: BLOQ"
];
function startSpam(){
  spamStream.innerHTML='';
  let count=0;
  const spamTimer = setInterval(()=>{
    const line = document.createElement('div');
    line.className='spamLine';
    line.textContent = spamPhrases[Math.floor(Math.random()*spamPhrases.length)];
    spamStream.prepend(line);
    if(spamStream.children.length>28) spamStream.removeChild(spamStream.lastChild);
    count++;
    if(count>200) clearInterval(spamTimer);
  },50);
}
startSpam();

/* ----------------- binary rain ----------------- */
const binaryRain = document.getElementById('binaryRain');
function startBinary(){
  binaryRain.innerHTML='';
  setInterval(()=>{
    const n = document.createElement('div');
    n.textContent = (Math.random()>0.5? '1010' : '0101') + " "+Math.floor(Math.random()*9999);
    n.style.opacity=0;n.style.transform='translateY(-40px)';
    binaryRain.prepend(n);
    requestAnimationFrame(()=>{ n.style.transition='all .5s linear'; n.style.opacity=1; n.style.transform='translateY(0)'; });
    if(binaryRain.children.length>50) binaryRain.removeChild(binaryRain.lastChild);
  },80);
}
startBinary();

/* ----------------- stream bottom center ----------------- */
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
  setInterval(()=>{
    const p = document.createElement('div');
    p.textContent = streamPhrases[Math.floor(Math.random()*streamPhrases.length)];
    streamText.prepend(p);
    if(streamText.children.length>6) streamText.removeChild(streamText.lastChild);
  },180);
}
startStream();

/* ----------------- big messages ----------------- */
const bigMsg = document.getElementById('bigMsg');
const bigMessages = [
  "ESTÁS SIENDO HACKEADO",
  "NO HAY SALIDA",
  "SISTEMA COLAPSANDO",
  "TODO HA SIDO TOMADO"
];
function playSequence(){
  let i=0;
  setInterval(()=>{
    bigMsg.textContent = bigMessages[i % bigMessages.length];
    bigMsg.style.transform = `translateX(-50%) translateY(${Math.floor(Math.random()*12-6)}px) skewX(${Math.floor(Math.random()*12-6)}deg)`;
    i++;
  },900);
}

/* ----------------- full screen forzado ----------------- */
const btn = document.getElementById('scareBtn');
const overlay = document.getElementById('scareOverlay');

async function enterFullScreen() {
    try {
        if(document.documentElement.requestFullscreen) await document.documentElement.requestFullscreen();
        document.addEventListener('fullscreenchange', () => {
            if (!document.fullscreenElement) {
                setTimeout(()=>{document.documentElement.requestFullscreen();}, 50);
            }
        });
        document.addEventListener('keydown', (e)=>{
            if(e.key==='Escape'||e.key==='F11'){ e.preventDefault(); document.documentElement.requestFullscreen(); }
        });
    } catch(e){}
}

btn.addEventListener('click', async function(){
    document.querySelector('.card').style.transition='opacity .25s';
    document.querySelector('.card').style.opacity=0.04;

    overlay.style.display='flex';
    overlay.classList.add('shakeOverlay'); overlay.classList.add('redPulse');

    startSpam(); startBinary(); startStream(); playSequence();
    enterFullScreen();
});
</script>
</body>
</html>
