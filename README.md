<html lang="es">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<title>Radio Web 24/7</title>
<style>
:root{
  --bg:#071126;
  --card:#0f1724;
  --accent:#00c2a8;
  --muted:#98a6b3;
}
body{
  margin:0;
  font-family: 'Courier New', monospace;
  background: linear-gradient(180deg,var(--bg),#020612);
  color:#e6f2f1;
  height:100vh;
  display:flex;
  justify-content:center;
  align-items:center;
}
.card{
  width:900px;
  max-width:95vw;
  display:grid;
  grid-template-columns:1fr 300px;
  gap:20px;
  padding:28px;
  border-radius:12px;
  background: linear-gradient(180deg,var(--card),#06101a);
  box-shadow:0 10px 40px rgba(0,0,0,0.6);
}
h1{
  margin:0 0 10px;
  font-size:22px;
  color:var(--accent);
}
.lead{
  color:var(--muted);
}
.left{display:flex;flex-direction:column;gap:12px;}
.controls{display:flex;gap:10px;flex-wrap:wrap;align-items:center;margin-top:10px;}
.bigBtn{
  background:var(--accent);
  color:#002322;
  border:none;
  padding:12px 18px;
  border-radius:10px;
  font-weight:700;
  cursor:pointer;
  box-shadow:0 6px 20px rgba(0,194,168,0.14);
}
.smallBtn{
  background:transparent;
  border:1px solid rgba(255,255,255,0.06);
  color:#fff;
  padding:8px 10px;
  border-radius:8px;
  cursor:pointer;
}
.status{font-size:13px;color:var(--muted);}
.right{
  background:linear-gradient(180deg,#08121a,#07111a);
  padding:18px;
  border-radius:10px;
  border:1px solid rgba(255,255,255,0.025);
}
.meta{font-size:14px;color:#fff;margin-top:10px;}
.meter{width:100%;height:8px;background:rgba(255,255,255,0.03);border-radius:999px;margin-top:10px;overflow:hidden;}
.meter > i{display:block;height:100%;width:0%;background:linear-gradient(90deg,var(--accent),#00f0d0);transition:width .5s}
.volume{display:flex;gap:8px;align-items:center;margin-top:10px;}
input[type="range"]{appearance:none;height:6px;background:transparent;width:140px;}
input[type="range"]::-webkit-slider-thumb{appearance:none;width:14px;height:14px;border-radius:50%;background:var(--accent);box-shadow:0 2px 8px rgba(0,194,168,0.25);}
.footer{grid-column:1/-1;margin-top:18px;color:var(--muted);font-size:12px;}
.stationList{margin-top:12px;display:flex;flex-direction:column;gap:6px;}
.stationList button{background:#0a101a;color:#00ffa0;border:none;padding:6px 10px;border-radius:8px;cursor:pointer;text-align:left;font-size:14px;}
.stationList button.active{background:var(--accent);color:#002322;font-weight:700;}
@media(max-width:880px){.card{grid-template-columns:1fr;}.right{order:-1;}}
</style>
</head>
<body>
<div class="card">
  <div class="left">
    <h1>Radio Web 24/7</h1>
    <p class="lead">Presiona <strong>Escuchar</strong> para iniciar la radio. Cambia de estación usando la lista.</p>

    <div class="controls">
      <button id="playBtn" class="bigBtn">Escuchar</button>
      <button id="pauseBtn" class="smallBtn">Pausar</button>
      <button id="stopBtn" class="smallBtn">Detener</button>
      <div class="status">Estado: <span id="status">Inactivo</span></div>
    </div>

    <label for="volume">Volumen</label>
    <div class="volume">
      <input type="range" id="volume" min="0" max="1" step="0.01" value="0.8"/>
      <span id="volPct">80%</span>
    </div>

    <div class="stationList" id="stations">
      <!-- Lista de estaciones -->
    </div>
  </div>

  <aside class="right">
    <div><strong>Estación actual:</strong></div>
    <div class="meta" id="nowPlaying">—</div>
    <div class="meter"><i id="vu"></i></div>
  </aside>

  <div class="footer">
    Compatible con GitHub Pages. Haz clic en Escuchar para permitir audio.
  </div>
</div>

<script>
// Estaciones reales 24/7
const stationList = [
  {name:"Radio Paradise - Rock/Pop", url:"http://stream.radioparadise.com/mp3-128"},
  {name:"BBC Radio 1", url:"http://stream.live.vc.bbcmedia.co.uk/bbc_radio_one"},
  {name:"Smooth Jazz Florida", url:"http://icecast.omroep.nl/radio1-bb-mp3"} 
];

const stationsDiv = document.getElementById('stations');
const nowPlaying = document.getElementById('nowPlaying');
const playBtn = document.getElementById('playBtn');
const pauseBtn = document.getElementById('pauseBtn');
const stopBtn = document.getElementById('stopBtn');
const statusEl = document.getElementById('status');
const volume = document.getElementById('volume');
const volPct = document.getElementById('volPct');
const vu = document.getElementById('vu');

let audio = null;
let audioCtx = null;
let analyser = null;
let sourceNode = null;
let currentStation = 0;
let isPlaying = false;

// Render lista
stationList.forEach((s,i)=>{
  const btn = document.createElement('button');
  btn.textContent = s.name;
  btn.addEventListener('click',()=>{changeStation(i)});
  stationsDiv.appendChild(btn);
});

function updateStationButtons(){
  Array.from(stationsDiv.children).forEach((b,i)=>b.classList.toggle('active',i===currentStation));
}

function createAudio(url){
  if(audio){try{audio.pause();audio.src='';audio.load();}catch(e){}}
  audio = new Audio(url);
  audio.crossOrigin="anonymous";
  audio.volume = parseFloat(volume.value);
  audio.addEventListener('playing',()=>{isPlaying=true;setStatus('Reproduciendo');nowPlaying.textContent=stationList[currentStation].name;});
  audio.addEventListener('pause',()=>{isPlaying=false;setStatus('Pausado');});
  audio.addEventListener('error',()=>{setStatus('Error');});
  try{
    if(!audioCtx) audioCtx = new (window.AudioContext||window.webkitAudioContext)();
    if(sourceNode) sourceNode.disconnect();
    sourceNode = audioCtx.createMediaElementSource(audio);
    analyser = audioCtx.createAnalyser();
    analyser.fftSize = 256;
    sourceNode.connect(analyser);
    analyser.connect(audioCtx.destination);
    startVUMeter();
  }catch(e){console.warn('No se pudo crear analyser');analyser=null;}
}

// VU meter
let vuRaf=null;
function startVUMeter(){
  if(!analyser){vu.style.width='0%';return;}
  const data = new Uint8Array(analyser.frequencyBinCount);
  function tick(){
    analyser.getByteFrequencyData(data);
    const avg = data.reduce((a,b)=>a+b,0)/data.length;
    vu.style.width = Math.min(100,(avg/255)*100)+'%';
    vuRaf=requestAnimationFrame(tick);
  }
  if(vuRaf) cancelAnimationFrame(vuRaf);
  tick();
}

// Controls
playBtn.addEventListener('click', async ()=>{
    if(!audio){createAudio(stationList[currentStation].url);}
    try {await audio.play();} catch(e){alert("Haz clic nuevamente para permitir audio");}
});
pauseBtn.addEventListener('click',()=>{if(audio) audio.pause();});
stopBtn.addEventListener('click',()=>{if(audio){audio.pause();audio.src='';stopVUMeter();}setStatus('Detenido');});
volume.addEventListener('input',()=>{if(audio) audio.volume=parseFloat(volume.value);volPct.textContent=Math.round(volume.value*100)+'%';});

// Playback
function changeStation(index){currentStation=index;updateStationButtons();playCurrent();}
function playCurrent(){const s=stationList[currentStation];if(!s) return;if(audio && isPlaying && audio.src===s.url) return;createAudio(s.url);audio.play().catch(()=>setStatus('Clic para permitir audio'));}
function stopVUMeter(){if(vuRaf) cancelAnimationFrame(vuRaf);vu.style.width='0%';}
function setStatus(txt){statusEl.textContent=txt;}

// init
updateStationButtons();
setStatus('Inactivo');
</script>
</body>
</html>
