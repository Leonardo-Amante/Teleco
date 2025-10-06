<html lang="es">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>🌌 Mi Web Interactiva</title>
  <style>
    @import url('https://fonts.googleapis.com/css2?family=Orbitron:wght@500;700&display=swap');

    * {
      margin: 0;
      padding: 0;
      box-sizing: border-box;
      font-family: 'Orbitron', sans-serif;
    }

    body {
      background: radial-gradient(circle at top, #0f0c29, #302b63, #24243e);
      color: white;
      min-height: 100vh;
      display: flex;
      flex-direction: column;
      overflow-x: hidden;
    }

    header {
      text-align: center;
      padding: 25px;
      font-size: 2rem;
      font-weight: bold;
      letter-spacing: 3px;
      background: rgba(255, 255, 255, 0.05);
      box-shadow: 0 2px 20px rgba(0,0,0,0.6);
      text-shadow: 0 0 10px #00ffe7, 0 0 20px #ff00ff;
    }

    main {
      flex: 1;
      display: flex;
      flex-direction: column;
      justify-content: center;
      align-items: center;
      text-align: center;
      padding: 40px;
      position: relative;
      z-index: 2;
    }

    h1 {
      font-size: 3rem;
      margin-bottom: 20px;
      background: linear-gradient(90deg, #ff00ff, #00ffff, #ff00ff);
      -webkit-background-clip: text;
      -webkit-text-fill-color: transparent;
      animation: glow 2s infinite alternate;
    }

    p {
      font-size: 1.2rem;
      margin-bottom: 50px;
      color: #dfe6e9;
      max-width: 650px;
    }

    .options {
      display: grid;
      grid-template-columns: repeat(auto-fit, minmax(220px, 1fr));
      gap: 25px;
      width: 100%;
      max-width: 950px;
    }

    .card {
      background: rgba(255, 255, 255, 0.05);
      padding: 35px;
      border-radius: 18px;
      text-align: center;
      cursor: pointer;
      transition: transform 0.3s, box-shadow 0.3s;
      box-shadow: 0 0 15px rgba(0,255,200,0.2);
      border: 1px solid rgba(0,255,200,0.3);
    }

    .card:hover {
      transform: translateY(-10px) scale(1.05);
      box-shadow: 0 0 25px #00ffe7, 0 0 50px #ff00ff;
    }

    .card h2 {
      font-size: 1.6rem;
      margin-bottom: 12px;
      color: #00ffe7;
      text-shadow: 0 0 10px #00ffe7;
    }

    .card p {
      font-size: 1rem;
      color: #ccc;
    }

    footer {
      text-align: center;
      padding: 18px;
      font-size: 0.95rem;
      background: rgba(255, 255, 255, 0.05);
      text-shadow: 0 0 5px #ff00ff, 0 0 10px #00ffe7;
    }

    /* Animaciones */
    @keyframes glow {
      from {
        text-shadow: 0 0 10px #00ffe7, 0 0 20px #ff00ff;
      }
      to {
        text-shadow: 0 0 20px #ff00ff, 0 0 40px #00ffe7;
      }
    }

    /* Fondo partículas */
    .particles {
      position: absolute;
      top: 0;
      left: 0;
      width: 100%;
      height: 100%;
      z-index: 1;
      overflow: hidden;
    }

    .particles span {
      position: absolute;
      display: block;
      width: 4px;
      height: 4px;
      background: #00ffe7;
      border-radius: 50%;
      animation: float 10s linear infinite;
      opacity: 0.7;
    }

    @keyframes float {
      0% {
        transform: translateY(100vh) scale(0);
        opacity: 0;
      }
      50% {
        opacity: 1;
      }
      100% {
        transform: translateY(-10vh) scale(1);
        opacity: 0;
      }
    }
  </style>
</head>
<body>
  <header>🌐 Mi Web Neón Interactiva</header>

  <!-- Partículas -->
  <div class="particles">
    <span style="left:10%; animation-delay:0s;"></span>
    <span style="left:20%; animation-delay:2s;"></span>
    <span style="left:40%; animation-delay:4s;"></span>
    <span style="left:60%; animation-delay:6s;"></span>
    <span style="left:80%; animation-delay:8s;"></span>
    <span style="left:90%; animation-delay:10s;"></span>
  </div>

  <main>
    <h1>Bienvenido al Futuro 🚀</h1>
    <p>Explora un mundo lleno de música, noticias, videos y más. Haz clic en cualquiera de las secciones y disfruta de la experiencia neón.</p>

    <div class="options">
      <div class="card" onclick="window.location.href='https://www.youtube.com/'">
        <h2>🎬 Videos</h2>
        <p>Explora el universo audiovisual más vibrante.</p>
      </div>

      <div class="card" onclick="window.location.href='https://www.spotify.com/'">
        <h2>🎵 Música</h2>
        <p>Ritmos que iluminan tu día y tu noche.</p>
      </div>

      <div class="card" onclick="window.location.href='https://news.google.com/'">
        <h2>📰 Noticias</h2>
        <p>Siempre actualizado con lo último del mundo.</p>
      </div>

      <div class="card" onclick="window.location.href='https://github.com/'">
        <h2>💻 Proyectos</h2>
        <p>Innovación, código y creatividad en un solo lugar.</p>
      </div>

      <div class="card" onclick="window.location.href='https://www.instagram.com/'">
        <h2>📸 Galería</h2>
        <p>Imágenes que brillan como luces de neón.</p>
      </div>
    </div>
  </main>

  <footer>© 2025 - Página futurista creada con ⚡ neón vibes</footer>
</body>
</html>
