<html lang="es">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1.0" />
<title>Calculadora de Redes + Chat IA — Leonardo Amante</title>
<style>
:root{
  --accent:#00ffc3; --accent2:#00aaff;
  --bg1:#00131d; --bg2:#002933;
  --text:#e8f6ff; --muted:#9fb0c6; --err:#ff4d6d;
}
*{box-sizing:border-box;margin:0;padding:0}
body{
  font-family:Inter,Segoe UI,Arial,sans-serif;color:var(--text);
  background:radial-gradient(circle at 25% 25%,var(--bg1) 0%,var(--bg2) 100%);
  background-size:200% 200%;animation:moveBg 25s ease-in-out infinite;
  min-height:100vh;overflow-y:auto;padding-bottom:120px;
}
@keyframes moveBg{0%{background-position:0% 50%}50%{background-position:100% 50%}100%{background-position:0% 50%}}
.panel{
  margin:30px auto;max-width:1000px;background:rgba(0,0,0,.40);
  border:1px solid rgba(255,255,255,.10);border-radius:12px;padding:20px;
  backdrop-filter:blur(8px);box-shadow:0 0 25px rgba(0,255,200,.15);
}
h1{color:var(--accent);text-shadow:0 0 12px var(--accent2);margin-bottom:10px}
label{color:var(--muted);font-size:.95rem}
input{
  width:100%;padding:10px;margin:8px 0;border:none;border-radius:8px;
  background:rgba(255,255,255,.07);color:var(--text);font-size:1rem;
}
button{
  background:linear-gradient(90deg,var(--accent),var(--accent2));
  color:#001;font-weight:800;border:none;border-radius:8px;
  padding:10px 18px;cursor:pointer;margin-top:10px;
}
button:hover{box-shadow:0 0 15px var(--accent)}
.result{margin-top:10px;background:rgba(255,255,255,.07);border-radius:10px;padding:12px}
.result-line{opacity:0;transform:translateY(6px)}
.result.show .result-line{animation:pop 420ms cubic-bezier(.2,.7,.2,1) forwards}
.result.show .result-line:nth-child(1){animation-delay:0ms}
.result.show .result-line:nth-child(2){animation-delay:90ms}
.result.show .result-line:nth-child(3){animation-delay:180ms}
.result.show .result-line:nth-child(4){animation-delay:270ms}
.result.show .result-line:nth-child(5){animation-delay:360ms}
.result.show .result-line:nth-child(6){animation-delay:450ms}
.result.show .result-line:nth-child(7){animation-delay:540ms}
@keyframes pop{from{opacity:0;transform:translateY(6px)}to{opacity:1;transform:translateY(0)}}
.value{font-weight:800;color:#adfff3}
#ipv6{color:#88dfff}
.warning{color:#bbb;font-size:.9rem;margin-top:6px}

/* Footer */
footer{text-align:right;padding:20px 30px;color:rgba(255,255,255,.4)}

/* Chat flotante IA */
#chat-btn{position:fixed;right:18px;bottom:18px;width:56px;height:56px;border-radius:50%;
  background:linear-gradient(135deg,#00ffc3,#00aaff);color:#001;display:grid;place-items:center;
  font-weight:900;cursor:pointer;z-index:9999;box-shadow:0 8px 28px rgba(0,0,0,.35)}
#chat-box{position:fixed;right:18px;bottom:86px;width:320px;max-height:72vh;
  background:rgba(0,0,0,.65);border:1px solid rgba(255,255,255,.1);
  border-radius:14px;box-shadow:0 16px 40px rgba(0,0,0,.5);display:none;
  backdrop-filter:blur(10px);z-index:9999}
#chat-head{padding:10px 12px;border-bottom:1px solid rgba(255,255,255,.08);display:flex;align-items:center;gap:8px}
#chat-head b{color:#aef}
#chat-body{padding:10px;overflow:auto;max-height:48vh}
.msg{border-radius:10px;padding:8px 10px;margin:6px 0;max-width:85%}
.user{background:rgba(0,255,195,.15);margin-left:auto;border:1px solid rgba(0,255,195,.25)}
.bot{background:rgba(255,255,255,.08);border:1px solid rgba(255,255,255,.1)}
#chat-input{display:flex;gap:8px;padding:10px;border-top:1px solid rgba(255,255,255,.1)}
#userInput{flex:1;background:rgba(255,255,255,.08);border:none;border-radius:8px;padding:10px;color:#e8f6ff}
#sendBtn{background:linear-gradient(90deg,#00ffc3,#00aaff);border:none;border-radius:8px;padding:10px 14px;font-weight:800;cursor:pointer;color:#001}

/* Aviso si falta token */
#token-alert{
  position:fixed;left:50%;transform:translateX(-50%);bottom:18px;
  background:rgba(255,77,109,.14);border:1px solid rgba(255,77,109,.55);
  color:#ffdce5;padding:10px 14px;border-radius:10px;
  box-shadow:0 10px 28px rgba(0,0,0,.35);backdrop-filter:blur(6px);
  font-weight:700;z-index:9998;display:none
}

