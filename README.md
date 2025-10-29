<html lang="es">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1.0"/>
<title>Calculadora de Redes — Leonardo Amante</title>
<style>
:root{
  --neon1:#00f0ff; --neon2:#007bff;
  --bg1:#030b14;   --bg2:#021a25;
  --text:#eaf8ff;  --muted:#9fb0c6;
}
*{box-sizing:border-box;margin:0;padding:0}
body{
  font-family: 'Orbitron', system-ui, Segoe UI, Arial, sans-serif;
  color:var(--text);
  min-height:100vh;
  background: radial-gradient(1200px 800px at 15% 20%, #062033 0%, rgba(6,32,51,0) 60%),
              radial-gradient(1000px 800px at 85% 30%, #081b2e 0%, rgba(8,27,46,0) 60%),
              linear-gradient(135deg, var(--bg1), var(--bg2));
  overflow-x:hidden;
}

/* -------- Fondo de partículas -------- */
#sky{
  position:fixed; inset:0; z-index:-1; overflow:hidden; pointer-events:none;
}
.dot{
  position:absolute; border-radius:50%;
  background:radial-gradient(circle, rgba(255,255,255,0.95) 0%, rgba(255,255,255,0.65) 40%, rgba(255,255,255,0) 70%);
  box-shadow:
    0 0 8px rgba(0,240,255,.5),
    0 0 18px rgba(0,123,255,.35);
  opacity:var(--o, .7);
  width:var(--s,8px); height:var(--s,8px);
  left:var(--x, 50%); top:var(--y, 50%);
  transform:translate(-50%,-50%);
  animation:
    floatY var(--dur,6s) ease-in-out var(--delay,0s) infinite alternate,
    driftX  calc(var(--dur,6s) * 1.2) ease-in-out var(--delay,0s) infinite alternate,
    twinkle calc(var(--dur,6s) * .9) ease-in-out var(--delay,0s) infinite alternate;
  filter: drop-shadow(0 0 6px rgba(0,240,255,.45));
}
@keyframes floatY{
  from{ transform:translate(calc(-50% + 0px), calc(-50% +  -20px)); }
  to  { transform:translate(calc(-50% + 0px), calc(-50% +   20px)); }
}
@keyframes driftX{
  from{ left: calc(var(--x,50%) - var(--dx, 12px)); }
  to  { left: calc(var(--x,50%) + var(--dx, 12px)); }
}
@keyframes twinkle{
  from { opacity: calc(var(--o,.8) * .55); box-shadow:0 0 6px rgba(0,240,255,.35), 0 0 14px rgba(0,123,255,.2); }
  to   { opacity: var(--o,.85); box-shadow:0 0 12px rgba(0,240,255,.7),  0 0 22px rgba(0,123,255,.45); }
}

/* -------- UI -------- */
header{
  text-align:center; padding:32px 16px;
  font-size:2.2rem; color:var(--neon1);
  text-shadow:0 0 10px var(--neon1), 0 0 22px var(--neon2);
}
.container{
  max-width:980px; margin:0 auto 48px auto; padding:24px;
  background: rgba(0, 20, 30, 0.55);
  border:1px solid rgba(0,255,255,.25);
  border-radius:18px;
  box-shadow: 0 0 30px rgba(0,255,255,.08);
  backdrop-filter: blur(12px);
}
h2{
  color:var(--neon1); margin:10px 0 6px;
  text-shadow: 0 0 8px rgba(0,240,255,.7);
}
label{color:var(--muted)}
input{
  width:100%; margin:8px 0 14px; padding:12px 12px;
  border:none; border-radius:10px; color:#aef;
  background:rgba(255,255,255,.06);
  box-shadow: inset 0 0 10px rgba(0,255,255,.22);
  outline: none;
}
button{
  width:100%; padding:12px 16px; border:none; border-radius:10px;
  font-weight:800; color:#031019; cursor:pointer;
  background:linear-gradient(90deg, var(--neon1), var(--neon2));
  box-shadow: 0 0 18px rgba(0,240,255,.4);
  transition: transform .15s ease, box-shadow .25s ease;
}
button:hover{ transform:translateY(-1px); box-shadow:0 0 24px rgba(0,240,255,.65); }

.results{
  margin-top:16px; padding:14px;
  background: rgba(0,0,0,.45);
  border:1px solid rgba(0,255,255,.18);
  border-radius:12px;
  box-shadow: 0 0 20px rgba(0,255,255,.08);
}
.value{ color:#7feaff; font-weight:800; text-shadow:0 0 8px rgba(0,170,255,.5); }

hr{ margin:26px 0; border:none; height:1px; background:linear-gradient(90deg, transparent, rgba(0,255,255,.35), transparent); }

footer{ text-align:center; padding:28px 12px; color:rgba(255,255,255,.35); font-size:.95rem;}
</style>
</head>
<body>

<!-- Fondo de partículas -->
<div id="sky" aria-hidden="true"></div>

<header>⚡ Calculadora de Redes ⚡</header>

<div class="container">
  <h2>Calculadora IPv4</h2>
  <label for="ip">Dirección IP</label>
  <input id="ip" placeholder="Ej: 10.10.10.10 o 192.168.1.1">
  <button id="btnIpv4">Calcular</button>

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

  <hr>

  <h2>Calculadora de Wildcard</h2>
  <label for="mask">Máscara</label>
  <input id="mask" placeholder="Ej: 255.255.255.0">
  <button id="btnWild">Calcular Wildcard</button>

  <div id="wildRes" class="results" style="display:none;">
    <p>🎯 <b>Wildcard:</b> <span id="wildcard" class="value"></span></p>
  </div>
</div>

<footer>Hecho por Leonardo Amante</footer>

<script>
/* =======================
   Partículas aleatorias
   ======================= */
(function makeDots(){
  const sky = document.getElementById('sky');
  const count = 60; // cantidad de puntos
  for(let i=0;i<count;i++){
    const d = document.createElement('div');
    d.className = 'dot';
    // Aleatorios: posición, tamaño, opacidad, velocidad, amplitud horizontal, retraso
    const x = Math.random()*100;        // %
    const y = Math.random()*100;        // %
    const s = 4 + Math.random()*8;      // px
    const o = 0.45 + Math.random()*0.5; // 0.45 - 0.95
    const dur = (5 + Math.random()*8).toFixed(2) + 's';
    const delay = (Math.random()*6).toFixed(2) + 's';
    const dx = (8 + Math.random()*22) + 'px';

    d.style.setProperty('--x', x+'%');
    d.style.setProperty('--y', y+'%');
    d.style.setProperty('--s', s+'px');
    d.style.setProperty('--o', o);
    d.style.setProperty('--dur', dur);
    d.style.setProperty('--delay', delay);
    d.style.setProperty('--dx', dx);

    sky.appendChild(d);
  }
})();

/* =======================
   Utilidades de redes
   ======================= */
function ipToInt(ip){
  const p = ip.split('.').map(Number);
  if (p.length!==4 || p.some(n=>Number.isNaN(n))) return NaN;
  return ((p[0]<<24)|(p[1]<<16)|(p[2]<<8)|p[3])>>>0;
}
function intToIp(i){
  return [(i>>>24)&255,(i>>>16)&255,(i>>>8)&255,i&255].join('.');
}
function prefixToMask(p){return intToIp(p===0?0:(~0<<(32-p))>>>0)}
function detectarClase(ip){
  const o = parseInt(ip.split('.')[0],10);
  if(o>=1 && o<=126) return {clase:'A', prefijo:8};
  if(o>=128 && o<=191) return {clase:'B', prefijo:16};
  if(o>=192 && o<=223) return {clase:'C', prefijo:24};
  // otras → fallback práctico
  return {clase:'Desconocida', prefijo:24};
}
function ipv4to6(ip){
  const p = ip.split('.').map(Number);
  const h1 = ((p[0]<<8)|p[1]).toString(16).padStart(4,'0');
  const h2 = ((p[2]<<8)|p[3]).toString(16).padStart(4,'0');
  return `2025::${h1}:${h2}`;
}

document.getElementById('btnIpv4').onclick = ()=>{
  const ip = document.getElementById('ip').value.trim();
  if(!/^\d{1,3}(\.\d{1,3}){3}$/.test(ip)){ alert('IP no válida'); return; }

  const {clase, prefijo} = detectarClase(ip);
  const ipI = ipToInt(ip);
  const maskI = (~0 << (32 - prefijo)) >>> 0;
  const red = (ipI & maskI) >>> 0;
  const broad = (red | (~maskI >>> 0)) >>> 0;
  const hosts = (prefijo>=31) ? (prefijo===31 ? 2 : 1) : (2**(32-prefijo))-2;
  const primera = (prefijo<=30) ? (red + 1) : (prefijo===31 ? red : broad);
  const ultima   = (prefijo<=30) ? (broad - 1) : (prefijo===31 ? broad : red);

  document.getElementById('resultados').style.display='block';
  document.getElementById('clase').textContent   = clase;
  document.getElementById('mascara').textContent = prefixToMask(prefijo);
  document.getElementById('red').textContent     = intToIp(red);
  document.getElementById('broadcast').textContent = intToIp(broad);
  document.getElementById('hosts').textContent   = hosts;
  document.getElementById('primera').textContent = intToIp(primera>>>0);
  document.getElementById('ultima').textContent  = intToIp(ultima>>>0);
  document.getElementById('ipv6').textContent    = ipv4to6(ip);
};

document.getElementById('btnWild').onclick = ()=>{
  const m = document.getElementById('mask').value.trim();
  const parts = m.split('.').map(Number);
  if(parts.length!==4 || parts.some(n=>Number.isNaN(n) || n<0 || n>255)){ alert('Máscara inválida'); return; }
  const wild = parts.map(n=>255-n).join('.');
  document.getElementById('wildRes').style.display='block';
  document.getElementById('wildcard').textContent = wild;
};
</script>
</body>
</html>
