<html lang="es">
<head>
<meta charset="utf-8"/>
<meta name="viewport" content="width=device-width,initial-scale=1"/>
<title>Telecom — Calculadora de Red y VELSM</title>
<style>
:root{
  --bg1:#061221;--bg2:#0a1e37;--bg3:#112d4a;
  --accent:#00e5c1;--accent2:#a86bff;--muted:#9fb0c6;
}
*{box-sizing:border-box}
body{
  margin:0;min-height:100vh;color:#e8f6ff;font-family:Inter,ui-sans-serif,system-ui;
  background:radial-gradient(1000px 500px at 10% 10%,#0c2b46 0%,transparent 55%),
             radial-gradient(900px 600px at 90% 90%,#1a1747 0%,transparent 60%),
             linear-gradient(135deg,var(--bg1),var(--bg2),var(--bg3));
  animation:bgshift 22s ease-in-out infinite;background-size:120% 120%;
  display:flex;align-items:center;justify-content:center;padding:26px;position:relative;overflow:hidden;
}
@keyframes bgshift{0%{background-position:0% 50%}50%{background-position:100% 50%}100%{background-position:0% 50%}}
.particles{position:fixed;inset:0;pointer-events:none;z-index:0}
.dot{position:absolute;width:4px;height:4px;border-radius:50%;
     background:radial-gradient(circle,#fff,rgba(255,255,255,.1));
     opacity:.15;filter:blur(.5px);animation:float 14s linear infinite}
.dot:nth-child(odd){animation-duration:18s;opacity:.22}
@keyframes float{0%{transform:translateY(110vh)}100%{transform:translateY(-10vh)}}
.wrap{position:relative;z-index:1;width:100%;max-width:1120px;display:grid;gap:26px;grid-template-columns:1fr 420px}
.panel{
  position:relative;border-radius:16px;padding:20px;
  background:rgba(18,32,48,.55);border:1px solid rgba(255,255,255,.08);
  backdrop-filter:blur(10px);box-shadow:0 10px 40px rgba(0,0,0,.45);
}
.header{display:flex;align-items:center;gap:12px;margin-bottom:10px}
.logo{
  width:50px;height:50px;border-radius:12px;
  background:linear-gradient(135deg,var(--accent),var(--accent2));
  display:grid;place-items:center;color:#02161b;font-weight:800;
  box-shadow:0 8px 24px rgba(0,229,193,.25),0 0 30px rgba(168,107,255,.15);
}
h1{font-size:20px;margin:0}
.lead{margin:6px 0 14px;color:var(--muted);font-size:13px}
.form-row{display:flex;gap:12px;margin-bottom:12px}
.field{flex:1;display:flex;flex-direction:column;gap:8px}
label{font-size:13px;color:var(--muted)}
input,textarea{
  background:rgba(7,16,28,.55);border:1px solid rgba(255,255,255,.06);
  color:#e6f7ff;border-radius:10px;padding:12px;font-size:14px;outline:none;
  transition:box-shadow .25s,border-color .25s;
}
input:focus,textarea:focus{
  border-color:var(--accent);
  box-shadow:0 0 0 4px rgba(0,229,193,.12);
}
.btn{
  background:linear-gradient(90deg,var(--accent),var(--accent2));
  color:#00161a;border:none;border-radius:10px;padding:11px 14px;font-weight:800;cursor:pointer;
  box-shadow:0 6px 18px rgba(0,0,0,.45),0 0 24px rgba(0,229,193,.18);
}
.btn.ghost{background:transparent;color:var(--muted);border:1px solid rgba(255,255,255,.12)}
.cards{display:flex;flex-direction:column;gap:12px}
.card{display:flex;justify-content:space-between;align-items:center;gap:10px;
  background:rgba(255,255,255,.035);border:1px solid rgba(255,255,255,.06);
  border-radius:12px;padding:12px;}
.title{font-size:12px;color:var(--muted)}
.value{font-size:16px;font-weight:800}
.copy-btn{border:none;border-radius:10px;background:rgba(255,255,255,.06);color:var(--muted);padding:6px 10px;cursor:pointer}
.copy-btn:hover{background:rgba(0,229,193,.12);color:#dff}
.result-large{margin-top:12px;padding:14px;border-radius:12px;
  background:linear-gradient(90deg,rgba(0,229,193,.06),rgba(168,107,255,.06));
  border:1px solid rgba(0,229,193,.12);font-weight:800;color:#e9fffb;}
.error{color:#ffb4ad;font-weight:800;margin-top:8px}
.firma{position:fixed;right:12px;bottom:8px;font-size:12px;
  color:rgba(255,255,255,.32);font-style:italic;user-select:none}
.info-msg{font-size:13px;color:var(--accent2);margin-bottom:10px;text-shadow:0 0 8px rgba(168,107,255,.2)}
</style>
</head>
<body>
<div class="particles"><div class="dot" style="left:10%;animation-delay:0s"></div><div class="dot" style="left:30%;animation-delay:4s"></div><div class="dot" style="left:50%;animation-delay:2s"></div><div class="dot" style="left:70%;animation-delay:6s"></div><div class="dot" style="left:90%;animation-delay:1s"></div></div>

<div class="wrap">
  <div class="panel">
    <div class="header"><div class="logo">TC</div><div><h1>Calculadora de Red — Telecom</h1><p class="lead">Detecta clase, máscara y evalúa tu fórmula VELSM</p></div></div>
    <div id="info" class="info-msg">Clase: —</div>

    <div class="form-row">
      <div class="field"><label>IP / CIDR</label><input id="ipInput" placeholder="Ej: 192.168.0.1"></div>
      <div class="field" style="max-width:130px"><label>Prefijo</label><input id="prefixInput" type="number" min="1" max="32" placeholder="24"></div>
    </div>
    <div class="form-row"><div class="field"><label>Máscara</label><input id="maskOut" readonly></div><div class="field"><label>Hosts</label><input id="hostsOut" readonly></div></div>
    <div class="form-row"><div class="field"><label>Red</label><input id="networkOut" readonly></div><div class="field"><label>Broadcast</label><input id="broadcastOut" readonly></div></div>
    <div class="form-row"><div class="field"><label>Primera IP</label><input id="firstOut" readonly></div><div class="field"><label>Última IP</label><input id="lastOut" readonly></div></div>

    <div style="display:flex;gap:10px;margin-top:10px"><button class="btn" id="calcBtn">Calcular</button><button class="btn ghost" id="clearBtn">Limpiar</button></div>
    <hr style="margin:14px 0;border:none;border-top:1px solid rgba(255,255,255,.1)">
    <label>Fórmula VELSM</label>
    <textarea id="formula" placeholder="Ejemplo: hosts * 1.5" style="height:88px"></textarea>
    <div style="display:flex;gap:10px;margin-top:10px"><button class="btn" id="evalBtn">Evaluar</button><button class="btn ghost" id="exampleBtn">Ejemplo</button></div>
    <div id="velsmResult" class="result-large">—</div>
    <div id="errorMsg" class="error" style="display:none"></div>
  </div>

  <div class="panel">
    <h1 style="font-size:16px;margin:4px 0 12px">Resultados</h1>
    <div class="cards">
      <div class="card"><div><div class="title">IP</div><div class="value" id="c_ip">—</div></div><button class="copy-btn" data-copy="c_ip">Copiar</button></div>
      <div class="card"><div><div class="title">Prefijo</div><div class="value" id="c_prefix">—</div></div><button class="copy-btn" data-copy="c_prefix">Copiar</button></div>
      <div class="card"><div><div class="title">Máscara</div><div class="value" id="c_mask">—</div></div><button class="copy-btn" data-copy="c_mask">Copiar</button></div>
      <div class="card"><div><div class="title">Red</div><div class="value" id="c_network">—</div></div><button class="copy-btn" data-copy="c_network">Copiar</button></div>
      <div class="card"><div><div class="title">Broadcast</div><div class="value" id="c_broadcast">—</div></div><button class="copy-btn" data-copy="c_broadcast">Copiar</button></div>
      <div class="card"><div><div class="title">Primera IP</div><div class="value" id="c_first">—</div></div><button class="copy-btn" data-copy="c_first">Copiar</button></div>
      <div class="card"><div><div class="title">Última IP</div><div class="value" id="c_last">—</div></div><button class="copy-btn" data-copy="c_last">Copiar</button></div>
      <div class="card"><div><div class="title">Hosts</div><div class="value" id="c_hosts">—</div></div><button class="copy-btn" data-copy="c_hosts">Copiar</button></div>
    </div>
  </div>
</div>
<div class="firma">Hecho por Leonardo Amante</div>

<script>
// --- utilidades ---
function ipToInt(ip){const p=ip.trim().split('.').map(Number);
 if(p.length!==4||p.some(n=>isNaN(n)||n<0||n>255))throw Error('IP inválida');
 return ((p[0]<<24)|(p[1]<<16)|(p[2]<<8)|p[3])>>>0;}
function intToIp(i){return[(i>>>24)&255,(i>>>16)&255,(i>>>8)&255,i&255].join('.')}
function prefixToMask(p){return intToIp(p===0?0:(~0<<(32-p))>>>0)}
function calc(ip,p){
 const ipi=ipToInt(ip),maskInt=(~0<<(32-p))>>>0;
 const n=ipi&maskInt,b=n|(~maskInt>>>0);
 const hosts=(p>=31)?(p===31?2:1):(2**(32-p)-2);
 const first=p<=30?n+1:(p===31?n:b);
 const last=p<=30?b-1:(p===31?b:n);
 return{ip,prefix:p,mask:prefixToMask(p),network:intToIp(n),broadcast:intToIp(b),first:intToIp(first),last:intToIp(last),hosts};
}

// --- detección de clase ---
function detectClass(ip){
  const first=parseInt(ip.split('.')[0]);
  let clase='Desconocida',pref=24,mask='255.255.255.0';
  if(first>=1&&first<=126){clase='A';pref=8;mask='255.0.0.0';}
  else if(first>=128&&first<=191){clase='B';pref=16;mask='255.255.0.0';}
  else if(first>=192&&first<=223){clase='C';pref=24;mask='255.255.255.0';}
  else if(first>=224&&first<=239){clase='D (Multicast)';pref='—';mask='—';}
  else if(first>=240&&first<=254){clase='E (Experimental)';pref='—';mask='—';}
  let priv='';
  if(ip.startsWith('10.')) priv=' — IP Privada';
  else if(ip.startsWith('172.16.')||ip.startsWith('172.17.')||ip.startsWith('172.31.')) priv=' — IP Privada';
  else if(ip.startsWith('192.168.')) priv=' — IP Privada';
  document.getElementById('info').textContent=`Clase ${clase}${priv}`;
  return{clase,pref,mask};
}

// --- DOM ---
const ipIn=document.getElementById('ipInput'),pIn=document.getElementById('prefixInput');
const mOut=document.getElementById('maskOut'),hOut=document.getElementById('hostsOut'),
nOut=document.getElementById('networkOut'),bOut=document.getElementById('broadcastOut'),
fOut=document.getElementById('firstOut'),lOut=document.getElementById('lastOut');
const err=document.getElementById('errorMsg'),resV=document.getElementById('velsmResult');
const fields={ip:c('c_ip'),prefix:c('c_prefix'),mask:c('c_mask'),network:c('c_network'),
broadcast:c('c_broadcast'),first:c('c_first'),last:c('c_last'),hosts:c('c_hosts')};
function c(id){return document.getElementById(id)}

// autocompletar al escribir IP
ipIn.addEventListener('input',()=>{
  const val=ipIn.value.trim();
  if(/^\d{1,3}(\.\d{1,3}){3}$/.test(val)){
    const info=detectClass(val);
    if(info.pref!=='—'){pIn.value=info.pref;mOut.value=info.mask;}
  }else document.getElementById('info').textContent='Clase: —';
});

document.getElementById('calcBtn').onclick=()=>{
 try{
   err.style.display='none';
   let ip=ipIn.value.trim(),pref=pIn.value?parseInt(pIn.value,10):undefined;
   if(ip.includes('/')){const[a,b]=ip.split('/');ip=a;pref=parseInt(b,10);}
   if(!pref&&pref!==0)throw Error('Falta prefijo');
   const r=calc(ip,pref);
   mOut.value=r.mask;hOut.value=r.hosts;nOut.value=r.network;bOut.value=r.broadcast;
   fOut.value=r.first;lOut.value=r.last;
   Object.entries(fields).forEach(([k,v])=>v.textContent=r[k]||'—');
   resV.textContent='—';
 }catch(e){err.textContent=e.message;err.style.display='block'}
};
document.getElementById('clearBtn').onclick=()=>{
 document.querySelectorAll('input,textarea').forEach(el=>el.value='');
 Object.values(fields).forEach(el=>el.textContent='—');
 resV.textContent='—';err.style.display='none';
 document.getElementById('info').textContent='Clase: —';
};
document.querySelectorAll('.copy-btn').forEach(b=>b.onclick=async()=>{
 const id=b.dataset.copy;await navigator.clipboard.writeText(c(id).textContent);
 b.textContent='✔';setTimeout(()=>b.textContent='Copiar',900);
});
document.getElementById('exampleBtn').onclick=()=>{document.getElementById('formula').value='hosts * 1.5'};
document.getElementById('evalBtn').onclick=()=>{
 try{
   const hosts=parseFloat(hOut.value)||0,prefix=parseFloat(pIn.value)||0;
   const expr=document.getElementById('formula').value.trim();if(!expr)return;
   const val=Function('hosts','prefix','return '+expr)(hosts,prefix);
   resV.textContent=String(val);
 }catch(e){err.textContent=e.message;err.style.display='block'}
};
</script>
</body>
</html>
