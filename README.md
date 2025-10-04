<html lang="es">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Radio Web 24/7</title>
  <style>
    body {
      font-family: 'Arial', sans-serif;
      background: #f4f4f9;
      margin: 0;
      padding: 0;
      display: flex;
      justify-content: center;
      align-items: center;
      height: 100vh;
      color: #333;
    }
    .container {
      background: #fff;
      border-radius: 10px;
      box-shadow: 0 4px 10px rgba(0, 0, 0, 0.1);
      overflow: hidden;
      width: 90%;
      max-width: 600px;
      text-align: center;
      padding: 20px;
    }
    h1 {
      color: #e74c3c;
      font-size: 2.5rem;
      margin-bottom: 20px;
    }
    .station-btn {
      background: #3498db;
      border: none;
      color: #fff;
      font-size: 1.2rem;
      padding: 10px 20px;
      margin: 10px;
      border-radius: 5px;
      cursor: pointer;
      transition: background 0.3s;
    }
    .station-btn:hover {
      background: #2980b9;
    }
    .controls {
      margin-top: 20px;
    }
    .controls button {
      background: #2ecc71;
      border: none;
      color: #fff;
      font-size: 1.2rem;
      padding: 10px 20px;
      margin: 10px;
      border-radius: 5px;
      cursor: pointer;
      transition: background 0.3s;
    }
    .controls button:hover {
      background: #27ae60;
    }
    .status {
      margin-top: 20px;
      font-size: 1.2rem;
      color: #95a5a6;
    }
  </style>
</head>
<body>
  <div class="container">
    <h1>Radio Web 24/7</h1>
    <p>Escucha las mejores emisoras chilenas de reggaetón, cumbia y ranchera en vivo.</p>
    <div>
      <button class="station-btn" onclick="playStation('https://stream.radioparadise.com/mp3-128')">Radio FM Latina Chile</button>
      <button class="station-btn" onclick="playStation('https://streaming.radios.com/cumbias-chilenas.mp3')">Radio Cumbia Chile</button>
      <button class="station-btn" onclick="playStation('https://streaming.radios.com/ranchera-chilena.mp3')">Estación Ranchera Chile</button>
    </div>
    <div class="controls">
      <button id="playBtn">Escuchar</button>
      <button id="pauseBtn" disabled>Pausar</button>
      <button id="stopBtn" disabled>Detener</button>
    </div>
    <div class="status" id="status">Estado: Inactivo</div>
  </div>

  <script>
    let audio = null;
    let isPlaying = false;

    function playStation(url) {
      if (audio) {
        audio.pause();
        audio.src = '';
      }
      audio = new Audio(url);
      audio.play();
      audio.onplay = () => {
        isPlaying = true;
        document.getElementById('status').innerText = 'Estado: Reproduciendo';
        document.getElementById('playBtn').disabled = true;
        document.getElementById('pauseBtn').disabled = false;
        document.getElementById('stopBtn').disabled = false;
      };
      audio.onpause = () => {
        isPlaying = false;
        document.getElementById('status').innerText = 'Estado: Pausado';
        document.getElementById('playBtn').disabled = false;
        document.getElementById('pauseBtn').disabled = true;
        document.getElementById('stopBtn').disabled = false;
      };
      audio.onended = () => {
        isPlaying = false;
        document.getElementById('status').innerText = 'Estado: Detenido';
        document.getElementById('playBtn').disabled = false;
        document.getElementById('pauseBtn').disabled = true;
        document.getElementById('stopBtn').disabled = true;
      };
    }

    document.getElementById('playBtn').addEventListener('click', () => {
      if (audio) {
        audio.play();
      }
    });

    document.getElementById('pauseBtn').addEventListener('click', () => {
      if (audio) {
        audio.pause();
      }
    });

    document.getElementById('stopBtn').addEventListener('click', () => {
      if (audio) {
        audio.pause();
        audio.src = '';
        document.getElementById('status').innerText = 'Estado: Detenido';
        document.getElementById('playBtn').disabled = false;
        document.getElementById('pauseBtn').disabled = true;
        document.getElementById('stopBtn').disabled = true;
      }
    });
  </script>
</body>
</html>
