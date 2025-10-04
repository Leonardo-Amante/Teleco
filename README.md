<html lang="es">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Pantalla de Hackeo Verde</title>
<style>
  body {
    margin: 0;
    padding: 0;
    background-color: black;
    color: #00FF00;
    font-family: 'Courier New', monospace;
    overflow: hidden;
  }
  .console {
    position: absolute;
    top: 10%;
    left: 50%;
    transform: translateX(-50%);
    width: 80%;
    height: 60%;
    border: 2px solid #00FF00;
    padding: 10px;
    box-sizing: border-box;
    overflow-y: auto;
    background-color: black;
    opacity: 0.8;
  }
  .console p {
    margin: 0;
    padding: 0;
    white-space: nowrap;
    animation: scrollText 5s linear infinite;
  }
  @keyframes scrollText {
    0% { transform: translateY(100%); }
    100% { transform: translateY(-100%); }
  }
  .rain {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    pointer-events: none;
    z-index: 1;
  }
  .rain span {
    position: absolute;
    width: 2px;
    height: 20px;
    background: rgba(0, 255, 0, 0.8);
    animation: fall linear infinite;
  }
  @keyframes fall {
    to { transform: translateY(100vh); }
  }
</style>
</head>
<body>

<div class="console">
  <p>Conectando a la red...</p>
  <p>Acceso concedido...</p>
  <p>Iniciando protocolo de seguridad...</p>
  <p>Estableciendo conexión segura...</p>
</div>
<div class="rain"></div>

<!-- Sonidos -->
<audio id="keyboardSound" src="https://freesound.org/data/previews/350/350016_4019026-lq.mp3" loop></audio>
<audio id="errorSound" src="https://freesound.org/data/previews/175/175788_2859973-lq.mp3" loop></audio>

<script>
  // Lluvia de caracteres
  const rain = document.querySelector('.rain');
  for (let i = 0; i < 100; i++) {
    const span = document.createElement('span');
    span.style.left = `${Math.random() * 100}vw`;
    span.style.animationDuration = `${Math.random() * 2 + 3}s`;
    rain.appendChild(span);
  }

  // Reproducir sonidos
  const keyboard = document.getElementById('keyboardSound');
  const error = document.getElementById('errorSound');

  // Para reproducir automáticamente al cargar
  window.addEventListener('load', () => {
    keyboard.volume = 0.15;
    error.volume = 0.2;
    keyboard.play().catch(()=>console.log("Necesita interacción para reproducir audio"));
    error.play().catch(()=>console.log("Necesita interacción para reproducir audio"));
  });

  // Mensajes de consola aleatorios
  const consoleEl = document.querySelector('.console');
  const messages = [
    "root@server: acceso detectado",
    "ssh: intento escalada privilegios",
    "fopen('/etc/passwd') -> SUCCESS",
    "backup: cifrado iniciado",
    "conexiones externas detectadas",
    "alerta: integridad comprometida",
    "modulo corrompido"
  ];

  setInterval(() => {
    const p = document.createElement('p');
    p.textContent = messages[Math.floor(Math.random()*messages.length)];
    consoleEl.appendChild(p);
    consoleEl.scrollTop = consoleEl.scrollHeight;
    if(consoleEl.children.length > 50) consoleEl.removeChild(consoleEl.children[0]);
  }, 900);
</script>

</body>
</html>
