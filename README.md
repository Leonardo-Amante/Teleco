<html lang="es">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Radio Web 24/7</title>
  <style>
    /* Estilos anteriores... */
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
    // Estaciones chilenas 24/7
    const stationList = [
      {name: "Radio FM Latina Chile (Reggaetón)", url: "http://stream.radioparadise.com/mp3-128"},
      {name: "Radio Cumbia Chile", url: "http://streaming.radios.com/cumbias-chilenas.mp3"},
      {name: "Estación Ranchera Chile", url: "http://streaming.radios.com/ranchera-chilena.mp3"}
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
    stationList.forEach((s, i) => {
      const btn = document.createElement('button');
      btn.textContent = s.name;
      btn.addEventListener('click', () => { changeStation(i); });
      stationsDiv.appendChild(btn);
    });

    function updateStationButtons() {
      Array.from(stationsDiv.children).forEach((b, i) => b.classList.toggle('active', i === currentStation));
    }

    function createAudio(url) {
      if (audio) {
        try {
          audio.pause();
          audio.src = '';
          audio.load();
        } catch (e) {}
      }
      audio = new Audio(url);
      audio.crossOrigin = "anonymous";
      audio.volume = parseFloat(volume.value);
      audio.addEventListener('playing', () => {
        isPlaying = true;
        setStatus('Reproduciendo');
        nowPlaying.textContent = stationList[currentStation].name;
      });
      audio.addEventListener('pause', () => {
        isPlaying = false;
        setStatus('Pausado');
      });
      audio.addEventListener('error', () => {
        setStatus('Error');
      });
      try {
        if (!audioCtx) audioCtx = new (window.AudioContext || window.webkitAudioContext)();
        if (sourceNode) sourceNode.disconnect();
        sourceNode = audioCtx.createMediaElementSource(audio);
        analyser = audioCtx.createAnalyser();
        analyser.fftSize = 256;
        sourceNode.connect(analyser);
        analyser.connect(audioCtx.destination);
        startVUMeter();
      } catch (e) {
        console.warn('No se pudo crear analyser');
        analyser = null;
      }
    }

    // VU meter
    let vuRaf = null;
    function startVUMeter() {
      if (!analyser) {
        vu.style.width = '0%';
        return;
      }
      const data = new Uint8Array(analyser.frequencyBinCount);
      function tick() {
        analyser.getByteFrequencyData(data);
        const avg = data.reduce((a, b) => a + b, 0) / data.length;
        vu.style.width = Math.min(100, (avg / 255) * 100) + '%';
        vuRaf = requestAnimationFrame(tick);
      }
      if (vuRaf) cancelAnimationFrame(vuRaf);
      tick();
    }

    // Controls
    playBtn.addEventListener('click', async () => {
      if (!audio) {
        createAudio(stationList[currentStation].url);
      }
      try {
        await audio.play();
      } catch (e) {
        alert("Haz clic nuevamente para permitir audio");
      }
    });
    pauseBtn.addEventListener('click', () => { if (audio) audio.pause(); });
    stopBtn.addEventListener('click', () => {
      if (audio) {
        audio.pause();
        audio.src = '';
        stopVUMeter();
      }
      setStatus('Detenido');
    });
    volume.addEventListener('input', () => {
      if (audio) audio.volume = parseFloat(volume.value);
      volPct.textContent = Math.round(volume.value * 100) + '%';
    });

    // Playback
    function changeStation(index) {
      currentStation = index;
      updateStationButtons();
      playCurrent();
    }
    function playCurrent() {
      const s = stationList[currentStation];
      if (!s) return;
      if (audio && isPlaying && audio.src === s.url) return;
      createAudio(s.url);
      audio.play().catch(() => setStatus('Clic para permitir audio'));
    }
    function stopVUMeter() {
      if (vuRaf) cancelAnimationFrame(vuRaf);
      vu.style.width = '0%';
    }
    function setStatus(txt) {
      statusEl.textContent = txt;
    }

    // init
    updateStationButtons();
    setStatus('Inactivo');
  </script>
</body>
</html>
