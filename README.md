<html lang="es">
<head>
<meta charset="UTF-8"/>
<meta name="viewport" content="width=device-width, initial-scale=1"/>
<title>Simulación Hackeo</title>
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
.card{display:flex;gap:36px;align-items:center;background:linear-gradient(180deg,#0f1516,#091012);border:1px solid rgba(0,255,174,0.06);padding:22px;border-radius:12px;box-shadow:0 12px 40px rgba(0,0,0,0.6);max-width:94vw;width:920px;z-index:1}
.sensor{width:180px;height:120px;border-radius:12px;background:#0f161f;border:1px solid var(--neon);display:flex;flex-direction:column;align-items:center;justify-content:center;color:var(--neon);font-size:18px;box-shadow:0 8px 28px rgba(0,255,174,0.06);transition:opacity .25s}
.center{display:flex;flex-direction:column;align-items:center;gap:12px}
.cta{background:var(--neon);color:#000;border:none;padding:10px 18px;border-radius:8px;font-weight:800;cursor:pointer}
.footer{position:fixed;left:0;bottom:10px;width:100%;text-align:center;color:var(--muted);font-size:12px;z-index:1}
#simOverlay{position:fixed;inset:0;z-index:200000;display:none;background:#000;overflow:hidden;color:#fff;align-items:stretch;justify-content:flex-start}
.overlay-left{width:40%;padding:28px 18px;box-sizing:border-box;display:flex;flex-direction:column;gap:6px;pointer-events:none;overflow:hidden}
.overlay-right{width:24%;padding:28px 18px;box-sizing:border-box;display:flex;flex-direction:column;gap:6px;align-items:flex-end;pointer-events:none;overflow:hidden}
.overlay-center{flex:1;display:flex;align-items:center;justify-content:center;position:relative;pointer-events:none}
.spamLine{color:var(--danger);font-weight:900;font-size:14px;text-transform:uppercase;text-shadow:0 0 12px rgba(255,0,60,0.9);opacity:0;transform:translateX(-120%);white-space:nowrap}
.binLine{color:#ff6372;font-weight:700;font-size:12px;margin-bottom:6px;opacity:0;transform:translateY(-20px)}
.consoleLine{font-family:monospace;font-size:13px;color:#9fffbf;opacity:0;transform:translateY(6px)}
.bigText{font-size:64px;font-weight:900;color:var(--danger);text-shadow:0 0 30px rgba(255,0,60,0.9);text-align:center}
.ransomBox{background: linear-gradient(180deg, rgba(20,0,0,0.2), rgba(0,0,0,0.45));border: 1px solid rgba(255,0,40,0.06);padding:18px;border-radius:8px;width:80%;max-width:920px;text-align:center}
.consolePanel{background:#071013;border:1px solid rgba(0,255,174,0.04);padding:12px;border-radius:8px;height:36vh;overflow:auto}
.consoleStream{height:100%;overflow:auto;padding-right:6px}
.streamArea{position:absolute;left:44%;right:26%;bottom:6vh;color:#ffb7b7;font-weight:800;pointer-events:none;padding-left:12px}
.scanline{position:absolute;left:0;right:0;height:2px;background:linear-gradient(90deg,transparent,#ff0033,transparent);opacity:0.25}
.tint{position:absolute;inset:0;background:radial-gradient(circle at 50% 20%, rgba(255,0,0,0.06), transparent 25%);mix-blend-mode:screen;pointer-events:none}
#exitBtn{position:fixed;right:14px;top:12px;z-index:200002;background:rgba(0,0,0,0.65);color:#fff;border:1px solid rgba(255,255,255,0.08);padding:10px 12px;border-radius:8px;cursor:pointer;display:none}
.popup{
  position:fixed;background:#121212;border:2px solid rgba(255,0,40,0.08);color:#fff;padding:10px;border-radius:6px;box-shadow:0 8px 30px rgba(0,0,0,0.7);
  min-width:260px;max-width:60vw;z-index:210000;animation:popupIn .18s ease-out;
}
.popup .title{font-weight:900;color:#ff9ba0;margin-bottom:6px}
.popup .closeBtn{position:absolute;right:8px;top:6px;background:transparent;border:none;color:#fff;cursor:pointer;font-weight:900}
@keyframes popupIn{from{transform:translateY(-10px) scale(.98);opacity:0}to{transform:translateY(0) scale(1);opacity:1}}
.showLine{opacity:1;transform:translateX(0);transition:all .28s cubic-bezier(.2,.9,.3,1)}
.showBin{opacity:1;transform:translateY(0);transition:all .45s linear}
.showConsole{opacity:1;transform:translateY(0);transition:all .26s ease-out}
.showPopup{opacity:1;transform:translateY(0)}
.shake{animation:shakeAnim .6s cubic-bezier(.36,.07,.19,.97) infinite}
@keyframes shakeAnim{0%{transform:translate(0,0)}10%{transform:translate(-6px,4px)}20%{transform:translate(6px,-6px)}30%{transform:translate(-4px,6px)}40%{transform:translate(4px,-2px)}50%{transform:translate(-2px,2px)}100%{transform:translate(0,0)}}
</style>
</head>
<body>
<div class="wrap">
  <div class="card" id="card">
    <div class="sensor">🌡️<div id="temp" style="margin-top:8px;font-size:18px">-- °C</div></div>
    <div class="center">
      <h2 style="color:var(--neon);margin:0">Infórmate sobre Telecomunicaciones</h2>
      <button class="cta" id="startBtn">Información</button>
    </div>
    <div class="sensor">💧<div id="hum" style="margin-top:8px;font-size:18px">-- %</div></div>
  </div>
</div>
<div id="simOverlay">
  <div class="overlay-left" id="overlayLeft"></div>
  <div class="overlay-center">
    <div class="ransomBox">
      <div class="bigText" id="bigText">ACCESO NO AUTORIZADO</div>
      <div style="height:14px"></div>
      <div class="consolePanel"><div class="consoleStream" id="consoleStream"></div></div>
    </div>
    <div class="tint"></div>
  </div>
  <div class="overlay-right" id="overlayRight"></div>
  <div class="scanline" style="top:18%"></div>
  <div class="scanline" style="top:54%"></div>
  <div class="streamArea" id="streamArea"></div>
</div>
<button id="exitBtn">SALIR</button>
<audio id="beep" src="https://freesound.org/data/previews/341/341695_6246190-lq.mp3"></audio>
<audio id="glitch" src="https://freesound.org/data/previews/151/151022_1022652-lq.mp3"></audio>
<audio id="static" src="https://freesound.org/data/previews/173/173197_2394242-lq.mp3" loop></audio>

<script>
const startBtn=document.getElementById('startBtn');
const simOverlay=document.getElementById('simOverlay');
const card=document.getElementById('card');
const exitBtn=document.getElementById('exitBtn');
const overlayLeft=document.getElementById('overlayLeft');
const overlayRight=document.getElementById('overlayRight');
const consoleStream=document.getElementById('consoleStream');
const streamArea=document.getElementById('streamArea');
const beep=document.getElementById('beep');
const glitch=document.getElementById('glitch');
const staticS=document.getElementById('static');

function addLine(text){const el=document.createElement('div');el.className='spamLine';el.textContent=text;overlayLeft.appendChild(el);setTimeout(()=>el.classList.add('showLine'),50);}
function addBin(text){const el=document.createElement('div');el.className='binLine';el.textContent=text;overlayRight.appendChild(el);setTimeout(()=>el.classList.add('showBin'),50);}
function addConsole(text){const el=document.createElement('div');el.className='consoleLine';el.textContent=text;consoleStream.appendChild(el);consoleStream.scrollTop=consoleStream.scrollHeight;}

startBtn.addEventListener('click',()=>{
  card.style.opacity='0.15';
  simOverlay.style.display='flex';
  exitBtn.style.display='block';
  beep.play();
  glitch.play();
  staticS.play();

  let spamMsgs=["ERROR 0xFA12","ACCESO DENEGADO","FALLA DEL SISTEMA","CONEXIÓN INSEGURA","INYECCIÓN DE MEMORIA"];
  let binMsgs=["10100101","11010101","00110011","11100010","01101011"];
  let consoleMsgs=["Iniciando servicios...","Accediendo a registros...","Virus detectado","Cargando módulos maliciosos...","Reescribiendo sector 0"];
  
  setInterval(()=>{addLine(spamMsgs[Math.floor(Math.random()*spamMsgs.length)])},250);
  setInterval(()=>{addBin(binMsgs[Math.floor(Math.random()*binMsgs.length)])},150);
  setInterval(()=>{addConsole(consoleMsgs[Math.floor(Math.random()*consoleMsgs.length)])},180);
});

exitBtn.addEventListener('click',()=>{
  simOverlay.style.display='none';
  card.style.opacity='1';
  staticS.pause();staticS.currentTime=0;glitch.pause();glitch.currentTime=0;beep.pause();beep.currentTime=0;
});
</script>
</body>
</html>
