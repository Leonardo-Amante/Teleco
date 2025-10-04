<html lang="es">
<head>
<meta charset="utf-8" />
<meta name="viewport" content="width=device-width,initial-scale=1" />
<title>Radio Web — Escucha 24/7</title>
<style>
  :root{
    --bg:#071126; --card:#0f1724; --accent:#00c2a8; --muted:#98a6b3; --danger:#ff4d6d;
  }
  html,body{height:100%;margin:0;font-family:Inter,ui-sans-serif,system-ui,-apple-system,"Segoe UI",Roboto,"Helvetica Neue",Arial;color:#e6f2f1;background:linear-gradient(180deg,var(--bg),#020612)}
  .wrap{min-height:100%;display:flex;align-items:center;justify-content:center;padding:28px;box-sizing:border-box}
  .card{width:960px;max-width:96vw;background:linear-gradient(180deg,var(--card),#06101a);border-radius:12px;padding:28px;box-shadow:0 10px 40px rgba(0,0,0,0.6);display:grid;grid-template-columns:1fr 360px;gap:20px;align-items:start}
  h1{margin:0 0 8px;font-size:20px;color:var(--accent)}
  p.lead{margin:0 0 18px;color:var(--muted)}
  .left{padding-right:6px}
  .controls{display:flex;gap:10px;align-items:center;margin-top:10px;flex-wrap:wrap}
  .bigBtn{background:var(--accent);color:#002322;border:none;padding:12px 18px;border-radius:10px;font-weight:700;cursor:pointer;box-shadow:0 6px 20px rgba(0,194,168,0.14)}
  .smallBtn{background:transparent;border:1px solid rgba(255,255,255,0.06);color:#fff;padding:8px 10px;border-radius:8px;cursor:pointer}
  .status{font-size:13px;color:var(--muted);margin-top:8px}
  .streamInput{width:100%;padding:10px;border-radius:8px;border:1px solid rgba(255,255,255,0.04);background:transparent;color:#e6f2f1}
  .right{background:linear-gradient(180deg,#08121a,#07111a);padding:18px;border-radius:10px;border:1px solid rgba(255,255,255,0.025)}
  label{display:block;font-size:13px;color:var(--muted);margin-bottom:6px}
  .meta{font-size:14px;color:#fff;margin-top:10px}
  .meter{width:100%;height:8px;background:rgba(255,255,255,0.03);border-radius:999px;overflow:hidden;margin-top:10px}
  .meter > i{display:block;height:100%;width:0%;background:linear-gradient(90deg,var(--accent),#00f0d0);transition:width .5s}
  .volume{display:flex;gap:8px;align-items:center}
  .footer{grid-column:1/-1;margin-top:18px;color:var(--muted);font-size:12px}
  input[type="range"]{appearance:none;height:6px;background:transparent;width:140px}
  input[type="range"]::-webkit-slider-thumb{appearance:none;width:14px;height:14px;border-radius:50%;background:var(--accent);box-shadow:0 2px 8px rgba(0,194,168,0.25)}
  .small{font-size:13px;color:var(--muted)}
  .error{color:var(--danger);font-weight:700}
  @media (max-width:880px){ .card{grid-template-columns:1fr; } .right{order:-1} }
</style>
</head>
<body>
  <div class="wrap">
    <div class="card" role="application" aria-label="Radio web">
      <div class="left">
        <h1>Radio Web</h1>
        <p class="lead">Presiona <strong>Escuchar la radio</strong> para iniciar la transmisión. Puedes pegar la URL de tu stream (Icecast/SHOUTcast o un MP3/AAC directo).</p>

        <label for="streamUrl" class="small">URL del stream (opcional)</label>
        <input id="streamUrl" class="streamInput" placeholder="p. ej. https://mi-servidor:8000/stream.mp3" aria-label="URL del stream">

        <div class="controls" style="margin-top:14px">
          <button id="playBtn" class="bigBtn" aria-live="polite">Escuchar la radio</button>
          <button id="pauseBtn" class="smallBtn" title="Pausar">Pausa</button>
          <button id="stopBtn" class="smallBtn" title="Detener">Detener</button>
          <div style="flex:1"></div>
          <div class="small">Estado: <span id="status">Inactivo</span></div>
        </div>

        <div style="margin-top:14px" class="small">
          <div>Reintentos automáticos: <span id="retries">0</span></div>
          <div id="lastError" style="margin-top:6px;color:var(--muted)"></div>
        </div>

        <div style="margin-top:18px">
          <div class="small">Si no tienes stream, usa la URL de ejemplo incluida en la ayuda.</div>
        </div>
      </div>

      <aside class="right" aria-label="Controles y meta">
        <div><strong>Ahora:</strong></div>
        <div class="meta" id="nowPlaying">—</div>

        <div style="margin-top:12px">
          <label for="volume">Volumen</label>
          <div class="volume">
            <input id="volume" type="range" min="0" max="1" step="0.01" value="0.8" />
            <div id="volPct" class="small">80%</div>
          </div>
          <div class="meter" aria-hidden="true"><i id="vu"></i></div>
        </div>

        <div style="margin-top:12px">
          <label class="small">Ejemplos de URLs</label>
          <div class="small" style="color:var(--muted)">
            • URL de prueba (archivo MP3): <code id="example">https://file-examples.com/wp-content/uploads/2017/11/file_example_MP3_700KB.mp3</code><br>
            • Usa tu propio Icecast/SHOUTcast stream (mp3/aac/ogg).
          </div>
        </div>

        <div style="margin-top:12px">
          <button id="copyExample" class="smallBtn">Usar URL de ejemplo</button>
        </div>
      </aside>

      <div class="footer">
        Nota: la reproducción 24/7 depende de que tu servidor de streaming esté online. Los navegadores requieren interacción (clic) para permitir audio: por eso hay un botón.
      </div>
    </div>
  </div>

<script>
/*
  Código JS del player:
  - Usa elemento Audio para reproducir stream.
  - Reconecta automáticamente al detectar error/abort (reintento exponencial limitado).
  - Permite pegar URL, o usar ejemplo.
*/

const playBtn = document.getElementById('playBtn');
const pauseBtn = document.getElementById('pauseBtn');
const stopBtn = document.getElementById('stopBtn');
const streamUrlInput = document.getElementById('streamUrl');
const statusEl = document.getElementById('status');
const nowPlaying = document.getElementById('nowPlaying');
const retriesEl = document.getElementById('retries');
const lastError = document.getElementById('lastError');
const volume = document.getElementById('volume');
const volPct = document.getElementById('volPct');
const copyExample = document.getElementById('copyExample');
const exampleUrl = document.getElementById('example').textContent.trim();
const vu = document.getElementById('vu');

let audio = null;
let audioCtx = null;
let analyser = null;
let sourceNode = null;
let retryCount = 0;
let retryTimer = null;
let isPlaying = false;

// default sample (only used if input empty)
const DEFAULT_STREAM = exampleUrl;

// helper: update status text
function setStatus(s, extraClass) {
  statusEl.textContent = s;
  if (extraClass === 'err') statusEl.style.color = 'var(--danger)';
  else statusEl.style.color = '';
}

// create audio element & connect analyser for VU
function createAudioElement(url) {
  if (audio) {
    try { audio.pause(); audio.src = ''; audio.load(); } catch(e){}
    audio = null;
  }
  audio = new Audio();
  audio.crossOrigin = "anonymous";
  audio.src = url;
  audio.preload = "none";
  audio.autoplay = false;
  audio.controls = false;
  audio.loop = false;

  // events
  audio.addEventListener('playing', () => {
    isPlaying = true;
    setStatus('Reproduciendo');
    nowPlaying.textContent = url;
    retryCount = 0;
    retriesEl.textContent = retryCount;
  });
  audio.addEventListener('pause', () => {
    isPlaying = false;
    setStatus('Pausado');
  });
  audio.addEventListener('error', (e) => {
    console.warn('audio error', e);
    handleStreamError('Error al reproducir (ver consola)');
  });
  audio.addEventListener('stalled', ()=> {
    console.warn('stalled');
    handleStreamError('Stalled — intentando reconectar...');
  });
  audio.addEventListener('ended', ()=> {
    // many streams never fire 'ended' (live). If ended, try reconnect.
    handleStreamError('Stream finalizó — reconectando...');
  });

  // connect analyser for simple VU meter
  try {
    if (!audioCtx) audioCtx = new (window.AudioContext || window.webkitAudioContext)();
    if (sourceNode) try{ sourceNode.disconnect(); }catch(e){}
    sourceNode = audioCtx.createMediaElementSource(audio);
    analyser = audioCtx.createAnalyser();
    analyser.fftSize = 256;
    sourceNode.connect(analyser);
    analyser.connect(audioCtx.destination);
    startVUMeter();
  } catch (e) {
    // some browsers block createMediaElementSource if cross-origin without CORS headers.
    console.warn('No se pudo crear analyser (CORS o bloqueo):', e);
    analyser = null;
  }

  return audio;
}

// VU meter
let vuRaf = null;
function startVUMeter(){
  if (!analyser) { vu.style.width = '0%'; return; }
  const data = new Uint8Array(analyser.frequencyBinCount);
  function tick(){
    analyser.getByteFrequencyData(data);
    // compute average
    let sum = 0;
    for (let i=0;i<data.length;i++) sum += data[i];
    const avg = sum / data.length;
    const pct = Math.min(100, Math.round((avg/255) * 100));
    vu.style.width = pct + '%';
    vuRaf = requestAnimationFrame(tick);
  }
  if (vuRaf) cancelAnimationFrame(vuRaf);
  tick();
}
function stopVUMeter(){
  if (vuRaf) cancelAnimationFrame(vuRaf);
  vu.style.width = '0%';
}

// handle errors + reconnect strategy
function handleStreamError(message) {
  setStatus('Error', 'err');
  lastError.textContent = message;
  retryCount++;
  retriesEl.textContent = retryCount;
  isPlaying = false;
  stopVUMeter();
  // try reconnect with exponential backoff, max 6 tries
  if (retryCount <= 6) {
    const delay = Math.min(30000, Math.pow(2, retryCount) * 1000); // 2s,4s,8s...
    setStatus('Reconectando en ' + Math.round(delay/1000) + 's ...');
    if (audio) try { audio.pause(); audio.src = ''; audio.load(); } catch(e){}
    clearTimeout(retryTimer);
    retryTimer = setTimeout(()=> {
      startPlayback(currentStreamUrl, true);
    }, delay);
  } else {
    setStatus('No se pudo reconectar (intentos agotados)', 'err');
  }
}

let currentStreamUrl = '';

// start playback (force indicates reconnection attempt)
function startPlayback(url, force=false) {
  if (!url) url = DEFAULT_STREAM;
  currentStreamUrl = url;
  // if already playing same stream, do nothing unless force
  if (audio && !force && isPlaying && audio.src === url) {
    setStatus('Ya reproduciendo');
    return;
  }
  setStatus('Conectando...');
  lastError.textContent = '';
  // ensure user has interacted: resume AudioContext if needed
  if (audioCtx && audioCtx.state === 'suspended') {
    audioCtx.resume().catch(()=>{});
  }
  // create element and play
  try {
    createAudioElement(url);
    // attempt play (user must have clicked — we call from click)
    audio.play().then(()=> {
      // ok playing (event listener handles UI)
    }).catch(err => {
      console.warn('play() failed:', err);
      // some browsers require AudioContext resume — show message
      setStatus('Requiere interacción del usuario para reproducir (haz clic de nuevo)');
    });
  } catch (e) {
    console.error('startPlayback error', e);
    handleStreamError('Excepción al empezar reproducción');
  }
}

// pause and stop
function pausePlayback(){
  if (audio) {
    try { audio.pause(); setStatus('Pausado'); } catch(e){}
  }
}
function stopPlayback(){
  if (audio) {
    try { audio.pause(); audio.src = ''; audio.load(); } catch(e){}
  }
  currentStreamUrl = '';
  stopVUMeter();
  setStatus('Detenido');
  isPlaying = false;
}

// UI bindings
playBtn.addEventListener('click', () => {
  const url = streamUrlInput.value.trim() || DEFAULT_STREAM;
  // store url into input (if default used)
  streamUrlInput.value = url;
  // resume audio context on some browsers
  if (audioCtx && audioCtx.state === 'suspended') audioCtx.resume().catch(()=>{});
  startPlayback(url);
});

pauseBtn.addEventListener('click', ()=> { pausePlayback(); });
stopBtn.addEventListener('click', ()=> { stopPlayback(); stopSimulationCleanup(); });

// volume control
volume.addEventListener('input', (e)=>{
  const v = parseFloat(e.target.value);
  volPct.textContent = Math.round(v*100) + '%';
  if (audio) audio.volume = v;
});

// copy example stream into input
copyExample.addEventListener('click', ()=> {
  streamUrlInput.value = DEFAULT_STREAM;
  streamUrlInput.focus();
});

// when audio created, set initial volume
function onAudioCreatedSetVolume(){
  if (audio) audio.volume = parseFloat(volume.value);
}

// cleanup for stop
function stopSimulationCleanup(){
  if (audio) {
    try { audio.pause(); audio.src=''; audio.load(); } catch(e){}
  }
  if (audioCtx) {
    try { /* keep audioContext open for reuse; don't close */ } catch(e){}
  }
  retryCount = 0;
  retriesEl.textContent = '0';
  lastError.textContent = '';
}

// when audio is created we set volume - override createAudioElement to call this
// We'll monkeypatch a tiny bit: override the audio reference setter to set volume.
const _createAudioOriginal = createAudioElement;
createAudioElement = function(url){
  const a = _createAudioOriginal(url);
  onAudioCreatedSetVolume();
  return a;
};

// accessibility: keyboard Enter on input starts playback
streamUrlInput.addEventListener('keydown', (e)=>{
  if (e.key === 'Enter') playBtn.click();
});

// initial status
setStatus('Inactivo');
retriesEl.textContent = '0';
</script>
</body>
</html>