/* Bloque marcador dentro del CÓDIGO (solo referencia visual en comentarios) */
.code-marker{color:#fff;background:linear-gradient(90deg,#ff4d6d,#ffcc00);
  padding:2px 6px;border-radius:6px;font-weight:900}
</style>
</head>
<body>

<!-- =========================
     CALCULADORA IPv4
========================= -->
<div class="panel">
  <h1>Calculadora IPv4</h1>
  <label>Dirección IP</label>
  <input id="ipInput" placeholder="Ej: 10.10.10.10 o 192.168.1.1">
  <div class="warning">Se detecta automáticamente la <b>máscara real por clase</b> (A=/8, B=/16, C=/24) al apretar “Calcular”.</div>
  <button id="calcBtn">Calcular</button>
  <div id="error" class="warning"></div>

  <div id="results" class="result" style="display:none">
    <div class="result-line">🌍 <b>Clase:</b> <span id="classOut" class="value">—</span></div>
    <div class="result-line">🧮 <b>Máscara:</b> <span id="maskOut" class="value">—</span> <span id="maskNote" class="warning"></span></div>
    <div class="result-line">💻 <b>Red:</b> <span id="netOut" class="value">—</span></div>
    <div class="result-line">📡 <b>Broadcast:</b> <span id="broadOut" class="value">—</span></div>
    <div class="result-line">🔢 <b>Hosts:</b> <span id="hostsOut" class="value">—</span></div>
    <div class="result-line">➡️ <b>Primera IP:</b> <span id="firstOut" class="value">—</span></div>
    <div class="result-line">⬅️ <b>Última IP:</b> <span id="lastOut" class="value">—</span></div>
    <div class="result-line">🌐 <b>IPv6 (2025::/64):</b> <span id="ipv6" class="value">—</span></div>
  </div>

  <button id="copyAll" style="margin-top:10px">Copiar Todo</button>
</div>

<!-- =========================
     CALCULADORA WILDCARD
========================= -->
<div class="panel">
  <h1>Calculadora de Wildcard</h1>
  <label>Máscara de Red</label>
  <input id="maskInput" placeholder="Ej: 255.255.255.0">
  <button id="wildBtn">Calcular Wildcard</button>
  <div class="result">
    <div class="result-line">🎯 <b>Wildcard:</b> <span id="wildOut" class="value">—</span></div>
  </div>
</div>

<footer>Hecho por Leonardo Amante</footer>

<!-- Botón & Panel del Chat IA -->
<div id="chat-btn" title="Asistente IA">IA</div>
<div id="chat-box" role="dialog" aria-label="Asistente IA">
  <div id="chat-head"><b>Asistente IA</b></div>
  <div id="chat-body"></div>
  <div id="chat-input">
    <input id="userInput" placeholder="Pregúntame algo sobre redes...">
    <button id="sendBtn">Enviar</button>
  </div>
</div>

<!-- Alerta si no hay token -->
<div id="token-alert">⚠️ Pega tu token de Hugging Face en el código para habilitar el chat IA.</div>

<script>
/* ================================================
   🚨 MARCADOR SUPER LLAMATIVO: PEGA TU TOKEN AQUÍ
   Reemplaza el valor "AQUI_TU_TOKEN_HF" por tu token real.
   EJEMPLO: "hf_XXXXXXXXXXXXXXXXXXXXXXXXXXXX"
   ================================================ */
const HF_TOKEN = "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";
/* ================================================ */

