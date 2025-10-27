<!doctype html>
<html lang="es">
<head>
<meta charset="utf-8" />
<meta name="viewport" content="width=device-width,initial-scale=1" />
<title>Telecom — Calculadora de Red y VELSM</title>
<style>
  :root{
    --bg-1:#071322; --bg-2:#0b2540;
    --card:#0f2330;
    --accent:#00e5c1;
    --accent-2:#a86bff;
    --muted:#9fb0c6;
  }
  *{box-sizing:border-box}
  body{
    margin:0;min-height:100vh;background:linear-gradient(135deg,var(--bg-1),var(--bg-2));
    color:#dbefff;font-family:Inter,ui-sans-serif,system-ui,-apple-system,"Segoe UI",Roboto,"Helvetica Neue",Arial;
    display:flex;align-items:center;justify-content:center;padding:28px;
  }

  .wrap{
    width:100%;max-width:1100px;
    display:grid;grid-template-columns: 1fr 420px;gap:28px;
  }

  .panel{
    background:linear-gradient(180deg, rgba(255,255,255,0.02), rgba(255,255,255,0.01));
    border-radius:14px;padding:20px;border:1px solid rgba(255,255,255,0.04);
    box-shadow: 0 10px 40px rgba(2,6,23,0.6);
  }

  .header{
    display:flex;align-items:center;gap:12px;margin-bottom:12px;
  }
  .logo{
    width:48px;height:48px;border-radius:10px;background:linear-gradient(135deg,var(--accent),var(--accent-2));
    display:flex;align-items:center;justify-content:center;font-weight:700;color:#02101a;box-shadow:0 6px 18px rgba(0,0,0,0.6);
  }
  h1{font-size:20px;margin:0}
  p.lead{margin:6px 0 14px;color:var(--muted);font-size:13px}

  /* Form area */
  .form-row{display:flex;gap:12px;margin-bottom:12px}
  .field{flex:1;display:flex;flex-direction:column;gap:8px}
  label{font-size:13px;color:var(--muted)}
  input[type="text"], input[type="number"], textarea{
    background: rgba(2,8,18,0.45);
    border:1px solid rgba(255,255,255,0.04);
    padding:10px 12px;border-radius:8px;color:#e6f7ff;font-size:14px;
    outline:none;
  }
  input[type="text"]:focus, input[type="number"]:focus, textarea:focus{
    box-shadow: 0 0 14px rgba(0,229,193,0.08);
    border-color: rgba(0,229,193,0.45);
  }
  .small{font-size:12px;color:var(--muted)}

  .controls{display:flex;gap:10px;margin-top:8px}
  .btn{
    background:linear-gradient(90deg,var(--accent),var(--accent-2));
    color:#00161a;padding:10px 14px;border-radius:8px;border:none;font-weight:700;cursor:pointer;
    box-shadow: 0 6px 18px rgba(0,0,0,0.5);
  }
  .btn.ghost{
    background:transparent;border:1px solid rgba(255,255,255,0.06);color:var(--muted);font-weight:600;
  }

  /* right column cards */
  .cards{display:flex;flex-direction:column;gap:12px}
  .card{
    background:linear-gradient(180deg, rgba(255,255,255,0.015), rgba(255,255,255,0.01));
    padding:12px;border-radius:10px;border:1px solid rgba(255,255,255,0.03);display:flex;align-items:center;justify-content:space-between;
  }
  .card .title{font-size:13px;color:var(--muted)}
  .card .value{font-size:16px;font-weight:700;color:#fff;word-break:break-all}

  .grid{
    display:grid;grid-template-columns:repeat(2,1fr);gap:10px;margin-top:12px;
  }

  .calc-footer{margin-top:12px;color:var(--muted);font-size:13px}

  /* VELSM box (formula) */
  textarea.formula{height:90px;resize:vertical}
  .velsm-row{display:flex;gap:8px;align-items:center}

  .copy-btn{
    padding:6px 8px;border-radius:8px;border:none;background:rgba(255,255,255,0.03);color:var(--muted);cursor:pointer;
  }

  .result-large{
    margin-top:12px;padding:14px;border-radius:10px;background:linear-gradient(90deg, rgba(0,229,193,0.05), rgba(168,107,255,0.03));
    border:1px solid rgba(0,229,193,0.06);font-weight:700;color:#e9fffb;
  }

  .error{color:#ffada7;font-weight:700;margin-top:8px}

  @media (max-width:980px){
    .wrap{grid-template-columns:1fr; padding:12px}
    .cards{flex-direction:row;flex-wrap:wrap}
    .card{flex:1 1 calc(50% - 10px)}
  }
</style>
</head>
<body>
  <div class="wrap">
    <!-- left: inputs & formula -->
    <div class="panel">
      <div class="header">
        <div class="logo">TC</div>
        <div>
          <h1>Calculadora de Red — Telecom</h1>
          <p class="lead">Ingresa IP o IP/CIDR y presiona <strong>Calcular</strong>. Luego pega tu fórmula en VELSM y presiona Evaluar.</p>
        </div>
      </div>

      <!-- inputs -->
      <div class="form-row">
        <div class="field">
          <label for="ipInput">IP (puedes usar notación con /CIDR o ingresar IP y prefijo separado)</label>
          <input id="ipInput" type="text" placeholder="Ej: 10.0.0.1 o 10.0.0.0/24">
        </div>
        <div class="field" style="max-width:120px">
          <label for="prefixInput">Prefijo (CIDR)</label>
          <input id="prefixInput" type="number" min="1" max="32" placeholder="24">
        </div>
      </div>

      <div class="form-row">
        <div class="field">
          <label>Máscara</label>
          <input id="maskOut" type="text" readonly placeholder="255.255.255.0">
        </div>
        <div class="field">
          <label>Hosts disponibles</label>
          <input id="hostsOut" type="text" readonly placeholder="254">
        </div>
      </div>

      <div class="form-row">
        <div class="field">
          <label>Dirección de Red</label>
          <input id="networkOut" type="text" readonly placeholder="10.0.0.0">
        </div>
        <div class="field">
          <label>Broadcast</label>
          <input id="broadcastOut" type="text" readonly placeholder="10.0.0.255">
        </div>
      </div>

      <div class="form-row">
        <div class="field">
          <label>Primera IP usable</label>
          <input id="firstOut" type="text" readonly placeholder="10.0.0.1">
        </div>
        <div class="field">
          <label>Última IP usable</label>
          <input id="lastOut" type="text" readonly placeholder="10.0.0.254">
        </div>
      </div>

      <div style="display:flex;gap:8px;margin-top:12px">
        <button class="btn" id="calcBtn">Calcular</button>
        <button class="btn ghost" id="clearBtn">Limpiar</button>
        <button class="btn ghost" id="copyAll">Copiar todo</button>
      </div>

      <hr style="margin:14px 0;border:none;border-top:1px solid rgba(255,255,255,0.03)">

      <!-- VELSM formula -->
      <div>
        <label for="formula">Fórmula VELSM (usa variables: <code>hosts</code>, <code>prefix</code>, <code>mask</code>, <code>network</code>, <code>broadcast</code>, <code>first</code>, <code>last</code>)</label>
        <textarea id="formula" class="formula" placeholder="Ejemplo: hosts * 1.5"></textarea>
        <div class="velsm-row" style="margin-top:8px">
          <button class="btn" id="evalBtn">Evaluar fórmula</button>
          <button class="btn ghost" id="pasteExample">Ejemplo</button>
          <div style="flex:1"></div>
          <div class="small">Resultado VELSM:</div>
        </div>
        <div id="velsmResult" class="result-large">—</div>
        <div id="err" class="error" style="display:none"></div>
      </div>

    </div>

    <!-- right: results cards -->
    <div class="panel">
      <div style="display:flex;justify-content:space-between;align-items:center">
        <div>
          <h1 style="font-size:16px;margin:0">Resultados</h1>
          <div class="small">Valores calculados</div>
        </div>
        <div class="small">Telecom • Demo</div>
      </div>

      <div class="cards" style="margin-top:16px">
        <div class="card"><div><div class="title">IP ingresada</div><div id="c_ip" class="value">—</div></div>
          <div style="display:flex;gap:8px"><button class="copy-btn" data-copy="c_ip">Copiar</button></div>
        </div>

        <div class="card"><div><div class="title">Prefijo</div><div id="c_prefix" class="value">—</div></div>
          <div><button class="copy-btn" data-copy="c_prefix">Copiar</button></div>
        </div>

        <div class="card"><div><div class="title">Máscara</div><div id="c_mask" class="value">—</div></div>
          <div><button class="copy-btn" data-copy="c_mask">Copiar</button></div>
        </div>

        <div class="card"><div><div class="title">Dirección red</div><div id="c_network" class="value">—</div></div>
          <div><button class="copy-btn" data-copy="c_network">Copiar</button></div>
        </div>

        <div class="card"><div><div class="title">Broadcast</div><div id="c_broadcast" class="value">—</div></div>
          <div><button class="copy-btn" data-copy="c_broadcast">Copiar</button></div>
        </div>

        <div class="card"><div><div class="title">1ª IP</div><div id="c_first" class="value">—</div></div>
          <div><button class="copy-btn" data-copy="c_first">Copiar</button></div>
        </div>

        <div class="card"><div><div class="title">Última IP</div><div id="c_last" class="value">—</div></div>
          <div><button class="copy-btn" data-copy="c_last">Copiar</button></div>
        </div>

        <div class="card"><div><div class="title">Hosts</div><div id="c_hosts" class="value">—</div></div>
          <div><button class="copy-btn" data-copy="c_hosts">Copiar</button></div>
        </div>

      </div>

      <div class="calc-footer">
        Consejo: puedes introducir IP en formato <code>10.0.0.0/24</code> o IP + prefijo por separado.
      </div>
    </div>
  </div>

<script>
// --- Utilities for IP math ---
function ipToInt(ip){
  const parts = ip.trim().split('.');
  if(parts.length !== 4) throw new Error('IP inválida');
  return parts.reduce((acc,p)=> (acc<<8) + (parseInt(p,10)||0), 0) >>> 0;
}
function intToIp(int){
  return [(int>>>24)&255, (int>>>16)&255, (int>>>8)&255, int&255].join('.');
}
function prefixToMask(prefix){
  const maskInt = prefix === 0 ? 0 : (~0 << (32 - prefix)) >>> 0;
  return intToIp(maskInt);
}
function maskToPrefix(mask){
  const m = ipToInt(mask);
  // count ones
  let cnt = 0;
  for(let i=31;i>=0;i--) if(m & (1<<i)) cnt++;
  return cnt;
}

// calculate network data
function calcNetwork(ipStr, prefix){
  const ipInt = ipToInt(ipStr);
  const maskInt = prefix === 0 ? 0 : (~0 << (32 - prefix)) >>> 0;
  const networkInt = ipInt & maskInt;
  const broadcastInt = networkInt | (~maskInt >>> 0);
  const hosts = (prefix>=31) ? (prefix===31?2:1) : (Math.pow(2, 32 - prefix) - 2);
  let first = null, last = null;
  if(prefix <= 30){
    first = networkInt + 1;
    last = broadcastInt - 1;
  } else if(prefix === 31){
    first = networkInt;
    last = broadcastInt; // /31 two hosts (RFC)
  } else {
    first = networkInt;
    last = networkInt;
  }
  return {
    ipInt, maskInt, networkInt, broadcastInt, firstInt:first, lastInt:last, hosts
  }
}

// --- DOM ---
const ipInput = document.getElementById('ipInput');
const prefixInput = document.getElementById('prefixInput');
const calcBtn = document.getElementById('calcBtn');
const clearBtn = document.getElementById('clearBtn');
const copyAll = document.getElementById('copyAll');

const maskOut = document.getElementById('maskOut');
const hostsOut = document.getElementById('hostsOut');
const networkOut = document.getElementById('networkOut');
const broadcastOut = document.getElementById('broadcastOut');
const firstOut = document.getElementById('firstOut');
const lastOut = document.getElementById('lastOut');

const c_ip = document.getElementById('c_ip');
const c_prefix = document.getElementById('c_prefix');
const c_mask = document.getElementById('c_mask');
const c_network = document.getElementById('c_network');
const c_broadcast = document.getElementById('c_broadcast');
const c_first = document.getElementById('c_first');
const c_last = document.getElementById('c_last');
const c_hosts = document.getElementById('c_hosts');

const formulaEl = document.getElementById('formula');
const evalBtn = document.getElementById('evalBtn');
const velsmResult = document.getElementById('velsmResult');
const errEl = document.getElementById('err');
const pasteExample = document.getElementById('pasteExample');

// helper: set results
function setAll(vals){
  c_ip.textContent = vals.ip || '—';
  c_prefix.textContent = vals.prefix != null ? vals.prefix : '—';
  c_mask.textContent = vals.mask || '—';
  c_network.textContent = vals.network || '—';
  c_broadcast.textContent = vals.broadcast || '—';
  c_first.textContent = vals.first || '—';
  c_last.textContent = vals.last || '—';
  c_hosts.textContent = vals.hosts != null ? vals.hosts : '—';

  maskOut.value = vals.mask || '';
  hostsOut.value = vals.hosts != null ? vals.hosts : '';
  networkOut.value = vals.network || '';
  broadcastOut.value = vals.broadcast || '';
  firstOut.value = vals.first || '';
  lastOut.value = vals.last || '';
  prefixInput.value = vals.prefix != null ? vals.prefix : '';
  ipInput.value = vals.ip || '';
}

// parse input and compute
function computeFromInputs(){
  errEl.style.display = 'none';
  let ipText = ipInput.value.trim();
  let prefix = null;
  try{
    if(ipText.includes('/')){
      const [ipPart, prePart] = ipText.split('/');
      ipText = ipPart.trim();
      prefix = parseInt(prePart.trim(),10);
      if(isNaN(prefix) || prefix < 0 || prefix > 32) throw new Error('Prefijo inválido');
    } else {
      // check prefix field
      if(prefixInput.value) {
        prefix = parseInt(prefixInput.value,10);
        if(isNaN(prefix) || prefix < 0 || prefix > 32) throw new Error('Prefijo inválido');
      } else {
        throw new Error('Ingresa IP en formato CIDR (10.0.0.0/24) o completa Prefijo');
      }
    }
    // validate ip numbers
    const parts = ipText.split('.');
    if(parts.length !==4) throw new Error('IP inválida (usa 4 octetos)');
    parts.forEach(p=>{
      const n = parseInt(p,10);
      if(isNaN(n) || n<0 || n>255) throw new Error('IP inválida (octeto fuera de rango)');
    });

    // compute
    const net = calcNetwork(ipText, prefix);
    const maskStr = prefixToMask(prefix);
    const networkStr = intToIp(net.networkInt);
    const broadcastStr = intToIp(net.broadcastInt);
    const firstStr = net.firstInt != null ? intToIp(net.firstInt) : '-';
    const lastStr = net.lastInt != null ? intToIp(net.lastInt) : '-';
    const hosts = net.hosts;
    const vals = {
      ip: ipText, prefix, mask: maskStr, network: networkStr, broadcast: broadcastStr,
      first: firstStr, last: lastStr, hosts
    };
    setAll(vals);
    return vals;
  } catch(err){
    errEl.style.display = 'block';
    errEl.textContent = 'Error: ' + (err.message || err);
    throw err;
  }
}

// events
calcBtn.addEventListener('click', ()=>{
  try{
    computeFromInputs();
    velsmResult.textContent = '—';
    errEl.style.display = 'none';
  } catch(e){
    // error already shown
  }
});

clearBtn.addEventListener('click', ()=>{
  ipInput.value='';prefixInput.value='';maskOut.value='';hostsOut.value='';networkOut.value='';broadcastOut.value='';firstOut.value='';lastOut.value='';formulaEl.value=''; velsmResult.textContent='—'; errEl.style.display='none';
  setAll({});
});

copyAll.addEventListener('click', async ()=>{
  try{
    const text = `IP: ${c_ip.textContent}\nPrefijo: ${c_prefix.textContent}\nMáscara: ${c_mask.textContent}\nNetwork: ${c_network.textContent}\nBroadcast: ${c_broadcast.textContent}\nFirst: ${c_first.textContent}\nLast: ${c_last.textContent}\nHosts: ${c_hosts.textContent}\nVELSM: ${velsmResult.textContent}`;
    await navigator.clipboard.writeText(text);
    alert('Copiado al portapapeles');
  } catch(e){
    alert('No se pudo copiar: ' + e);
  }
});

// copy buttons
document.querySelectorAll('.copy-btn').forEach(btn=>{
  btn.addEventListener('click', async ()=>{
    const id = btn.getAttribute('data-copy');
    const el = document.getElementById(id);
    if(!el) return;
    const txt = el.textContent || el.value || '';
    try{
      await navigator.clipboard.writeText(txt);
      btn.textContent = '✔';
      setTimeout(()=> btn.textContent = 'Copiar',900);
    }catch(e){
      alert('Error copiando: '+e);
    }
  });
});

// Evaluate user formula safely-ish
// We allow arithmetic + Math functions. We replace variable names with numeric values and use Function to evaluate.
// WARNING: this runs in-browser; no server calls. Keep it local.
function safeEvalFormula(formula, context){
  // allow only characters: numbers, spaces, letters, basic ops and parentheses, comma, dot
  // remove any suspicious tokens like "window", "document", "fetch", etc.
  const blacklist = ['window','document','eval','fetch','XMLHttpRequest','localStorage','sessionStorage','location','navigator'];
  for(const b of blacklist) if(formula.includes(b)) throw new Error('Fórmula contiene palabra no permitida');

  // Replace variable names with context values (numbers or quoted)
  // variables: hosts, prefix, mask, network, broadcast, first, last
  const vars = Object.keys(context);
  let expr = formula;
  // Replace each variable name as whole word
  vars.forEach(v=>{
    const re = new RegExp('\\b'+v+'\\b','g');
    const val = context[v];
    // if value is numeric, insert number; else if string, wrap quotes
    if(typeof val === 'number') expr = expr.replace(re, String(val));
    else if(typeof val === 'string') expr = expr.replace(re, '`'+val+'`'); // backtick to represent string in eval
    else expr = expr.replace(re, String(val));
  });

  // Allow Math. functions by prefacing Math.
  // e.g. user may write max(hosts,10) -> we provide Math.max by mapping common fn names
  // For simplicity allow calling Math.* by requiring user writes Math.*.
  // Validate allowed characters still
  if(!/^[\d\s\+\-\*\/\%\(\)\.\,\`\w]+$/.test(expr)) throw new Error('Fórmula contiene caracteres no permitidos');
  // Evaluate expression using Function
  try{
    // Create a safe function with Math in scope
    const fn = new Function('Math', 'return ('+expr+');');
    const res = fn(Math);
    if(typeof res === 'number' && !isFinite(res)) throw new Error('Resultado no finito');
    return res;
  } catch(e){
    throw new Error('Error al evaluar fórmula: ' + (e.message||e));
  }
}

evalBtn.addEventListener('click', ()=>{
  errEl.style.display='none';
  try{
    const vals = computeFromInputs(); // ensure values exist / updated
    // build context
    const ctx = {
      hosts: Number(vals.hosts),
      prefix: Number(vals.prefix),
      mask: vals.mask,
      network: vals.network,
      broadcast: vals.broadcast,
      first: vals.first,
      last: vals.last
    };
    const formula = formulaEl.value.trim();
    if(!formula) { alert('Pega tu fórmula en el cuadro'); return; }
    const res = safeEvalFormula(formula, ctx);
    velsmResult.textContent = String(res);
  } catch(err){
    errEl.style.display='block';
    errEl.textContent = 'Error: ' + (err.message||err);
  }
});

// paste example formula
pasteExample.addEventListener('click', ()=>{
  formulaEl.value = 'hosts * 1.5'; // ejemplo básico
});

// initialize blank
setAll({});

</script>
</body>
</html>
