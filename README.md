<html lang="es">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1.0" />
<title>Calculadora de Redes — Telecom Leonardo Amante</title>
<style>
:root{
  --accent:#00ffc3;
  --accent2:#00aaff;
  --bg1:#02101a;
  --bg2:#042534;
  --text:#e8f6ff;
  --muted:#9fb0c6;
}
*{box-sizing:border-box}
body{
  margin:0;
  color:var(--text);
  font-family:Inter,Segoe UI,Arial,sans-serif;
  min-height:100vh;
  background:radial-gradient(circle at 25% 25%,var(--bg1) 0%,var(--bg2) 100%);
  background-size:200% 200%;
  animation:moveBg 20s ease-in-out infinite;
  overflow-y:auto;
  scroll-behavior:smooth;
}
@keyframes moveBg{
  0%{background-position:0% 50%}
  50%{background-position:100% 50%}
  100%{background-position:0% 50%}
}
h1{margin:0 0 10px;font-size:1.6rem;color:var(--accent)}
.panel{
  margin:30px auto;
  max-width:1000px;
  background:rgba(0,0,0,0.35);
  border:1px solid rgba(255,255,255,0.1);
  border-radius:12px;
  padding:20px;
  backdrop-filter:blur(10px);
  box-shadow:0 0 25px rgba(0,255,200,0.15);
}
label{font-size:0.9rem;color:var(--muted)}
input,textarea{
  width:100%;
  padding:10px;
  margin-top:5px;
  margin-bottom:10px;
  border:none;
  border-radius:8px;
  background:rgba(255,255,255,0.07);
  color:var(--text);
  font-size:1rem;
}
input:focus,textarea:focus{outline:2px solid var(--accent)}
button{
  background:linear-gradient(90deg,var(--accent),var(--accent2));
  border:none;border-radius:8px;
  color:#000;font-weight:700;
  padding:10px 16px;
  cursor:pointer;
  transition:0.3s;
}
button:hover{box-shadow:0 0 15px var(--accent)}
.result{background:rgba(255,255,255,0.07);
  border-radius:10px;padding:10px;margin-top:10px}