/* ------------ Utilidades IPv4 ------------ */
function ipToInt(ip){const p=ip.split('.').map(Number);if(p.length!==4)return NaN;return ((p[0]<<24)|(p[1]<<16)|(p[2]<<8)|p[3])>>>0;}
function intToIp(i){return[(i>>>24)&255,(i>>>16)&255,(i>>>8)&255,i&255].join('.')}
function prefixToMask(p){return intToIp(p===0?0:(~0<<(32-p))>>>0)}
function detectClass(ip){
  const o=parseInt(ip.split('.')[0],10);
  if(o>=1&&o<=126) return 'A';
  if(o===127) return 'Loopback (127/8)';
  if(o>=128&&o<=191) return 'B';
  if(o>=192&&o<=223) return 'C';
  if(o>=224&&o<=239) return 'D (Multicast)';
  if(o>=240&&o<=254) return 'E (Experimental)';
  if(o===0) return 'Reservada (0/8)';
  return 'Desconocida';
}
function classDefaultPrefix(ip){
  const o=parseInt(ip.split('.')[0],10);
  if(o>=1&&o<=126) return 8;
  if(o>=128&&o<=191) return 16;
  if(o>=192&&o<=223) return 24;
  return null; // D/E/Loopback/Reservada → sin máscara host “real”
}
function calc(ip,p){
  const ipi=ipToInt(ip);
  const maskInt=p===0?0:((~0<<(32-p))>>>0);
  const net=(p===0?0:(ipi & maskInt))>>>0;
  const broad=(p===0?0xFFFFFFFF:(net | (~maskInt>>>0)))>>>0;
  const hosts=(p>=31)?(p===31?2:1):(2**(32-p))-2;
  const first=(p<=30)?net+1:(p===31?net:broad);
  const last=(p<=30)?broad-1:(p===31?broad:net);
  return{
    mask:prefixToMask(p),
    network:intToIp(net),
    broadcast:intToIp(broad),
    hosts,
    first:intToIp(first>>>0),
    last:intToIp(last>>>0)
  };
}
function ipv4to6_2025(ip){
  const parts=ip.split('.').map(Number);
  if(parts.length!==4) return '—';
  const hex1=((parts[0]<<8)|parts[1]).toString(16).padStart(4,'0');
  const hex2=((parts[2]<<8)|parts[3]).toString(16).padStart(4,'0');
  return `2025::${hex1}:${hex2}`;
}
function wildcard(mask){
  const oct = mask.split('.').map(n=>Number(n));
  if(oct.length!==4 || oct.some(n=>Number.isNaN(n) || n<0 || n>255)) return 'Máscara inválida';
  return oct.map(n=>255-n).join('.');
}

/* ------------ DOM y eventos ------------ */
const ipIn=document.getElementById('ipInput');
const err=document.getElementById('error');
const res=document.getElementById('results');
const OUT={
  clase:document.getElementById('classOut'),
  mask:document.getElementById('maskOut'),
  maskNote:document.getElementById('maskNote'),
  net:document.getElementById('netOut'),
  broad:document.getElementById('broadOut'),
  hosts:document.getElementById('hostsOut'),
  first:document.getElementById('firstOut'),
  last:document.getElementById('lastOut'),
  ipv6:document.getElementById('ipv6')
};

document.getElementById('calcBtn').onclick=()=>{
  res.classList.remove('show');
  const raw=ipIn.value.trim();
  if(!raw){ err.textContent="Ingresa una IP."; res.style.display="none"; return; }
  if(!/^\d{1,3}(\.\d{1,3}){3}$/.test(raw)){ err.textContent="Formato inválido. Usa x.x.x.x"; res.style.display="none"; return; }
  err.textContent="";

  const octs=raw.split('.').map(Number);
  if(octs.some(n=>n>255)){
    err.textContent="⚠ IP fuera de rango real (octetos >255). Se mostrará cálculo solo a modo de práctica.";
  }

  const clase=detectClass(raw);
  const pref=classDefaultPrefix(raw);
  OUT.clase.textContent=clase;
  OUT.maskNote.textContent="";

  if(pref===null){
    const fallback=24;
    const r=calc(raw, fallback);
    OUT.mask.textContent=r.mask;
    OUT.maskNote.textContent=" (sin máscara host clásica; usando /24 para práctica)";
    OUT.net.textContent=r.network;
    OUT.broad.textContent=r.broadcast;
    OUT.hosts.textContent=r.hosts;
    OUT.first.textContent=r.first;
    OUT.last.textContent=r.last;
  }else{
    const r=calc(raw, pref);
    OUT.mask.textContent=r.mask;
    OUT.net.textContent=r.network;
    OUT.broad.textContent=r.broadcast;
    OUT.hosts.textContent=r.hosts;
    OUT.first.textContent=r.first;
    OUT.last.textContent=r.last;
  }
  OUT.ipv6.textContent=ipv4to6_2025(raw);

  res.style.display="block";
  void res.offsetWidth;
  res.classList.add('show');
};

