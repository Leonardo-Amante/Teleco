<html lang="es">
<head>
<meta charset="utf-8"/>
<meta name="viewport" content="width=device-width,initial-scale=1"/>
<title>Telecom — Calculadora de Red y VELSM</title>
<style>
/* ====== THEME ====== */
:root{
  --bg1:#051423; --bg2:#0a2140; --bg3:#0f2f59;
  --glass:rgba(14,25,42,.56);
  --stroke:rgba(255,255,255,.08);
  --accent:#00e5c1; --accent2:#a86bff; --muted:#9fb0c6;
}
*{box-sizing:border-box}

/* ====== GLOBAL BG with animations ====== */
body{
  margin:0;min-height:100vh;color:#e8f6ff;
  font-family:Inter,ui-sans-serif,system-ui,-apple-system,"Segoe UI",Roboto,Arial;
  background:
    radial-gradient(1200px 700px at 8% 12%, #133a62 0%, transparent 55%),
    radial-gradient(900px 600px at 92% 88%, #241a6b 0%, transparent 60%),
    linear-gradient(135deg, var(--bg1), var(--bg2), var(--bg3));
  background-size:140% 140%;
  animation:bgshift 24s ease-in-out infinite;
  display:flex;align-items:center;justify-content:center;padding:26px;position:relative;overflow:hidden;
}
@keyframes bgshift{
  0%{background-position:0% 50%}
  50%{background-position:100% 50%}
  100%{background-position:0% 50%}
}

/* LED moving strips (top/bottom) */
.led-strip{position:fixed;left:0;right:0;height:4px;z-index:4;pointer-events:none;opacity:.7}
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

/* Moving circuit lines layer */
.circuits{position:fixed;inset:0;z-index:0;pointer-events:none;opacity:.22}
.circuit{
  position:absolute;height:2px;width:120vw;left:-10vw;background:
  linear-gradient(90deg, transparent 0%, rgba(0,229,193,.9) 40%, rgba(168,107,255,.9) 60%, transparent 100%);
  filter:blur(.3px);
  animation:scrollx 14s linear infinite;
}
.circuit:nth-child(1){top:15%}
.circuit:nth-child(2){top:38%;animation-duration:18s}
.circuit:nth-child(3){top:62%;animation-duration:16s}
.circuit:nth-child(4){top:85%;animation-duration:20s}
@keyframes scrollx{to{transform:translateX(10vw)}}

/* Particles */
.particles{position:fixed;inset:0;pointer-events:none;z-index:0}
.dot{
  position:absolute;width:4px;height:4px;border-radius:50%;
  background:radial-gradient(circle,#fff,rgba(255,255,255,.08));
  opacity:.18;filter:blur(.5px);animation:float 16s linear infinite;
}
.dot:nth-child(odd){animation-duration:20s;opacity:.26}
@keyframes float{0%{transform:translateY(110vh)}100%{transform:translateY(-10vh)}}

/* ====== LAYOUT ====== */
.wrap{position:relative;z-index:1;width:100%;max-width:1140px;display:grid;gap:26px;grid-template-columns:1fr 420px}

/* Panels: glass + LED border */
.panel{
  position:relative;border-radius:16px;padding:20px;background:var(--glass);
  border:1px solid var(--stroke);backdrop-filter:blur(10px);
  box-shadow:0 10px 40px rgba(0,0,0,.45);
}
.panel::before{
  content:"";position:absolute;inset:-1px;border-radius:16px;padding:1px;
  background:
      linear-gradient(120deg, rgba(255,255,255,.08), rgba(255,255,255,0) 30%),
      conic-gradient(from var(--a,0deg), var(--accent), var(--accent2), var(--accent), var(--accent2));
  -webkit-mask:linear-gradient(#000 0 0) content-box,linear-gradient(#000 0 0);
  -webkit-mask-composite:xor;mask-composite:exclude;
  animation:borderSpin 10s linear infinite;
}
@keyframes borderSpin{to{--a:360deg}}

.header{display:flex;align-items:center;gap:12px;margin-bottom:10px}
.logo{
  width:50px;height:50px;border-radius:12px;
  background:linear-gradient(135deg,var(--accent),var(--accent2));
  display:grid;place-items:center;color:#02161b;font-weight:800;
  box-shadow:0 8px 24px rgba(0,229,193,.25),0 0 30px rgba(168,107,255,.15);
}
h1{font-size:20px;margin:0}
.lead{margin:6px 0 14px;color:var(--muted);font-size:13px}
.info-msg{font-size:13px;color:var(--accent2);margin-bottom:10px;text-shadow:0 0 8px rgba(168,107,255,.2)}

.form-row{display:flex;gap:12px;margin-bottom:12px}
.field{flex:1;display:flex;flex-direction:column;gap:8px}
label{font-size:13px;color:var(--muted)}
input,textarea{
  background:rgba(7,16,28,.55);border:1px solid rgba(255,255,255,.06);
  color:#e6f7ff;border-radius:10px;padding:12px;font-size:14px;outline:none;
  transition:box-shadow .25s,border-color .25s, transform .06s ease;
}
input:focus,textarea:focus{
  border-color:var(--accent);
  box-shadow:0 0 0 4px rgba(0,229,193,.12);
}
input:active{transform:scale(.998)}
.btn{
  background:linear-gradient(90deg,var(--accent),var(--accent2));
  color:#00161a;border:none;border-radius:10px;padding:11px 14px;font-weight:800;cursor:pointer;
  box-shadow:0 6px 18px rgba(0,0,0,.45),0 0 24px rgba(0,229,193,.18);
  transition:transform .15s, filter .25s;
  transform:perspective(600px) translateZ(0);
}
.btn:hover{transform:perspective(600px) translateZ(10px)}
.btn.ghost{background:transparent;color:var(--muted);border:1px solid rgba(255,255,255,.12);transform:none}

.cards{display:flex;flex-direction:column;gap:12px}
.card{
  display:flex;justify-content:space-between;align-items:center;gap:10px;
  background:rgba(255,255,255,.035);border:1px solid rgba(255,255,255,.06);
  border-radius:12px;padding:12px;transition:background .25s, transform .15s, box-shadow .15s;
}
.card:hover{background:rgba(255,255,255,.08);transform:translateY(-1px);box-shadow:0 10px 26px rgba(0,0,0,.25)}
.title{font-size:12px;color:var(--muted)}
.value{font-size:16px;font-weight:800}
.copy-btn{border:none;border-radius:10px;background:rgba(255,255,255,.06);color:var(--muted);padding:6px 10px;cursor:pointer}
.copy-btn:hover{background:rgba(0,229,193,.12);color:#dff}

.result-large{
  margin-top:12px;padding:14px;border-radius:12px;
  background:linear-gradient(90deg,rgba(0,229,193,.06),rgba(168,107,255,.06));
  border:1px solid rgba(0,229,193,.12);font-weight:800;color:#e9fffb;
  box-shadow:0 12px 28px rgba(0,0,0,.25) inset;
}
.error{color:#ffb4ad;font-weight:800;margin-top:8px}

/* Signature */
.firma{position:fixed;right:12px;bottom:8px;font-size:12px;color:rgba(255,255,255,.32);
  font-style:italic;user-select:none;letter-spacing:.3px}

/* Reduce motion accessibility */
@media (prefers-reduced-motion: reduce){
  .led-top,.led-bottom,.panel::before{animation:none}
  body{animation:none}
  .circuit,.dot{animation-duration:1s}
}

/* Responsive */
@media (max-width:980px){.wrap{grid-template-columns:1fr}}
</style>
</head>
<body>
  <!-- LED strips -->
  <div class="led-strip led-top"></div>
  <div class="led-strip led-bottom"></div>

  <!-- circuits layer -->
  <div class="circuits" aria-hidden="true">
    <div class="circuit"></div><div class="circuit"></div><div class="circuit"></div><div class="circuit"></div>
  </div>

  <!-- particles -->
  <div class="particles" aria-hidden="true">
    <div class="dot" style="left:8%;animation-delay:0s"></div>
    <div class="dot" style="left:22%;animation-delay:3s"></div>
    <div class="dot" style="left:37%;animation-delay:6s"></div>
    <div class="dot" style="left:58%;animation-delay:1s"></div>
    <div class="dot" style="left:74%;animation-delay:4s"></div>
    <div class="dot" style="left:90%;animation-delay:2s"></div>
  </div>

  <div class="wrap">
    <!-- LEFT PANEL -->
    <div class="panel">
      <div class="header">
        <div class="logo">TC</div>
        <div>
          <h1>Calculadora de Red — Telecom</h1>
          <p class="lead">Detecta clase, autocompleta máscara/prefijo y evalúa VELSM</p>
        </div>
      </div>

      <div id="info" class="info-msg">Clase: —</div>

      <div class="form-row">
        <div class="field">
          <label for="ipInput">IP / CIDR</label>
          <input id="ipInput" placeholder="Ej: 192.168.0.1 o 10.0.0.0/8">
        </div>
        <div class="field" style="max-width:130px">
          <label for="prefixInput">Prefijo</label>
          <input id="prefixInput" type="number" min="1" max="32" placeholder="24">
        </div>
      </div>

      <div class="form-row">
        <div class="field"><label>Máscara</label><input id="maskOut" readonly></div>
        <div class="field"><label>Hosts</label><input id="hostsOut" readonly></div>
      </div>
      <div class="form-row">
        <div class="field"><label>Red</label><input id="networkOut" readonly></div>
        <div class="field"><label>Broadcast</label><input id="broadcastOut" readonly></div>
      </div>
      <div class="form-row">
        <div class="field"><label>Primera IP</label><input id="firstOut" readonly></div>
        <div class="field"><label>Última IP</label><input id="lastOut" readonly></div>
      </div>

      <div style="display:flex;gap:10px;margin-top:10px">
        <button class="btn" id="calcBtn">Calcular</button>
        <button class="btn ghost" id="clearBtn">Limpiar</button>
      </div>

      <hr style="margin:14px 0;border:none;border-top:1px solid rgba(255,255,255,.1)">

      <label for="formula">Fórmula VELSM</label>
      <textarea id="formula" placeholder="Ejemplo: hosts * 1.5" style="height:88px"></textarea>
      <div style="display:flex;gap:10px;margin-top:10px">
        <button class="btn" id="evalBtn">Evaluar</button>
        <button class="btn ghost" id="exampleBtn">Ejemplo</button>
      </div>
      <div id="velsmResult" class="result-large">—</div>
      <div id="errorMsg" class="error" style="display:none"></div>
    </div>

    <!-- RIGHT PANEL -->
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
/* ---------- IPv4 helpers ---------- */
function ipToInt(ip){
  const p=ip.trim().split('.').map(Number);
  if(p.length!==4||p.some(n=>isNaN(n)||n<0||n>255)) throw Error('IP inválida');
  return ((p[0]<<24)|(p[1]<<16)|(p[2]<<8)|p[3])>>>0;
}
function intToIp(i){return[(i>>>24)&255,(i>>>16)&255,(i>>>8)&255,i&255].join('.')}
function prefixToMask(p){return intToIp(p===0?0:(~0<<(32-p))>>>0)}
function calc(ip,p){
  const ipi=ipToInt(ip), maskInt=(~0<<(32-p))>>>0;
  const n=ipi & maskInt, b=n | (~maskInt>>>0);
  const hosts=(p>=31)?(p===31?2:1):(2**(32-p)-2);
  const first=p<=30?n+1:(p===31?n:b);
  const last =p<=30?b-1:(p===31?b:n);
  return {ip, prefix:p, mask:prefixToMask(p), network:intToIp(n), broadcast:intToIp(b), first:intToIp(first), last:intToIp(last), hosts};
}

/* ---------- Class detection & auto mask ---------- */
function detectClass(ip){
  const firstOct = parseInt(ip.split('.')[0],10);
  let clase='Desconocida', pref=24, mask='255.255.255.0';
  if(firstOct>=1 && firstOct<=126){clase='A';pref=8; mask='255.0.0.0'}
  else if(firstOct>=128 && firstOct<=191){clase='B';pref=16;mask='255.255.0.0'}
  else if(firstOct>=192 && firstOct<=223){clase='C';pref=24;mask='255.255.255.0'}
  else if(firstOct>=224 && firstOct<=239){clase='D (Multicast)';pref='—';mask='—'}
  else if(firstOct>=240 && firstOct<=254){clase='E (Experimental)';pref='—';mask='—'}
  let priv='';
  if(ip.startsWith('10.')) priv=' — IP Privada';
  else if(ip.startsWith('172.16.')||ip.startsWith('172.17.')||ip.startsWith('172.18.')||ip.startsWith('172.19.')||
          ip.startsWith('172.20.')||ip.startsWith('172.21.')||ip.startsWith('172.22.')||ip.startsWith('172.23.')||
          ip.startsWith('172.24.')||ip.startsWith('172.25.')||ip.startsWith('172.26.')||ip.startsWith('172.27.')||
          ip.startsWith('172.28.')||ip.startsWith('172.29.')||ip.startsWith('172.30.')||ip.startsWith('172.31.')) priv=' — IP Privada';
  else if(ip.startsWith('192.168.')) priv=' — IP Privada';
  document.getElementById('info').textContent = `Clase ${clase}${priv}`;
  return {clase,pref,mask};
}

/* ---------- DOM refs ---------- */
const ipIn=document.getElementById('ipInput');
const pIn=document.getElementById('prefixInput');
const mOut=document.getElementById('maskOut');
const hOut=document.getElementById('hostsOut');
const nOut=document.getElementById('networkOut');
const bOut=document.getElementById('broadcastOut');
const fOut=document.getElementById('firstOut');
const lOut=document.getElementById('lastOut');
const err=document.getElementById('errorMsg');
const resV=document.getElementById('velsmResult');
const infoMsg=document.getElementById('info');

const fields = {
  ip:document.getElementById('c_ip'),
  prefix:document.getElementById('c_prefix'),
  mask:document.getElementById('c_mask'),
  network:document.getElementById('c_network'),
  broadcast:document.getElementById('c_broadcast'),
  first:document.getElementById('c_first'),
  last:document.getElementById('c_last'),
  hosts:document.getElementById('c_hosts')
};

/* ---------- Auto-detect on typing ---------- */
ipIn.addEventListener('input', ()=>{
  const v = ipIn.value.trim();
  if(/^\d{1,3}(\.\d{1,3}){3}$/.test(v)){
    const d = detectClass(v);
    if(d.pref!=='—'){ pIn.value=d.pref; mOut.value=d.mask; }
  } else {
    infoMsg.textContent = 'Clase: —';
  }
});

/* ---------- Actions ---------- */
document.getElementById('calcBtn').onclick = ()=>{
  try{
    err.style.display='none';
    let ip = ipIn.value.trim(), pref = pIn.value?parseInt(pIn.value,10):undefined;
    if(ip.includes('/')){ const [a,b]=ip.split('/'); ip=a; pref=parseInt(b,10); }
    if(!ip) throw Error('Ingresa una IP válida');
    if(!pref && pref!==0) throw Error('Falta prefijo (o usa notación CIDR)');
    const r = calc(ip,pref);
    mOut.value=r.mask; hOut.value=r.hosts; nOut.value=r.network; bOut.value=r.broadcast; fOut.value=r.first; lOut.value=r.last;
    Object.entries(fields).forEach(([k,el])=> el.textContent = r[k] ?? '—');
    resV.textContent='—';
  }catch(e){ err.textContent=e.message; err.style.display='block'; }
};

document.getElementById('clearBtn').onclick = ()=>{
  document.querySelectorAll('input,textarea').forEach(el=>el.value='');
  Object.values(fields).forEach(el=>el.textContent='—');
  resV.textContent='—'; err.style.display='none'; infoMsg.textContent='Clase: —';
};

document.querySelectorAll('.copy-btn').forEach(btn=>{
  btn.addEventListener('click', async ()=>{
    const id = btn.getAttribute('data-copy');
    try{
      await navigator.clipboard.writeText(document.getElementById(id).textContent);
      btn.textContent='✔'; setTimeout(()=>btn.textContent='Copiar',900);
    }catch{}
  });
});

document.getElementById('exampleBtn').onclick=()=>{document.getElementById('formula').value='hosts * 1.5'};
document.getElementById('evalBtn').onclick=()=>{
  try{
    const hosts = parseFloat(hOut.value)||0;
    const prefix = parseFloat(pIn.value)||0;
    const expr = document.getElementById('formula').value.trim();
    if(!expr) return;
    const val = Function('hosts','prefix','return '+expr)(hosts,prefix);
    resV.textContent = String(val);
  }catch(e){ err.textContent=e.message; err.style.display='block'; }
};

/* distribute particle top positions */
document.querySelectorAll('.dot').forEach((d,i)=>{ d.style.top = (i*16)%100 + '%'; });
</script>
</body>
</html>
