<html lang="es">
<head>
<meta charset="utf-8"/>
<meta name="viewport" content="width=device-width,initial-scale=1"/>
<title>Telecom — Calculadora de Red y VELSM</title>
<style>
:root{
  --bg1:#051423; --bg2:#0a2140; --bg3:#0f2f59;
  --glass:rgba(14,25,42,.56);
  --stroke:rgba(255,255,255,.08);
  --accent:#00e5c1; --accent2:#a86bff; --muted:#9fb0c6;
  --text:#e8f6ff;
}
*{box-sizing:border-box}
body{
  margin:0;min-height:100vh;color:var(--text);
  font-family:Inter,ui-sans-serif,system-ui;
  background:linear-gradient(135deg,var(--bg1),var(--bg2),var(--bg3));
  background-size:140% 140%;
  animation:bgshift 24s ease-in-out infinite;
  display:flex;align-items:center;justify-content:center;padding:26px;position:relative;overflow:hidden;
}
@keyframes bgshift{
  0%{background-position:0% 50%}
  50%{background-position:100% 50%}
  100%{background-position:0% 50%}
}

/* ✅ fix: particle/circuit layers behind everything */
.led-strip,.circuits,.particles{pointer-events:none;z-index:0;}

.wrap{position:relative;z-index:2;width:100%;max-width:1140px;display:grid;gap:26px;grid-template-columns:1fr 420px}
.panel{
  position:relative;border-radius:16px;padding:20px;background:var(--glass);
  border:1px solid var(--stroke);backdrop-filter:blur(10px);
  box-shadow:0 10px 40px rgba(0,0,0,.45);z-index:3;
}

/* ✅ ensure all inputs clickable */
input,textarea,button{position:relative;z-index:4;}