document.getElementById('wildBtn').onclick=()=>{
  const mask=document.getElementById('maskInput').value.trim();
  const out =document.getElementById('wildOut');
  out.textContent = mask ? wildcard(mask) : "—";
};

document.getElementById('copyAll').onclick=()=>{
  const text=`IPv4: ${ipIn.value}
Clase: ${OUT.clase.textContent}
Máscara: ${OUT.mask.textContent}
Red: ${OUT.net.textContent}
Broadcast: ${OUT.broad.textContent}
Hosts: ${OUT.hosts.textContent}
Primera IP: ${OUT.first.textContent}
Última IP: ${OUT.last.textContent}
IPv6 (2025::/64): ${OUT.ipv6.textContent}
Wildcard: ${document.getElementById('wildOut').textContent}`;
  navigator.clipboard.writeText(text);
  alert("Datos copiados al portapapeles.");
};

/* ------------ Chat IA (Hugging Face Inference API) ------------ */
/* Modelo recomendado público: mistralai/Mixtral-8x7B (texto). */
const HF_MODEL = "mistralai/Mixtral-8x7B"; // puedes cambiar a otro modelo público

const chatBtn=document.getElementById('chat-btn');
const chatBox=document.getElementById('chat-box');
const chatBody=document.getElementById('chat-body');
const chatInput=document.getElementById('userInput');
const chatSend=document.getElementById('sendBtn');
const tokenAlert=document.getElementById('token-alert');

function addMsg(text,cls){
  const d=document.createElement('div');
  d.className='msg '+cls;
  d.textContent=text;
  chatBody.appendChild(d);
  chatBody.scrollTop=chatBody.scrollHeight;
}

chatBtn.onclick=()=>{chatBox.style.display=chatBox.style.display==='block'?'none':'block';};

chatSend.onclick=async()=>{
  const q=chatInput.value.trim();
  if(!q) return;
  addMsg(q,'user'); chatInput.value='';

  if(!HF_TOKEN || !HF_TOKEN.startsWith('hf_')){
    tokenAlert.style.display='block';
    addMsg("⚠️ Debes pegar tu token de Hugging Face en el código para habilitar el chat.", 'bot');
    return;
  }

  addMsg("⏳ Pensando...", 'bot');
  try{
    const resp = await fetch(`https://api-inference.huggingface.co/models/${HF_MODEL}`,{
      method:"POST",
      headers:{
        "Authorization":"Bearer "+HF_TOKEN,
        "Content-Type":"application/json"
      },
      body:JSON.stringify({
        inputs: q,
        parameters: { max_new_tokens: 200, temperature: 0.5 },
        options: { wait_for_model: true }
      })
    });

    const data = await resp.json();
    // Formatos posibles: [{generated_text: "..."}] o {generated_text: "..."} o texto plano
    let out = "";
    if (Array.isArray(data)) {
      out = data[0]?.generated_text || JSON.stringify(data);
    } else if (typeof data === "object" && data.generated_text) {
      out = data.generated_text;
    } else if (typeof data === "string") {
      out = data;
    } else if (data.error) {
      out = "Error: " + data.error;
    } else {
      out = JSON.stringify(data);
    }

    // Reemplaza el último "Pensando..." por la respuesta real
    const last = chatBody.querySelector('.msg.bot:last-child');
    if(last) last.textContent = out;
    else addMsg(out,'bot');

  }catch(e){
    const last = chatBody.querySelector('.msg.bot:last-child');
    const msg = "Hubo un problema al contactar Hugging Face.";
    if(last) last.textContent = msg; else addMsg(msg,'bot');
  }
};

chatInput.addEventListener('keydown',e=>{if(e.key==='Enter') chatSend.click();});

// Mostrar alerta si falta token al cargar
window.addEventListener('load', ()=>{
  if(!HF_TOKEN || !HF_TOKEN.startsWith('hf_')){
    tokenAlert.style.display = 'block';
  }
});
</script>
</body>
</html>
