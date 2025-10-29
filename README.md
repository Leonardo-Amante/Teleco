<html lang="es">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1.0" />
<title>Calculadora de Redes — Leonardo Amante</title>
<style>
:root {
  --neon1: #00f0ff;
  --neon2: #007bff;
  --bg1: #030b14;
  --bg2: #021a25;
  --text: #eaf8ff;
  --accent: #00e0ff;
  --shadow: 0 0 15px rgba(0,240,255,0.4);
}

body {
  margin: 0;
  font-family: 'Orbitron', sans-serif;
  background: linear-gradient(135deg, var(--bg1), var(--bg2));
  color: var(--text);
  overflow-x: hidden;
  min-height: 100vh;
  animation: movebg 20s infinite alternate ease-in-out;
}

@keyframes movebg {
  from { background-position: 0% 50%; }
  to { background-position: 100% 50%; }
}

header {
  text-align: center;
  padding: 30px;
  font-size: 2.3rem;
  color: var(--accent);
  text-shadow: 0 0 10px var(--neon1), 0 0 20px var(--neon2);
}

.container {
  max-width: 900px;
  margin: auto;
  background: rgba(0, 20, 30, 0.6);
  border-radius: 20px;
  border: 1px solid rgba(0,255,255,0.3);
  box-shadow: 0 0 30px rgba(0,255,255,0.1);
  padding: 30px;
  backdrop-filter: blur(12px);
}

h2 {
  color: var(--neon1);
  text-shadow: 0 0 8px var(--neon2);
  margin-bottom: 10px;
}

label {
  font-size: 1rem;
  opacity: 0.8;
}

input {
  width: 100%;
  margin-top: 5px;
  margin-bottom: 15px;
  padding: 10px;
  font-size: 1rem;
  border: none;
  border-radius: 10px;
  color: #00f7ff;
  background: rgba(255, 255, 255, 0.05);
  box-shadow: inset 0 0 10px rgba(0,255,255,0.3);
  outline: none;
}

button {
  display: block;
  width: 100%;
  margin: 10px 0;
  padding: 12px;
  font-size: 1rem;
  font-weight: bold;
  border: none;
  border-radius: 10px;
  background: linear-gradient(90deg, var(--neon1), var(--neon2));
  color: #000;
  cursor: pointer;
  box-shadow: 0 0 20px rgba(0,255,255,0.4);
  transition: transform 0.2s, box-shadow 0.3s;
}

button:hover {
  transform: scale(1.02);
  box-shadow: 0 0 25px rgba(0,255,255,0.7);
}

.results {
  margin-top: 20px;
  padding: 15px;
  background: rgba(0,0,0,0.4);
  border-radius: 15px;
  border: 1px solid rgba(0,255,255,0.2);
  box-shadow: 0 0 25px rgba(0,255,255,0.1);
}

.value {
  color: var(--accent);
  font-weight: bold;
  text-shadow: 0 0 8px var(--neon2);
}

footer {
  text-align: center;
  padding: 25px;
  color: rgba(255,255,255,0.3);
  font-size: 0.9rem;
}

/* Fondo animado */
@keyframes floatingDots {
  from {transform: translateY(0);}
  to {transform: translateY(-20px);}
}

.dots {
  position: fixed;
  width: 100%;
  height: 100%;
  overflow: hidden;
  top: 0;
  left: 0;
  z-index: -1;
}

.dot {
  position: absolute;
  width: 10px;
  height: 10px;
  background: var(--neon1);
  border-radius: 50%;
  box-shadow: 0 0 10px var(--neon2);
  opacity: 0.2;
  animation: floatingDots 4s infinite alternate;
}
</style>
</head>
<body>

<div class="dots">
  <div class="dot" style="top:10%;left:15%;animation-delay:0s"></div>
  <div class="dot" style="top:50%;left:40%;animation-delay:1s"></div>
  <div class="dot" style="top:70%;left:80%;animation-delay:2s"></div>
  <div class="dot" style="top:30%;left:60%;animation-delay:3s"></div>
  <div class="dot" style="top:80%;left:25%;animation-delay:2s"></div>
</div>

<header>⚡ Calculadora de Redes ⚡</header>