.led-strip{position:fixed;left:0;right:0;height:4px;z-index:1;pointer-events:none;opacity:.7}
.led-top{top:0;background:
  repeating-linear-gradient(90deg, #0ff 0 10px, #fff 10px 20px, #f0f 20px 30px, #0ff 30px 40px);
  filter:drop-shadow(0 0 8px #0ff);
  animation: ledflow 6s linear infinite;
}
.led-bottom{bottom:0;background:
  repeating-linear-gradient(90deg, #f0f 0 10px, #fff 10px 20px, #0ff 20px 30px, #f0f 30px 40px);
  filter:drop-shadow(0 0 8px #f0f);
  animation: ledflow 6s linear infinite reverse;
}
@keyframes ledflow{to{background-position:100% 0}}

.circuits{position:fixed;inset:0;z-index:1;pointer-events:none;opacity:.22}
.circuit{position:absolute;height:2px;width:120vw;left:-10vw;
  background:linear-gradient(90deg,transparent 0%,rgba(0,229,193,.9)40%,rgba(168,107,255,.9)60%,transparent 100%);
  filter:blur(.3px);animation:scrollx 14s linear infinite;}
.circuit:nth-child(1){top:15%}
.circuit:nth-child(2){top:38%;animation-duration:18s}
.circuit:nth-child(3){top:62%;animation-duration:16s}
.circuit:nth-child(4){top:85%;animation-duration:20s}
@keyframes scrollx{to{transform:translateX(10vw)}}

.particles{position:fixed;inset:0;pointer-events:none;z-index:1}
.dot{position:absolute;width:4px;height:4px;border-radius:50%;
  background:radial-gradient(circle,#fff,rgba(255,255,255,.08));
  opacity:.18;filter:blur(.5px);animation:float 16s linear infinite;}
.dot:nth-child(odd){animation-duration:20s;opacity:.26}
@keyframes float{0%{transform:translateY(110vh)}100%{transform:translateY(-10vh)}}

.header{display:flex;align-items:center;gap:12px;margin-bottom:10px}
.logo{width:50px;height:50px;border-radius:12px;
  background:linear-gradient(135deg,var(--accent),var(--accent2));
  display:grid;place-items:center;color:#02161b;font-weight:800;}
h1{font-size:20px;margin:0}
.lead{margin:6px 0 14px;color:var(--muted);font-size:13px}
.info-msg{font-size:13px;color:var(--accent2);margin-bottom:10px}
.form-row{display:flex;gap:12px;margin-bottom:12px}
.field{flex:1;display:flex;flex-direction:column;gap:8px}
label{font-size:13px;color:var(--muted)}
input,textarea{
  background:rgba(7,16,28,.55);border:1px solid rgba(255,255,255,.06);
  color:#e6f7ff;border-radius:10px;padding:12px;font-size:14px;outline:none;
  transition:box-shadow .25s,border-color .25s;
}
input:focus,textarea:focus{border-color:var(--accent);box-shadow:0 0 0 4px rgba(0,229,193,.12)}
.btn{background:linear-gradient(90deg,var(--accent),var(--accent2));
  color:#00161a;border:none;border-radius:10px;padding:11px 14px;font-weight:800;cursor:pointer;}
.btn.ghost{background:transparent;color:var(--muted);border:1px solid rgba(255,255,255,.12)}
.cards{display:flex;flex-direction:column;gap:12px}
.card{display:flex;justify-content:space-between;align-items:center;gap:10px;
  background:rgba(255,255,255,.035);border:1px solid rgba(255,255,255,.06);
  border-radius:12px;padding:12px;}
.title{font-size:12px;color:var(--muted)}
.value{font-size:16px;font-weight:800}
.result-large{margin-top:12px;padding:14px;border-radius:12px;
  background:linear-gradient(90deg,rgba(0,229,193,.06),rgba(168,107,255,.06));
  border:1px solid rgba(0,229,193,.12);font-weight:800;color:#e9fffb;}
.error{color:#ffb4ad;font-weight:800;margin-top:8px}
.firma{position:fixed;right:12px;bottom:8px;font-size:12px;
  color:rgba(255,255,255,.32);font-style:italic;user-select:none;}
</style>
</head>
<body>
<div class="led-strip led-top"></div>
<div class="led-strip led-bottom"></div>
<div class="circuits">
  <div class="circuit"></div><div class="circuit"></div><div class="circuit"></div><div class="circuit"></div>
</div>
<div class="particles">
  <div class="dot" style="left:8%;animation-delay:0s"></div>
  <div class="dot" style="left:22%;animation-delay:3s"></div>
  <div class="dot" style="left:37%;animation-delay:6s"></div>
  <div class="dot" style="left:58%;animation-delay:1s"></div>
  <div class="dot" style="left:74%;animation-delay:4s"></div>
  <div class="dot" style="left:90%;animation-delay:2s"></div>
</div>

<div class="wrap">
  <div class="panel">
    <div class="header"><div class="logo">TC</div>
      <div><h1>Calculadora de Red — Telecom</h1><p class="lead">Detección automática de clase y máscara</p></div>
    </div>
    <div id="info" class="info-msg">Clase: —</div>
    <div class="form-row">
      <div class="field"><label>IP / CIDR</label><input id="ipInput" placeholder="Ej: 192.168.0.1"></div>
      <div class="field" style="max-width:130px"><label>Prefijo</label><input id="prefixInput" type="number" min="1" max="32"></div>
    </div>
    <div class="form-row"><div class="field"><label>Máscara</label><input id="maskOut" readonly></div>
      <div class="field"><label>Hosts</label><input id="hostsOut" readonly></div></div>
    <div class="form-row"><div class="field"><label>Red</label><input id="networkOut" readonly></div>
      <div class="field"><label>Broadcast</label><input id="broadcastOut" readonly></div></div>
    <div class="form-row"><div class="field"><label>Primera IP</label><input id="firstOut" readonly></div>
      <div class="field"><label>Última IP</label><input id="lastOut" readonly></div></div>
    <div style="display:flex;gap:10px;margin-top:10px">
      <button class="btn" id="calcBtn">Calcular</button>
      <button class="btn ghost" id="clearBtn">Limpiar</button>
    </div>
    <hr style="margin:14px 0;border:none;border-top:1px solid rgba(255,255,255,.1)">
    <label>Fórmula VELSM</label><textarea id="formula" placeholder="Ej: hosts * 1.5"></textarea>
    <div style="display:flex;gap:10px;margin-top:10px">
      <button class="btn" id="evalBtn">Evaluar</button>
      <button class="btn ghost" id="exampleBtn">Ejemplo</button>
    </div>
    <div id="velsmResult" class="result-large">—</div>
    <div id="errorMsg" class="error" style="display:none"></div>
  </div>
  <div class="panel">
    <h1 style="font-size:16px;margin:4px 0 12px">Resultados</h1>
    <div class="cards">
      <div class="card"><div><div class="title">IP</div><div class="value" id="c_ip">—</div></div></div>
      <div class="card"><div><div class="title">Máscara</div><div class="value" id="c_mask">—</div></div></div>
      <div class="card"><div><div class="title">Red</div><div class="value" id="c_network">—</div></div></div>
      <div class="card"><div><div class="title">Broadcast</div><div class="value" id="c_broadcast">—</div></div></div>
      <div class="card"><div><div class="title">Hosts</div><div class="value" id="c_hosts">—</div></div></div>
    </div>
  </div>
</div>
<div class="firma">Hecho por Leonardo Amante</div>

<script>
function ipToInt(ip){const p=ip.trim().split('.').map(Number);if(p.length!==4||p.some(n=>isNaN(n)||n<0||n>255))throw Error('IP inválida');return((p[0]<<24)|(p[1]<<16)|(p[2]<<8)|p[3])>>>0;}
function intToIp(i){return[(i>>>24)&255,(i>>>16)&255,(i>>>8)&255,i&255].join('.')}
function prefixToMask(p){return intToIp(p===0?0:(~0<<(32-p))>>>0)}
function calc(ip,p){const ipi=ipToInt(ip),maskInt=(~0<<(32-p))>>>0;const n=ipi&maskInt,b=n|(~maskInt>>>0);const hosts=(p>=31)?(p===31?2:1):(2**(32-p)-2);const first=p<=30?n+1:(p===31?n:b);const last=p<=30?b-1:(p===31?b:n);return{mask:prefixToMask(p),network:intToIp(n),broadcast:intToIp(b),hosts,first:intToIp(first),last:intToIp(last)}}
function detectClass(ip){const first=parseInt(ip.split('.')[0]);let pref=24,mask='255.255.255.0';if(first>=1&&first<=126){pref=8;mask='255.0.0.0'}else if(first>=128&&first<=191){pref=16;mask='255.255.0.0'}else if(first>=192&&first<=223){pref=24;mask='255.255.255.0'}document.getElementById('info').textContent=`Clase ${pref<=8?'A':pref<=16?'B':'C'}`;return{pref,mask}}
const ipIn=document.getElementById('ipInput'),pIn=document.getElementById('prefixInput'),mOut=document.getElementById('maskOut'),hOut=document.getElementById('hostsOut'),nOut=document.getElementById('networkOut'),bOut=document.getElementById('broadcastOut'),fOut=document.getElementById('firstOut'),lOut=document.getElementById('lastOut'),err=document.getElementById('errorMsg'),resV=document.getElementById('velsmResult');
ipIn.addEventListener('input',()=>{const v=ipIn.value.trim();if(/^\d{1,3}(\.\d{1,3}){3}$/.test(v)){const d=detectClass(v);pIn.value=d.pref;mOut.value=d.mask;}});
document.getElementById('calcBtn').onclick=()=>{try{err.style.display='none';const ip=ipIn.value.trim();const pref=parseInt(pIn.value,10);if(!ip||isNaN(pref))throw Error('Datos incompletos');const r=calc(ip,pref);mOut.value=r.mask;hOut.value=r.hosts;nOut.value=r.network;bOut.value=r.broadcast;fOut.value=r.first;lOut.value=r.last;}catch(e){err.textContent=e.message;err.style.display='block'}};
document.getElementById('clearBtn').onclick=()=>{document.querySelectorAll('input,textarea').forEach(el=>el.value='');resV.textContent='—';err.style.display='none';document.getElementById('info').textContent='Clase: —';};
document.getElementById('exampleBtn').onclick=()=>{document.getElementById('formula').value='hosts * 1.5'};
document.getElementById('evalBtn').onclick=()=>{try{const hosts=parseFloat(hOut.value)||0;const expr=document.getElementById('formula').value.trim();if(!expr)return;resV.textContent=Function('hosts','return '+expr)(hosts);}catch(e){err.textContent=e.message;err.style.display='block'}};
</script>
</body>
</html>
