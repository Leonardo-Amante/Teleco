<html lang="es">
<head>
<meta charset="utf-8" />
<meta name="viewport" content="width=device-width,initial-scale=1" />
<title>Telecom — Calculadora de Red y VELSM</title>
<style>
  :root{
    --bg1:#071322; --bg2:#0b2540; --bg3:#162a47;
    --card:#0f2330;
    --accent:#00e5c1;
    --accent-2:#a86bff;
    --muted:#9fb0c6;
  }
  *{box-sizing:border-box}
  body{
    margin:0;min-height:100vh;
    background:linear-gradient(135deg,var(--bg1),var(--bg2),var(--bg3));
    background-size:400% 400%;
    animation:bgshift 20s ease infinite;
    color:#dbefff;
    font-family:Inter,ui-sans-serif,system-ui,-apple-system,"Segoe UI",Roboto,"Helvetica Neue",Arial;
    display:flex;align-items:center;justify-content:center;padding:28px;position:relative;
  }
  @keyframes bgshift{
    0%{background-position:0% 50%}
    50%{background-position:100% 50%}
    100%{background-position:0% 50%}
  }

  .wrap{
    width:100%;max-width:1100px;
    display:grid;grid-template-columns: 1fr 420px;gap:28px;
  }

  .panel{
    background:rgba(15,35,48,0.9);
    border-radius:14px;padding:20px;
    border:1px solid rgba(255,255,255,0.08);
    box-shadow:0 10px 40px rgba(0,229,193,0.15), 0 0 40px rgba(168,107,255,0.08);
    backdrop-filter: blur(8px);
  }

  .header{display:flex;align-items:center;gap:12px;margin-bottom:12px}
  .logo{
    width:48px;height:48px;border-radius:10px;
    background:linear-gradient(135deg,var(--accent),var(--accent-2));
    display:flex;align-items:center;justify-content:center;font-weight:700;
    color:#02101a;box-shadow:0 6px 18px rgba(0,0,0,0.5);
  }
  h1{font-size:20px;margin:0}
  p.lead{margin:6px 0 14px;color:var(--muted);font-size:13px}

  /* Form fields */
  .form-row{display:flex;gap:12px;margin-bottom:12px}
  .field{flex:1;display:flex;flex-direction:column;gap:8px}
  label{font-size:13px;color:var(--muted)}
  input,textarea{
    background:rgba(2,8,18,0.5);
    border:1px solid rgba(255,255,255,0.06);
    padding:10px 12px;border-radius:8px;color:#e6f7ff;font-size:14px;
    outline:none;
    transition:box-shadow .3s,border-color .3s;
  }
  input:focus,textarea:focus{
    border-color:var(--accent);
    box-shadow:0 0 12px rgba(0,229,193,0.25);
  }

  .btn{
    background:linear-gradient(90deg,var(--accent),var(--accent-2));
    color:#00161a;padding:10px 14px;border-radius:8px;border:none;font-weight:700;cursor:pointer;
    box-shadow:0 4px 15px rgba(0,0,0,0.4);
    transition:transform .2s;
  }
  .btn:hover{transform:scale(1.05)}
  .btn.ghost{
    background:transparent;border:1px solid rgba(255,255,255,0.2);color:var(--muted);
  }

  .cards{display:flex;flex-direction:column;gap:12px}
  .card{
    background:rgba(255,255,255,0.03);
    padding:12px;border-radius:10px;
    border:1px solid rgba(255,255,255,0.05);
    display:flex;align-items:center;justify-content:space-between;
    transition:background .3s;
  }
  .card:hover{background:rgba(255,255,255,0.07)}
  .title{font-size:13px;color:var(--muted)}
  .value{font-size:16px;font-weight:700;color:#fff;word-break:break-all}
  .copy-btn{
    padding:6px 8px;border-radius:8px;border:none;background:rgba(255,255,255,0.05);color:var(--muted);cursor:pointer;
  }
  .copy-btn:hover{background:rgba(0,229,193,0.1)}

  .result-large{
    margin-top:12px;padding:14px;border-radius:10px;
    background:linear-gradient(90deg,rgba(0,229,193,0.05),rgba(168,107,255,0.05));
    border:1px solid rgba(0,229,193,0.08);
    font-weight:700;color:#e9fffb;
  }

  .error{color:#ffada7;font-weight:700;margin-top:8px}

  /* Firma */
  .firma{
    position:absolute;
    bottom:8px;right:12px;
    font-size:12px;
    color:rgba(255,255,255,0.3);
    user-select:none;
    font-style:italic;
    letter-spacing:0.5px;
  }

  @media (max-width:980px){
    .wrap{grid-template-columns:1fr;gap:18px}
  }
</style>
</head>
<body>
  <div class="wrap" id="app">
    <!-- panel principal -->
    <div class="panel" id="leftPanel">
      <div class="header">
        <div class="logo">TC</div>
        <div>
          <h1>Calculadora de Red — Telecom</h1>
          <p class="lead">Ingresa tu IP, calcula subredes y evalúa tu fórmula VELSM.</p>
        </div>
      </div>

      <div class="form-row">
        <div class="field">
          <label>IP / CIDR</label>
          <input id="ipInput" placeholder="Ej: 192.168.1.0/24">
        </div>
        <div class="field" style="max-width:120px">
          <label>Prefijo</label>
          <input id="prefixInput" type="number" placeholder="24" min="1" max="32">
        </div>
      </div>

      <div class="form-row">
        <div class="field"><label>Máscara</label><input id="maskOut" readonly></div>
        <div class="field"><label>Hosts</label><input id="hostsOut" readonly></div>
      </div>

      <div class="form-row">
        <div class="field"><label>Dirección de Red</label><input id="networkOut" readonly></div>
        <div class="field"><label>Broadcast</label><input id="broadcastOut" readonly></div>
      </div>

      <div class="form-row">
        <div class="field"><label>Primera IP</label><input id="firstOut" readonly></div>
        <div class="field"><label>Última IP</label><input id="lastOut" readonly></div>
      </div>

      <div style="display:flex;gap:8px;margin-top:12px">
        <button class="btn" id="calcBtn">Calcular</button>
        <button class="btn ghost" id="clearBtn">Limpiar</button>
      </div>

      <hr style="margin:14px 0;border:none;border-top:1px solid rgba(255,255,255,0.05)">

      <label>Fórmula VELSM</label>
      <textarea id="formula" placeholder="Ejemplo: hosts * 1.5" style="height:80px"></textarea>
      <div style="margin-top:8px;display:flex;gap:8px">
        <button class="btn" id="evalBtn">Evaluar</button>
        <button class="btn ghost" id="exampleBtn">Ejemplo</button>
      </div>
      <div id="velsmResult" class="result-large">—</div>
      <div id="errorMsg" class="error" style="display:none"></div>
    </div>

    <!-- resultados -->
    <div class="panel">
      <h1 style="font-size:16px;margin:0 0 12px">Resultados</h1>
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
function ipToInt(ip){const p=ip.split('.').map(Number);if(p.length!==4||p.some(n=>n<0||n>255||isNaN(n)))throw Error('IP inválida');return ((p[0]<<24|p[1]<<16|p[2]<<8|p[3])>>>0);}
function intToIp(i){return[(i>>>24)&255,(i>>>16)&255,(i>>>8)&255,i&255].join('.');}
function prefixToMask(p){return intToIp(p===0?0:(~0<< (32-p))>>>0);}
function calc(ip,p){const ipi=ipToInt(ip),maskInt=(~0<<(32-p))>>>0,n=ipi&maskInt,b=n|(~maskInt>>>0);
return{mask:prefixToMask(p),network:intToIp(n),broadcast:intToIp(b),
first:intToIp(n+1),last:intToIp(b-1),hosts:(2**(32-p))-2,prefix:p,ip};}
const ipIn=document.getElementById('ipInput'),pIn=document.getElementById('prefixInput'),
calcBtn=document.getElementById('calcBtn'),clr=document.getElementById('clearBtn'),
formula=document.getElementById('formula'),evalBtn=document.getElementById('evalBtn'),
velsm=document.getElementById('velsmResult'),err=document.getElementById('errorMsg');
const outs=['mask','hosts','network','broadcast','first','last'];
function show(r){for(const k of outs){document.getElementById(k+'Out').value=r[k];const el=document.getElementById('c_'+k);if(el)el.textContent=r[k];}
document.getElementById('c_ip').textContent=r.ip;document.getElementById('c_prefix').textContent=r.prefix;}
calcBtn.onclick=()=>{try{err.style.display='none';const val=ipIn.value.trim();let ip=val,pfx=parseInt(pIn.value||24);
if(val.includes('/'))[ip,pfx]=val.split('/');show(calc(ip,parseInt(pfx)));}
catch(e){err.textContent=e.message;err.style.display='block';}};
clr.onclick=()=>{document.querySelectorAll('input,textarea').forEach(i=>i.value='');document.querySelectorAll('.value').forEach(v=>v.textContent='—');velsm.textContent='—';err.style.display='none';};
document.querySelectorAll('.copy-btn').forEach(b=>b.onclick=async()=>{const id=b.getAttribute('data-copy');const t=document.getElementById(id).textContent;await navigator.clipboard.writeText(t);b.textContent='✔';setTimeout(()=>b.textContent='Copiar',900);});
evalBtn.onclick=()=>{try{const h=parseFloat(document.getElementById('hostsOut').value)||0,p=parseFloat(pIn.value)||0;const expr=formula.value.trim();if(!expr)return;const r=Function('hosts','prefix','return '+expr)(h,p);velsm.textContent=r;}
catch(e){velsm.textContent='—';err.textContent=e.message;err.style.display='block';}};
document.getElementById('exampleBtn').onclick=()=>{formula.value='hosts * 1.5';};
</script>
</body>
</html>