<div class="container">
  <h2>Calculadora IPv4</h2>
  <label for="ip">Dirección IP:</label>
  <input type="text" id="ip" placeholder="Ejemplo: 192.168.1.1">
  <button onclick="calcularIPv4()">Calcular</button>

  <div id="resultados" class="results" style="display:none;">
    <p>🌍 <b>Clase:</b> <span id="clase" class="value"></span></p>
    <p>🧮 <b>Máscara:</b> <span id="mascara" class="value"></span></p>
    <p>💻 <b>Red:</b> <span id="red" class="value"></span></p>
    <p>📡 <b>Broadcast:</b> <span id="broadcast" class="value"></span></p>
    <p>🔢 <b>Hosts:</b> <span id="hosts" class="value"></span></p>
    <p>➡️ <b>Primera IP:</b> <span id="primera" class="value"></span></p>
    <p>⬅️ <b>Última IP:</b> <span id="ultima" class="value"></span></p>
    <p>🌐 <b>IPv6 (2025::/64):</b> <span id="ipv6" class="value"></span></p>
  </div>

  <hr style="margin:25px 0;border:1px solid rgba(0,255,255,0.2);">

  <h2>Calculadora de Wildcard</h2>
  <label for="mask">Máscara:</label>
  <input type="text" id="mask" placeholder="Ejemplo: 255.255.255.0">
  <button onclick="calcularWildcard()">Calcular Wildcard</button>

  <div id="wildRes" class="results" style="display:none;">
    <p>🎯 <b>Wildcard:</b> <span id="wildcard" class="value"></span></p>
  </div>
</div>

<footer>Hecho por Leonardo Amante</footer>

<script>
function ipToInt(ip){
  const p = ip.split('.').map(Number);
  if (p.length !== 4) return NaN;
  return ((p[0]<<24)|(p[1]<<16)|(p[2]<<8)|p[3])>>>0;
}
function intToIp(i){
  return [(i>>>24)&255,(i>>>16)&255,(i>>>8)&255,i&255].join('.');
}
function prefixToMask(p){return intToIp(p===0?0:(~0<<(32-p))>>>0)}
function detectarClase(ip){
  const o=parseInt(ip.split('.')[0]);
  if(o>=1&&o<=126) return {clase:'A',prefijo:8};
  if(o>=128&&o<=191) return {clase:'B',prefijo:16};
  if(o>=192&&o<=223) return {clase:'C',prefijo:24};
  return {clase:'Desconocida',prefijo:24};
}
function ipv4to6(ip){
  const p=ip.split('.').map(Number);
  const h1=((p[0]<<8)|p[1]).toString(16).padStart(4,'0');
  const h2=((p[2]<<8)|p[3]).toString(16).padStart(4,'0');
  return `2025::${h1}:${h2}`;
}
function calcularIPv4(){
  const ip=document.getElementById('ip').value.trim();
  if(!ip.match(/^(\d{1,3}\.){3}\d{1,3}$/)){alert('IP no válida');return;}
  const {clase,prefijo}=detectarClase(ip);
  const ipInt=ipToInt(ip);
  const maskInt=(~0<<(32-prefijo))>>>0;
  const red=ipInt & maskInt;
  const broad=red | (~maskInt>>>0);
  const hosts=(2**(32-prefijo))-2;
  const primera=red+1;
  const ultima=broad-1;
  document.getElementById('resultados').style.display='block';
  document.getElementById('clase').textContent=clase;
  document.getElementById('mascara').textContent=prefixToMask(prefijo);
  document.getElementById('red').textContent=intToIp(red);
  document.getElementById('broadcast').textContent=intToIp(broad);
  document.getElementById('hosts').textContent=hosts;
  document.getElementById('primera').textContent=intToIp(primera);
  document.getElementById('ultima').textContent=intToIp(ultima);
  document.getElementById('ipv6').textContent=ipv4to6(ip);
}
function calcularWildcard(){
  const mask=document.getElementById('mask').value.trim();
  const parts=mask.split('.').map(Number);
  if(parts.length!==4||parts.some(n=>isNaN(n)||n<0||n>255)){
    alert('Máscara inválida');return;
  }
  const wild=parts.map(n=>255-n).join('.');
  document.getElementById('wildcard').textContent=wild;
  document.getElementById('wildRes').style.display='block';
}
</script>
</body>
</html>