.value{font-weight:700;font-size:1.05rem;color:#adfff3}
#ipv6{color:#88dfff}
.warning{color:#bbb;font-size:0.8rem}
footer{
  text-align:right;
  padding:10px 30px;
  color:rgba(255,255,255,0.3);
  font-size:0.9rem;
}
</style>
</head>
<body>

<div class="panel">
  <h1>Calculadora de Red — IPv4 / IPv6</h1>
  <label>Dirección IP o CIDR</label>
  <input id="ipInput" placeholder="Ej: 192.168.1.0/24">
  <label>Prefijo (opcional)</label>
  <input id="prefixInput" type="number" min="0" max="32" placeholder="24">
  <button id="calcBtn">Calcular</button>
  <div id="error" class="warning"></div>

  <div id="results" class="result" style="display:none">
    <div>🌍 <b>Clase:</b> <span id="classOut">—</span></div>
    <div>🧮 <b>Máscara:</b> <span id="maskOut">—</span></div>
    <div>💻 <b>Red:</b> <span id="netOut">—</span></div>
    <div>📡 <b>Broadcast:</b> <span id="broadOut">—</span></div>
    <div>🔢 <b>Hosts:</b> <span id="hostsOut">—</span></div>
    <div>➡️ <b>Primera IP:</b> <span id="firstOut">—</span></div>
    <div>⬅️ <b>Última IP:</b> <span id="lastOut">—</span></div>
    <div>🌐 <b>IPv6 equivalente:</b> <span id="ipv6">—</span></div>
  </div>

  <button id="copyAll" style="margin-top:10px">Copiar Todo</button>
</div>

<div class="panel">
  <h1>VLSM — Evaluador</h1>
  <label>Expresión (usa la variable <b>hosts</b>)</label>
  <textarea id="formula" placeholder="Ej: hosts * 1.5"></textarea>
  <button id="evalBtn">Evaluar</button>
  <div class="result"><b>Resultado:</b> <span id="vlsmOut">—</span></div>
</div>

<div class="panel">
  <h1>Calculadora de Wildcard</h1>
  <label>Máscara de Red</label>
  <input id="maskInput" placeholder="Ej: 255.255.255.0">
  <button id="wildBtn">Calcular Wildcard</button>
  <div class="result"><b>Wildcard:</b> <span id="wildOut">—</span></div>
</div>

<footer>Hecho por Leonardo Amante</footer>

<script>
function ipToInt(ip){const p=ip.split('.').map(Number);
  if(p.length!==4)return NaN;
  return((p[0]<<24)|(p[1]<<16)|(p[2]<<8)|p[3])>>>0;}
function intToIp(i){return[(i>>>24)&255,(i>>>16)&255,(i>>>8)&255,i&255].join('.');}
function prefixToMask(p){return intToIp(p===0?0:(~0<<(32-p))>>>0);}
function detectClass(ip){
  const o=parseInt(ip.split('.')[0]);if(o>=1&&o<=126)return"A";if(o>=128&&o<=191)return"B";if(o>=192&&o<=223)return"C";return"Desconocida";}
function calc(ip,p){
  const ipi=ipToInt(ip);const maskInt=p===0?0:((~0<<(32-p))>>>0);
  const net=(p===0?0:(ipi&maskInt))>>>0;
  const broad=(p===0?0xFFFFFFFF:(net|(~maskInt>>>0)))>>>0;
  const hosts=p>=31?(p===31?2:1):(2**(32-p))-2;
  const first=p<=30?net+1:(p===31?net:broad);
  const last=p<=30?broad-1:(p===31?broad:net);
  return{mask:prefixToMask(p),network:intToIp(net),broadcast:intToIp(broad),
         hosts,first:intToIp(first>>>0),last:intToIp(last>>>0)};
}
function wildcard(mask){
  const p=mask.split('.').map(n=>255-Number(n));return p.join('.');}
function ipv4to6(ip){return"::ffff:"+ip;}

const ipIn=document.getElementById('ipInput');
const prefixIn=document.getElementById('prefixInput');
const err=document.getElementById('error');
const res=document.getElementById('results');

document.getElementById('calcBtn').onclick=()=>{
  const val=ipIn.value.trim();if(!val){err.textContent="Ingresa una IP.";return;}
  err.textContent="";
  let ip=val,pref=prefixIn.value?parseInt(prefixIn.value):undefined;
  if(val.includes('/')){const [a,b]=val.split('/');ip=a;pref=parseInt(b);}
  if(isNaN(pref))pref=24;
  if(!/^\d{1,3}(\.\d{1,3}){3}$/.test(ip)){
    err.textContent="Formato de IP inválido.";return;
  }
  const parts=ip.split('.').map(Number);
  if(parts.some(n=>n>999)){err.textContent="⚠ IP fuera de rango real, pero calculada igualmente.";}
  const r=calc(ip,pref);
  res.style.display="block";
  document.getElementById('classOut').textContent=detectClass(ip);
  document.getElementById('maskOut').textContent=r.mask;
  document.getElementById('netOut').textContent=r.network;
  document.getElementById('broadOut').textContent=r.broadcast;
  document.getElementById('hostsOut').textContent=r.hosts;
  document.getElementById('firstOut').textContent=r.first;
  document.getElementById('lastOut').textContent=r.last;
  document.getElementById('ipv6').textContent=ipv4to6(ip);
};
document.getElementById('evalBtn').onclick=()=>{
  const expr=document.getElementById('formula').value;
  const hostsTxt=document.getElementById('hostsOut').textContent;
  const hosts=Number(hostsTxt)||0;
  try{
    const result=Function('hosts',`return ${expr}`)(hosts);
    document.getElementById('vlsmOut').textContent=result;
  }catch{document.getElementById('vlsmOut').textContent="Error";}
};
document.getElementById('wildBtn').onclick=()=>{
  const m=document.getElementById('maskInput').value.trim();
  if(!m)return;
  if(!/^\d{1,3}(\.\d{1,3}){3}$/.test(m)){document.getElementById('wildOut').textContent="Máscara inválida";return;}
  document.getElementById('wildOut').textContent=wildcard(m);
};
document.getElementById('copyAll').onclick=()=>{
  const text=`IPv4: ${ipIn.value}
IPv6: ${document.getElementById('ipv6').textContent}
Red: ${document.getElementById('netOut').textContent}
Broadcast: ${document.getElementById('broadOut').textContent}
Hosts: ${document.getElementById('hostsOut').textContent}
Wildcard: ${document.getElementById('wildOut').textContent}`;
  navigator.clipboard.writeText(text);
  alert("Datos copiados al portapapeles.");
};
</script>
</body>
</html>
