<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8">
  <title>MQTT Sender</title>
  <style>
    @import url('https://fonts.googleapis.com/css2?family=Orbitron:wght@500&display=swap');

    body {
      margin: 0; padding: 0;
      font-family: 'Orbitron', monospace;
      background: #0a0f14;
      color: #c9d1d9;
      height: 100vh;
      display: flex;
      justify-content: center;
      align-items: center;
      overflow: hidden;
      position: relative;
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
      padding: 40px 50px;
      box-shadow: 0 0 15px #00ffae80, 0 0 30px #00ffae50, 0 0 45px #00ffae30;
      display: flex;
      align-items: center;
      justify-content: center;
      gap: 60px;
      width: 800px;
      max-width: 95vw;
      z-index: 1;
      flex-direction: column;
    }

    .sensor-column {
      display: flex;
      flex-direction: column;
      max-width: 180px;
      min-width: 180px;
      justify-content: center;
      align-items: center;
    }

    .message-box {
      width: 100%;
      display: flex;
      flex-direction: column;
      align-items: center;
    }

    h2 {
      margin-bottom: 25px;
      color: #00ffae;
      text-shadow: 0 0 5px #00ffae, 0 0 10px #00ffae, 0 0 20px #00ffae;
      text-align: center;
      width: 100%;
    }

    form {
      display: flex;
      flex-direction: column;
      width: 100%;
      align-items: center;
    }

    input[type=text] {
      width: 250px;
      padding: 12px 15px;
      border: none;
      border-radius: 7px;
      margin-bottom: 20px;
      font-size: 17px;
      background: #0f161f;
      color: #c9d1d9;
      box-shadow: inset 0 0 5px #00ffae88;
      transition: box-shadow 0.3s ease;
    }

    input[type=text]:focus {
      outline: none;
      box-shadow: 0 0 10px #00ffae, inset 0 0 10px #00ffaecc;
    }

    input[type=submit] {
      background: #00ffae;
      color: #000;
      border: none;
      padding: 12px 25px;
      font-size: 17px;
      border-radius: 7px;
      cursor: pointer;
      transition: background 0.3s, box-shadow 0.3s;
      box-shadow: 0 0 15px #00ffae;
      font-weight: 700;
      letter-spacing: 1px;
    }

    input[type=submit]:hover {
      background: #00ffaa;
      box-shadow: 0 0 30px #00ffae;
    }

    .sensor-box {
      background: #0f161f;
      border: 1px solid #00ffae;
      border-radius: 15px;
      padding: 20px 15px;
      box-shadow: 0 0 10px #00ffae, 0 0 20px #00ffae88;
      color: #00ffae;
      font-size: 20px;
      text-shadow: 0 0 8px #00ffae, 0 0 12px #00ffae70;
      text-align: center;
      max-width: 180px;
      min-width: 180px;
      user-select: none;
      margin-bottom: 15px;
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

    /* Contenedor del video */
    #videoContainer iframe {
      margin-top: 20px;
      width: 560px;
      height: 315px;
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
  <div class="sensor-column">
    <div class="sensor-box">
      🌡️ Temperatura:<br><span id="temp">--</span> °C
    </div>
    <div class="sensor-box">
      💧 Humedad:<br><span id="hum">--</span> %
    </div>
  </div>

  <div class="message-box">
    <h2>📡 Enviar un mensaje</h2>
    <form onsubmit="playVideo(); return false;">
      <input type="text" name="msg" placeholder="Escribe tu mensaje..." required>
      <input type="submit" value="Enviar">
    </form>
    <div id="videoContainer"></div>
  </div>
</div>

<div class="footer">ESP8266 MQTT Web Sender — Tema Electrónico Neon</div>

<script>
  // Simula datos de sensor
  function fetchSensorData(){
    document.getElementById('temp').textContent = (20 + Math.random()*5).toFixed(1);
    document.getElementById('hum').textContent = (40 + Math.random()*10).toFixed(1);
  }
  setInterval(fetchSensorData, 3000);
  fetchSensorData();

  // Incrusta y reproduce el video dentro de la página
  function playVideo() {
    const videoContainer = document.getElementById('videoContainer');
    videoContainer.innerHTML = `
      <iframe 
        src="https://www.youtube.com/embed/Ae2uKbFbt1U?autoplay=1&mute=1&rel=0" 
        title="YouTube video player" 
        allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" 
        allowfullscreen>
      </iframe>
    `;
  }
</script>

</body>
</html>
