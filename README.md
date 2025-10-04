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
  color: #00ffae;
  overflow: hidden;
  display: flex;
  justify-content: center;
  align-items: center;
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
  box-shadow: 0 0 20px #00ffae50, 0 0 40px #00ffae30;
  display: flex;
  justify-content: space-around;
  align-items: center;
  width: 850px;
  max-width: 95vw;
  z-index: 1;
  flex-wrap: wrap;
}

.sensor-box {
  background: linear-gradient(145deg, #0f161f, #101921);
  border: 1px solid #00ffae;
  border-radius: 12px;
  padding: 18px 25px;
  box-shadow: 0 0 12px #00ffae50, 0 0 25px #00ffae30 inset;
  color: #00ffae;
  font-size: 20px;
  text-align: center;
  width: 180px;
  height: 120px;
  margin: 10px;
  user-select: none;
  display: flex;
  flex-direction: column;
  justify-content: center;
  transition: transform 0.2s;
}

.sensor-box:hover {
  transform: scale(1.05);
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
  font-size: 22px;
  text-shadow: 0 0 5px #00ffae, 0 0 10px #00ffae, 0 0 20px #00ffae;
}

.message-box button {
  padding: 14px 32px;
  font-size: 18px;
  font-weight: bold;
  background: linear-gradient(45deg, #00ffae, #00ffaa);
  color: #000;
  border: none;
  border-radius: 12px;
  cursor: pointer;
  box-shadow: 0 0 15px #00ffae50;
  transition: all 0.3s;
}

.message-box button:hover {
  transform: translateY(-3px);
  box-shadow: 0 0 30px #00ffaa70, 0 0 40px #00ffaa50 inset;
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

#hackScreen {
  display: none;
  position: fixed;
  top: 0; left: 0;
  width: 100%; height: 100%;
  background: #000;
  color: #00ffae;
  z-index: 9999;
  font-family: 'Orbitron', monospace;
  overflow: hidden;
}

#hackScreen p {
  margin: 0;
  font-size: 22px;
  line-height: 1.5;
  animation: glitch 0.3s infinite;
}

@keyframes glitch {
  0% { opacity: 1; transform: translate(0,0); }
  20% { opacity: 0.8; transform: translate(-2px,2px); }
  40% { opacity: 1; transform: translate(2px,-2px); }
  60% { opacity: 0.8; transform: translate(-1px,1px); }
  80% { opacity: 1; transform: translate(1px,-1px); }
  100% { opacity: 1; transform: translate(0,0); }
}
</style>
</head>
<body>

<div class="card">
  <div class="sensor-box">
    🌡️ Temp:<br><span id="temp">--</span> °C
  </div>
  <div class="sensor-box">
    💧 Humedad:<br><span id="hum">--</span> %
  </div>

  <div class="message-box">
    <h2>Descubre Telecomunicaciones</h2>
    <button onclick="hackScreen()">Información</button>
  </div>
</div>

<div class="footer">ESP8266 Dashboard — Tema Neon</div>

<div id="hackScreen"></div>

<script>
function fetchSensorData(){
  document.getElementById('temp').textContent = (20 + Math.random()*5).toFixed(1);
  document.getElementById('hum').textContent = (40 + Math.random()*10).toFixed(1);
}
setInterval(fetchSensorData, 3000);
fetchSensorData();

function hackScreen() {
  const screen = document.getElementById('hackScreen');
  screen.style.display = 'block';
  screen.innerHTML = '';

  const messages = [
    "ESTÁS SIENDO HACKEADO...",
    "ACCESO NO AUTORIZADO DETECTADO",
    "INTERFERENCIA EN RED...",
    "ERROR DE SISTEMA...",
    "CONEXIÓN COMPROMETIDA..."
  ];

  // Crea mensajes al azar repetidos
  for(let i=0; i<50; i++) {
    const p = document.createElement('p');
    p.textContent = messages[Math.floor(Math.random() * messages.length)];
    p.style.position = 'absolute';
    p.style.top = Math.random()*100 + '%';
    p.style.left = Math.random()*100 + '%';
    p.style.fontSize = (14 + Math.random()*20) + 'px';
    screen.appendChild(p);
  }
}
</script>
</body>
</html>
