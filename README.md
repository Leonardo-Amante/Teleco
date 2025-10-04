<html lang="es">
<head>
  <meta charset="UTF-8">
  <title>Telecomunicaciones Dashboard</title>
  <style>
    @import url('https://fonts.googleapis.com/css2?family=Orbitron:wght@500&display=swap');

    html, body {
      margin: 0; padding: 0;
      height: 100%;
      font-family: 'Orbitron', monospace;
      background: #0a0f14;
      color: #c9d1d9;
      overflow: hidden;
    }

    body::before {
      content: "";
      position: absolute;
      top: 0; left: 0; right: 0; bottom: 0;
      background:
        linear-gradient(90deg, rgba(0,255,174,0.05) 1px, transparent 1px),
        linear-gradient(rgba(0,255,174,0.05) 1px, transparent 1px),
        radial-gradient(circle at 25% 75%, rgba(0,255,174,0.1), transparent 50%),
        radial-gradient(circle at 75% 25%, rgba(0,255,174,0.1), transparent 50%);
      background-size: 60px 60px, 60px 60px, 200px 200px, 200px 200px;
      animation: moveLines 60s linear infinite;
      z-index: 0;
    }

    @keyframes moveLines {
      0% {background-position: 0 0, 0 0, 0 0, 0 0;}
      100% {background-position: 60px 60px, 60px 60px, 0 0, 0 0;}
    }

    .card {
      position: relative;
      background: #161b22;
      border: 1px solid #00ffae;
      border-radius: 15px;
      padding: 30px 40px;
      box-shadow: 0 0 15px #00ffae80, 0 0 30px #00ffae50, 0 0 45px #00ffae30;
      display: flex;
      justify-content: space-around;
      align-items: center;
      width: 900px;
      max-width: 95vw;
      z-index: 1;
      margin: auto;
      top: 50%;
      transform: translateY(50%);
      flex-wrap: wrap;
    }

    .sensor-box {
      background: #0f161f;
      border: 1px solid #00ffae;
      border-radius: 15px;
      padding: 20px 25px;
      box-shadow: 0 0 10px #00ffae, 0 0 20px #00ffae88;
      color: #00ffae;
      font-size: 20px;
      text-shadow: 0 0 8px #00ffae, 0 0 12px #00ffae70;
      text-align: center;
      width: 180px;
      margin: 10px;
      user-select: none;
    }

    .message-box {
      display: flex;
      flex-direction: column;
      align-items: center;
      text-align: center;
      margin: 10px;
    }

    .message-box h2 {
      margin-bottom: 20px;
      color: #00ffae;
      font-size: 24px;
      text-shadow: 0 0 5px #00ffae, 0 0 10px #00ffae, 0 0 20px #00ffae;
    }

    .message-box button {
      padding: 12px 30px;
      font-size: 18px;
      font-weight: bold;
      background: #00ffae;
      color: #000;
      border: none;
      border-radius: 10px;
      cursor: pointer;
      box-shadow: 0 0 15px #00ffae;
    }

    .message-box button:hover {
      background: #00ffaa;
      box-shadow: 0 0 30px #00ffae;
    }

    .footer {
      margin-top: 25px;
      font-size: 13px;
      color: #8b949e;
      text-align: center;
      position: absolute;
      bottom: 15px;
      width: 100%;
      left: 0;
      z-index: 1;
      user-select: none;
    }

    .electronic-dot {
      width: 10px;
      height: 10px;
      background: #00ffae;
      border-radius: 50%;
      box-shadow: 0 0 8px #00ffae, 0 0 15px #00ffae80;
      position: absolute;
      animation: pulse 3s infinite alternate;
    }

    .electronic-dot:nth-child(1) { top: 10%; left: 5%; animation-delay: 0s; }
    .electronic-dot:nth-child(2) { top: 30%; right: 8%; animation-delay: 1.5s; }
    .electronic-dot:nth-child(3) { bottom: 15%; left: 12%; animation-delay: 0.8s; }
    .electronic-dot:nth-child(4) { bottom: 25%; right: 15%; animation-delay: 2.3s; }

    @keyframes pulse {
      from { box-shadow: 0 0 8px #00ffae, 0 0 15px #00ffae80; }
      to { box-shadow: 0 0 15px #00ffae, 0 0 30px #00ffaecc; }
    }

    /* Video fullscreen */
    #videoContainer {
      position: fixed;
      top: 0;
      left: 0;
      width: 100%;
      height: 100%;
      z-index: 9999;
      display: none;
    }

    #videoContainer iframe {
      width: 100%;
      height: 100%;
      border: none;
    }
  </style>
</head>
<body>

<div class="electronic-dot"></div>
<div class="electronic-dot"></div>
<div class="electronic-dot"></div>
<div class="electronic-dot"></div>

<div class="card">
  <div class="sensor-box">
    🌡️ Temperatura:<br><span id="temp">--</span> °C
  </div>
  <div class="sensor-box">
    💧 Humedad:<br><span id="hum">--</span> %
  </div>

  <div class="message-box">
    <h2>Infórmate sobre nuestra carrera Telecomunicaciones</h2>
    <button onclick="playVideo()">Información</button>
  </div>
</div>

<div class="footer">ESP8266 MQTT Web Sender — Tema Electrónico Neon</div>

<div id="videoContainer"></div>

<script>
  // Actualiza sensores cada 3 segundos
  function fetchSensorData(){
    document.getElementById('temp').textContent = (20 + Math.random()*5).toFixed(1);
    document.getElementById('hum').textContent = (40 + Math.random()*10).toFixed(1);
  }
  setInterval(fetchSensorData, 3000);
  fetchSensorData();

  // Reproduce video fullscreen sin controles
  function playVideo() {
    const container = document.getElementById('videoContainer');
    container.style.display = 'block';
    container.innerHTML = `
      <iframe 
        src="https://www.youtube.com/embed/Ae2uKbFbt1U?autoplay=1&controls=0&modestbranding=1&rel=0&fs=1&disablekb=1" 
        allow="autoplay; fullscreen; encrypted-media" allowfullscreen>
      </iframe>
    `;
  }
</script>

</body>
</html>
