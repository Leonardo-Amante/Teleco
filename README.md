<html lang="es">
<head>
<meta charset="utf-8"/>
<meta name="viewport" content="width=device-width,initial-scale=1"/>
<title>Simulación — Telecomunicaciones (Demo)</title>
<style>
  @import url('https://fonts.googleapis.com/css2?family=Orbitron:wght@500&display=swap');

  :root{
    --neon:#00ffae;
    --bg:#050607;
    --danger:#ff0033;
  }

  html,body{height:100%;margin:0;background:var(--bg);font-family:'Orbitron',monospace;color:#dbeee6;overflow:hidden}
  .center-wrap{height:100%;display:flex;align-items:center;justify-content:center;padding:20px;box-sizing:border-box}

  /* Dashboard (keeps green boxes look) */
  .card{
    display:flex;gap:48px;align-items:center;background:linear-gradient(180deg,#101418,#0b0d0f);
    border:1px solid rgba(0,255,174,0.07);padding:28px;border-radius:14px;box-shadow:0 10px 40px rgba(0,0,0,0.6);
    max-width:94vw;width:900px;z-index:1;
  }
  .sensor{width:180px;height:120px;border-radius:12px;background:#0f161f;border:1px solid var(--neon);
    display:flex;flex-direction:column;align-items:center;justify-content:center;color:var(--neon);font-size:18px;box-shadow:0 6px 20px rgba(0,255,174,0.06)}
  .center{display:flex;flex-direction:column;align-items:center;gap:12px}
  .cta{background:var(--neon);color:#000;border:none;padding:10px 20px;border-radius:8px;font-weight:800;cursor:pointer}
  .footer{position:fixed;left:0;bottom:10px;width:100%;text-align:center;color:#9aa6ad;font-size:12px;z-index:1}

  /* CONSENT modal (must confirm to run demo) */
  #consent {
    position:fixed; inset:0; display:flex; align-items:center; justify-content:center; z-index:200000;
    background: rgba(0,0,0,0.6); padding:20px; box-sizing:border-box;
  }
  .consentBox{
    width:min(720px,92vw); background:linear-gradient(#081014,#061014); border-radius:12px; padding:22px; color:#e6fff4;
    border:1px solid rgba(0,255,174,0.06); box-shadow:0 10px 40px rgba(0,0,0,0.7); text-align:left;
  }
  .consentBox h3{margin:0 0 8px;color:var(--neon)}
  .consentButtons{display:flex;gap:12px; margin-top:16px}

  /* SIMULATED FULLSCREEN OVERLAY (very immersive) */
  #simOverlay{
    position:fixed; inset:0; z-index:150000; display:none; background:#000; color:#fff; overflow:hidden;
  }
  .overlay-left{position:absolute;left:0;top:0;width:42%;height:100%;padding:28px;box-sizing:border-box;pointer-events:none}
  .overlay-right{position:absolute;right:0;top:0;width:26%;height:100%;padding:26px;box-sizing:border-box;pointer-events:none}
  .overlay-center{position:absolute;left:42%;right:26%;top:0;bottom:0;display:flex;align-items:center;justify-content:center;pointer-events:none}

  .spamLine{color:var(--danger);font-weight:900;font-size:14px;text-transform:uppercase;margin-bottom:6px;
    text-shadow:0 0 12px rgba(255,0,60,0.9);opacity:0;transform:translateX(-120%)}
  .binLine{color:#ff5a6a;font-weight:700;font-size:12px;margin-bottom:6px;opacity:0;transform:translateY(-20px)}

  .bigText{font-size:68px;font-weight:900;color:var(--danger);text-shadow:0 0 30px rgba(255,0,60,0.9);text-align:center;pointer-events:none}
  .watermark{
    position:fixed; left:16px; top:16px; background:rgba(255,255,255,0.06); color:#fff; padding:6px 10px; border-radius:8px;
    border:1px solid rgba(255,255,255,0.04); z-index:200001; backdrop-filter: blur(4px); font-weight:800; letter-spacing:1px;
  }

  /* always-visible exit (clear) and info */
  #exitBtn{
    position:fixed; right:16px; top:16px; z-index:200002; background:rgba(0,0,0,0.6); color:#fff; border:1px solid rgba(255,255,255,0.08);
    padding:8px 10px; border-radius:8px; cursor:pointer; font-weight:800; display:none;
  }

  /* animations when lines show */
  .showLine{opacity:1; transform:translateX(0); transition: transform .34s cubic-bezier(.2,.9,.3,1), opacity .28s}
  .showBin{opacity:1; transform:translateY(0); transition: all .45s linear}

  /* small flicker/shake */
  .shake{animation:shakeAnim .6s cubic-bezier(.36,.07,.19,.97) infinite}
  @keyframes shakeAnim{0%{transform:translate(0,0)}10%{transform:translate(-6px,4px)}20%{transform:translate(6px,-6px)}30%{transform:translate(-4px,6px)}40%{transform:translate(4px,-2px)}50%{transform:translate(-2px,2px)}100%{transform:translate(0,0)}}

  /* bottom stream */
  .streamArea{position:absolute;left:42%;right:26%;bottom:6vh;color:#ffb7b7;font-weight:800;pointer-events:none;text-align:left;padding-left:12px}

  @media (max-width:720px){
    .bigText{font-size:34px}
    .overlay-left{width:48%}
    .overlay-right{display:none}
    .overlay-center{left:48%;right:0}
    .streamArea{left:48%;right:3%}
  }
</style>
</head>
<body>
  <!-- watermark / demo label -->
  <div class="watermark">SIMULACIÓN — DEMO (uso educativo/consentido)</div>

  <div class="center-wrap">
    <div class="card" id="card">
      <div class="sensor">🌡️<div id="temp" style="font-size:20px;margin-top:6px">-- °C</div></div>
      <div class="center">
        <h2 style="color:var(--neon);margin:0">Infórmate sobre Telecomunicaciones</h2>
        <button class="cta" id="startDemo">Iniciar demo</button>
      </div>
      <div class="sensor">💧<div id="hum" style="font-size:20px;margin-top:6px">-- %</div></div>
    </div>
  </div>

  <div class="footer">ESP8266 Dashboard — Demo</div>

  <!-- Consent modal (user must confirm) -->
  <div id="consent">
    <div class="consentBox" role="dialog" aria-modal="true">
      <h3>AVISO — Modo Demostración</h3>
      <p>Vas a iniciar una <strong>simulación visual y sonora</strong> diseñada para <strong>demostración/entrenamiento</strong>. Esta experiencia está <strong>marcada como SIMULACIÓN</strong> y no afecta sistemas reales.</p>
      <p>¿Confirmas que mostrarás esto únicamente a personas que han dado su consentimiento o para uso educativo?</p>
      <div class="consentButtons">
        <button class="cta" id="consentYes">Sí, iniciar demo</button>
        <button class="cta" id="consentNo" style="background:#444;color:#fff">No, cancelar</button>
      </div>
    </div>
  </div>

  <!-- Simulated fullscreen overlay -->
  <div id="simOverlay" aria-hidden="true" role="dialog">
    <div class="overlay-left" id="overlayLeft"></div>
    <div class="overlay-right" id="overlayRight"></div>
    <div class="overlay-center">
      <div class="bigText" id="bigText">ESTÁS SIENDO DEMOSTRADO</div>
    </div>
    <div class="streamArea" id="streamArea"></div>
  </div>

  <button id="exitBtn">SALIR (demo)</button>

<script>
  /* simulated sensors */
  function updateSensors(){
    document.getElementById('temp').textContent = (18 + Math.random()*10).toFixed(1) + ' °C';
    document.getElementById('hum').textContent  = (30 + Math.random()*30).toFixed(1) + ' %';
  }
  setInterval(updateSensors, 2400);
  updateSensors();

  /* elements */
  const consent = document.getElementById('consent');
  const consentYes = document.getElementById('consentYes');
  const consentNo = document.getElementById('consentNo');
  const startDemo = document.getElementById('startDemo');
  const sim = document.getElementById('simOverlay');
  const leftCol = document.getElementById('overlayLeft');
  const rightCol = document.getElementById('overlayRight');
  const bigText = document.getElementById('bigText');
  const exitBtn = document.getElementById('exitBtn');
  const streamArea = document.getElementById('streamArea');
  const card = document.getElementById('card');

  /* spam / binary / stream phrases */
  const spamPhrases = [
    "ACCESO NO AUTORIZADO +++",
    "SISTEMA INTRUSO - IP: 192.168."+Math.floor(Math.random()*255)+"."+Math.floor(Math.random()*255),
    "ENCRIPTANDO... [███▒▒▒▒]",
    "ELIMINANDO BACKUPS...",
    "ERROR 0xDEAD",
    "CONEXION ROTA",
    "403 FORBIDDEN"
  ];
  const binChoices = ['10101010','01010101','11001100','00110011'];

  const streamPhrases = [
    "ENCRIPTANDO SISTEMAS...",
    "INTRUSIÓN: SERVIDOR CENTRAL",
    "RESPUESTA: 0xFF00",
    "ELIMINANDO TRAZAS",
    "CONECTANDO RANSOM NET",
    "HOSTS COMPROMETIDOS: " + (3 + Math.floor(Math.random()*40))
  ];

  let spamTimer, binTimer, streamTimer, flickerTimer, audioStop;

  /* helpers */
  const r = (a,b) => Math.floor(Math.random()*(b-a+1))+a;
  const pick = (arr) => arr[Math.floor(Math.random()*arr.length)];

  /* create many spam lines quickly */
  function startSpam(){
    leftCol.innerHTML = '';
    let count = 0;
    spamTimer = setInterval(()=>{
      const d = document.createElement('div');
      d.className = 'spamLine'; d.textContent = (Math.random()>0.6? '!!! ' : '') + pick(spamPhrases) + (Math.random()>0.7? ' ### '+ r(1000,99999) : '');
      leftCol.prepend(d);
      // animate in next frame
      requestAnimationFrame(()=> d.classList.add('showLine'));
      if(leftCol.children.length > 80) leftCol.removeChild(leftCol.lastChild);
      count++; if(count > 800) clearInterval(spamTimer);
    }, 42);
  }

  /* binary rain */
  function startBinary(){
    rightCol.innerHTML = '';
    binTimer = setInterval(()=>{
      const n = document.createElement('div'); n.className='binLine'; n.textContent = pick(binChoices) + ' ' + r(100,9999);
      rightCol.prepend(n);
      requestAnimationFrame(()=> n.classList.add('showBin'));
      if(rightCol.children.length > 80) rightCol.removeChild(rightCol.lastChild);
    }, 72);
  }

  /* bottom dramatic stream */
  function startStream(){
    streamArea.innerHTML = '';
    streamTimer = setInterval(()=>{
      const p = document.createElement('div'); p.textContent = pick(streamPhrases);
      streamArea.prepend(p);
      if(streamArea.children.length > 8) streamArea.removeChild(streamArea.lastChild);
    }, 120);
  }

  /* big text flicker sequence */
  function playBigSequence(){
    const msgs = ["ESTÁS SIENDO DEMOSTRADO","NO HAY SALIDA","SISTEMA COLAPSANDO","TODO HA SIDO TOMADO"];
    bigText.textContent = msgs[0];
    let i = 1;
    flickerTimer = setInterval(()=>{
      bigText.textContent = msgs[i % msgs.length];
      bigText.style.transform = `translateY(${r(-20,20)}px) skewX(${r(-8,8)}deg)`;
      i++;
      if(i > msgs.length * 6) clearInterval(flickerTimer);
    }, 900);
  }

  /* simple audio (starts only after consent click) */
  function startAudio(){
    try{
      const ctx = new (window.AudioContext || window.webkitAudioContext)();
      const baseO = ctx.createOscillator(); baseO.type='sine'; baseO.frequency.value = 35 + Math.random()*25;
      const baseG = ctx.createGain(); baseG.gain.value = 0.0025;
      baseO.connect(baseG); baseG.connect(ctx.destination); baseO.start();
      // glitch beeps
      const beep = setInterval(()=>{
        const o = ctx.createOscillator(); o.type='square'; o.frequency.value = 400 + Math.random()*1200;
        const g = ctx.createGain(); g.gain.value = 0.001 + Math.random()*0.004;
        o.connect(g); g.connect(ctx.destination); o.start();
        g.gain.exponentialRampToValueAtTime(0.00001, ctx.currentTime + 0.12 + Math.random()*0.18);
        setTimeout(()=>{ try{ o.stop(); }catch(e){} }, 300);
      }, 220 + Math.random()*300);
      audioStop = () => { clearInterval(beep); try{ baseO.stop(); ctx.close(); }catch(e){} };
    }catch(e){ audioStop = ()=>{}; }
  }

  function stopAudio(){ if(typeof audioStop === 'function') audioStop(); audioStop = null; }

  /* activate simulation overlay (consent already given) */
  function activateSim(){
    // make original sensor boxes transparent so they don't show behind overlay
    document.querySelectorAll('.sensor').forEach(s => { s.style.opacity = '0'; s.style.pointerEvents = 'none'; });
    // show watermark and overlay
    sim.style.display = 'block';
    // start effects
    startSpam(); startBinary(); startStream(); playBigSequence();
    startAudio();
    // show visible exit button after short safety delay
    setTimeout(()=> exitBtn.style.display = 'block', 8000); // appear after 8s; adjust as needed
    // prevent scroll / interactions beneath
    document.documentElement.style.overflow = 'hidden';
    document.body.style.overflow = 'hidden';
    // optional class for small shake
    sim.classList.add('shake');
  }

  function deactivateSim(){
    // cleanup intervals
    clearInterval(spamTimer); clearInterval(binTimer); clearInterval(streamTimer); clearInterval(flickerTimer);
    // hide overlay and restore sensors
    sim.style.display = 'none';
    document.querySelectorAll('.sensor').forEach(s => { s.style.opacity = ''; s.style.pointerEvents = ''; });
    stopAudio();
    exitBtn.style.display = 'none';
    // clear content
    leftCol.innerHTML=''; rightCol.innerHTML=''; streamArea.innerHTML=''; bigText.textContent='';
    document.documentElement.style.overflow = ''; document.body.style.overflow = '';
  }

  /* bind consent flow */
  document.getElementById('startDemo').addEventListener('click', ()=> { consent.style.display = 'flex'; });
  consentNo.addEventListener('click', ()=> { consent.style.display = 'none'; });
  consentYes.addEventListener('click', ()=> {
    consent.style.display = 'none';
    activateSim();
  });

  exitBtn.addEventListener('click', ()=> { deactivateSim(); });

  /* also allow Esc key to close (safety) */
  document.addEventListener('keydown', (e)=>{
    if(e.key === 'Escape') {
      if(sim.style.display === 'block') deactivateSim();
    }
  });

</script>
</body>
</html>
