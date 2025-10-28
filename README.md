<html lang="es">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1.0" />
<title>Calculadora de Redes — Telecom Leonardo Amante</title>
<style>
:root{
  --accent:#00ffc3; --accent2:#00aaff;
  --bg1:#00131d; --bg2:#002933;
  --text:#e8f6ff; --muted:#9fb0c6;
}
*{box-sizing:border-box}
body{
  margin:0; color:var(--text);
  font-family:Inter,Segoe UI,Arial,sans-serif;
  min-height:100vh;
  background:radial-gradient(circle at 25% 25%,var(--bg1) 0%,var(--bg2) 100%);
  background-size:200% 200%; animation:moveBg 25s ease-in-out infinite;
  overflow-y:auto; scroll-behavior:smooth;
}
@keyframes moveBg{0%{background-position:0% 50%}50%{background-position:100% 50%}100%{background-position:0% 50%}}
h1{margin:0 0 10px;font-size:1.6rem;color:var(--accent);text-shadow:0 0 15px var(--accent2)}
.panel{
  margin:30px auto; max-width:1000px; background:rgba(0,0,0,0.35);
  border:1px solid rgba(255,255,255,0.1); border-radius:12px; padding:20px;
  backdrop-filter:blur(10px); box-shadow:0 0 25px rgba(0,255,200,0.15);
}
label{font-size:0.9rem;color:var(--muted)}
input{
  width:100%; padding:10px; margin-top:5px; margin-bottom:10px;
  border:none; border-radius:8px; background:rgba(255,255,255,0.07);
  color:var(--text); font-size:1rem;
}
input:focus{outline:2px solid var(--accent)}
button{
  background:linear-gradient(90deg,var(--accent),var(--accent2));
  border:none;border-radius:8px;color:#000;font-weight:700;
  padding:10px 16px;cursor:pointer;transition:0.3s;
}
button:hover{box-shadow:0 0 15px var(--accent)}
.result{background:rgba(255,255,255,0.07);border-radius:10px;padding:10px;margin-top:10px;overflow:hidden}
.result-line{opacity:0;transform:translateY(6px)}
.result.show .result-line{animation:popIn 420ms cubic-bezier(.2,.7,.2,1) forwards;animation-delay: calc(var(--i,0) * 90ms)}
@keyframes popIn{from{opacity:0;transform:translateY(6px)}to{opacity:1;transform:translateY(0)}}
.value{font-weight:700;font-size:1.05rem;color:#adfff3}
#ipv6{color:#88dfff}
.warning{color:#bbb;font-size:0.85rem;margin-top:6px}
footer{text-align:right;padding:10px 30px;color:rgba(255,255,255,0.3);font-size:0.9rem}
.sep{height:1px;background:rgba(255,255,255,0.1);margin:16px 0}
.small{font-size:.85rem;color:var(--muted)}
</style>
</head>
<body>

<!-- CALCULADORA IPV4 -->
<div class="panel">
  <h1>Calculadora IPv4</h1>
  <label>Dirección IP o CIDR</label>
  <input id="ipInput" placeholder="Ej: 192.168.1.0/24 o 8.8.8.8">
  <div class="small">Tip: si no pones prefijo, se aplica la <b>máscara por clase</b> (A=/8, B=/16, C=/24).</div>
  <label>Prefijo (opcional)</label>
  <input id="prefixInput" type="number" min="0" max="32" placeholder="(auto por clase)">
  <button id="calcBtn">Calcular</button>
  <div id="error" class="warning"></div>

  <div id="results" class="result" style="display:none">
    <div class="result-line" style="--i:0">🌍 <b>Clase:</b> <span id="classOut" class="value">—</span></div>
    <div class="result-line" style="--i:1">🧮 <b>Máscara:</b> <span id="maskOut" class="value">—</span></div>
    <div class="result-line" style="--i:2">💻 <b>Red:</b> <span id="netOut" class="value">—</span></div>
    <div class="result-line" style="--i:3">📡 <b>Broadcast:</b> <span id="broadOut" class="value">—</span></div>
    <div class="result-line" style="--i:4">🔢 <b>Hosts:</b> <span id="hostsOut" class="value">—</span></div>
    <div class="result-line" style="--i:5">➡️ <b>Primera IP:</b> <span id="firstOut" class="value">—</span></div>
    <div class="result-line" style="--i:6">⬅️ <b>Última IP:</b> <span id="lastOut" class="value">—</span></div>
    <div class="result-line" style="--i:7">🌐 <b>IPv6 real (2025::/64):</b> <span id="ipv6" class="value">—</span></div>
  </div>

  <button id="copyAll" style="margin-top:10px">Copiar Todo</button>
</div>

<!-- CALCULADORA WILDCARD -->
<div class="panel">
  <h1>Calculadora de Wildcard</h1>
  <label>Máscara de Red</label>
  <input id="maskInput" placeholder="Ej: 255.255.255.0">
  <button id="wildBtn">Calcular Wildcard</button>
  <div class="result">
    <div class="result-line" id="wildRow" style="--i:0"><b>Wildcard:</b> <span id="wildOut" class="value">—</span></div>
  </div>
</div>

<footer>Hecho por Leonardo Amante</footer>

<script>
/* ---------- Utilidades IPv4 ---------- */
function ipToInt(ip){
  const p=ip.split('.').map(Number);
  if(p.length!==4) return NaN; // Permitimos >255 para práctica
  return ((p[0]<<24)|(p[1]<<16)|(p[2]<<8)|p[3])>>>0;
}
function intToIp(i){return[(i>>>24)&255,(i>>>16)&255,(i>>>8)&255,i&255].join('.')}
function prefixToMask(p){return intToIp(p===0?0:(~0<<(32-p))>>>0)}
function detectClass(ip){
  const o=parseInt(ip.split('.')[0],10);
  if(o>=1&&o<=126) return 'A';
  if(o>=128&&o<=191) return 'B';
  if(o>=192&&o<=223) return 'C';
  if(o>=224&&o<=239) return 'D (Multicast)';
  if(o>=240&&o<=254) return 'E (Experimental)';
  return 'Desconocida';
}
/* Prefijo “clásico” por clase (A=8, B=16, C=24). Para D/E o desconocida, fallback 24. */
function defaultPrefixForIP(ip){
  const o=parseInt(ip.split('.')[0],10);
  if(o>=1&&o<=126) return 8;
  if(o>=128&&o<=191) return 16;
  if(o>=192&&o<=223) return 24;
  return 24; // fallback razonable
}

function calc(ip,p){
  const ipi=ipToInt(ip);
  const maskInt=p===0?0:((~0<<(32-p))>>>0);
  const net=(p===0?0:(ipi&maskInt))>>>0;
  const broad=(p===0?0xFFFFFFFF:(net|(~maskInt>>>0)))>>>0;
  const hosts=(p>=31)?(p===31?2:1):(2**(32-p))-2;
  const first=(p<=30)?net+1:(p===31?net:broad);
  const last=(p<=30)?broad-1:(p===31?broad:net);
  return{
    mask:prefixToMask(p),
    network:intToIp(net), broadcast:intToIp(broad),
    hosts, first:intToIp(first>>>0), last:intToIp(last>>>0)
  };
}

/* ---------- IPv6 real con prefijo 2025::/64 ---------- */
function ipv4to6_real_2025(ip){
  const parts=ip.split('.').map(Number);
  if(parts.length!==4) return '—';
  const hex1=((parts[0]<<8)|parts[1]).toString(16).padStart(4,'0');
  const hex2=((parts[2]<<8)|parts[3]).toString(16).padStart(4,'0');
  return `2025::${hex1}:${hex2}`;
}

/* ---------- Wildcard ---------- */
function wildcard(mask){
  const p=mask.split('.').map(n=>255-Number(n));
  if(p.length!==4 || p.some(v=>Number.isNaN(v))) return 'Máscara inválida';
  return p.join('.');
}

/* ---------- DOM ---------- */
const ipIn=document.getElementById('ipInput');
const pIn=document.getElementById('prefixInput');
const err=document.getElementById('error');
const resBox=document.getElementById('results');
const OUT={
  clase:document.getElementById('classOut'),
  mask:document.getElementById('maskOut'),
  net:document.getElementById('netOut'),
  broad:document.getElementById('broadOut'),
  hosts:document.getElementById('hostsOut'),
  first:document.getElementById('firstOut'),
  last:document.getElementById('lastOut'),
  ipv6:document.getElementById('ipv6')
};

/* Autocompletar prefijo por clase al tipear (si el usuario no fijó nada) */
ipIn.addEventListener('input', ()=>{
  const raw=ipIn.value.trim();
  if(!raw) return;
  // Si viene con /CIDR, no tocamos el prefijo del input
  if(/^\d{1,3}(\.\d{1,3}){3}$/.test(raw)){
    const c=defaultPrefixForIP(raw);
    if(pIn.value==="" || Number(pIn.value)<0 || Number(pIn.value)>32){
      pIn.value=c;
    }
  }
});

document.getElementById('calcBtn').onclick=()=>{
  resBox.classList.remove('show'); // reset animación
  const raw=ipIn.value.trim();
  let ip=raw, pref=(pIn.value!==""?parseInt(pIn.value,10):undefined);
  if(!raw){
    err.textContent="Ingresa una IP o IP/CIDR.";
    resBox.style.display="none"; return;
  }
  err.textContent="";
  // Soportar notación CIDR en el mismo campo
  if(raw.includes('/')){
    const [a,b]=raw.split('/');
    ip=a; pref=parseInt(b,10);
  }
  if(!/^\d{1,3}(\.\d{1,3}){3}$/.test(ip)){
    err.textContent="Formato inválido. Usa x.x.x.x o x.x.x.x/yy";
    resBox.style.display="none"; return;
  }
  // Si no hay prefijo válido, usar el de la clase
  if(!Number.isInteger(pref) || pref<0 || pref>32){
    pref = defaultPrefixForIP(ip);
    pIn.value = pref; // reflejarlo en el input
  }

  const octs=ip.split('.').map(Number);
  if(octs.some(n=>n>255)){
    err.textContent="⚠ IP fuera de rango real (octetos >255). Se calculará igualmente para tu práctica.";
  }

  const r=calc(ip,pref);
  OUT.clase.textContent=detectClass(ip);
  OUT.mask.textContent=r.mask;
  OUT.net.textContent=r.network;
  OUT.broad.textContent=r.broadcast;
  OUT.hosts.textContent=r.hosts;
  OUT.first.textContent=r.first;
  OUT.last.textContent=r.last;
  OUT.ipv6.textContent=ipv4to6_real_2025(ip);

  resBox.style.display="block";
  void resBox.offsetWidth; // reflow para reiniciar animación
  resBox.classList.add('show');
};

document.getElementById('wildBtn').onclick=()=>{
  const mask=document.getElementById('maskInput').value.trim();
  const out=document.getElementById('wildOut');
  const row=document.getElementById('wildRow');
  if(!mask){out.textContent="—";return}
  if(!/^\d{1,3}(\.\d{1,3}){3}$/.test(mask)){out.textContent="Máscara inválida";return}
  out.textContent=wildcard(mask);
  row.classList.remove('show'); void row.offsetWidth; row.classList.add('show');
};

document.getElementById('copyAll').onclick=()=>{
  const text=`IPv4: ${ipIn.value}
Prefijo usado: ${pIn.value || '(auto por clase)'}
Máscara: ${OUT.mask.textContent}
Red: ${OUT.net.textContent}
Broadcast: ${OUT.broad.textContent}
Hosts: ${OUT.hosts.textContent}
Primera IP: ${OUT.first.textContent}
Última IP: ${OUT.last.textContent}
IPv6 real (2025::/64): ${OUT.ipv6.textContent}
Wildcard: ${document.getElementById('wildOut').textContent}`;
  navigator.clipboard.writeText(text);
  alert("Datos copiados al portapapeles.");
};
</script>
</body>
</html>
