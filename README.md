<html lang="es">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>🌌 Mi Web Interactiva</title>
  <style>
    @import url('https://fonts.googleapis.com/css2?family=Orbitron:wght@500;700&family=Roboto:wght@400;700&display=swap');

    * {
      margin: 0;
      padding: 0;
      box-sizing: border-box;
      font-family: 'Roboto', sans-serif;
    }

    body {
      background: linear-gradient(135deg, #1b1b2f, #162447, #1f4068);
      color: white;
      min-height: 100vh;
      display: flex;
      flex-direction: column;
      overflow-x: hidden;
    }

    header {
      text-align: center;
      padding: 25px;
      font-size: 2.2rem;
      font-weight: bold;
      letter-spacing: 2px;
      background: rgba(255, 255, 255, 0.05);
      box-shadow: 0 2px 20px rgba(0,0,0,0.6);
      text-shadow: 0 0 8px #6a00f4, 0 0 14px #00eaff;
    }

    main {
      flex: 1;
      display: flex;
      flex-direction: column;
      align-items: center;
      text-align: center;
      padding: 40px;
      position: relative;
      z-index: 2;
    }

    h1 {
      font-size: 2.8rem;
      margin-bottom: 15px;
      background: linear-gradient(90deg, #ff6ec7, #00eaff, #a46bff);
      -webkit-background-clip: text;
      -webkit-text-fill-color: transparent;
      text-shadow: 0 0 10px rgba(255,255,255,0.3);
    }

    p {
      font-size: 1.2rem;
      margin-bottom: 40px;
      color: #e0e0e0;
      max-width: 700px;
    }

    .options {
      display: grid;
      grid-template-columns: repeat(auto-fit, minmax(220px, 1fr));
      gap: 25px;
      width: 100%;
      max-width: 1000px;
      margin-bottom: 50px;
    }

    .card {
      background: rgba(255, 255, 255, 0.08);
      padding: 30px;
      border-radius: 15px;
      text-align: center;
      cursor: pointer;
      transition: transform 0.3s, box-shadow 0.3s;
      box-shadow: 0 0 15px rgba(0,0,0,0.3);
      border: 1px solid rgba(255,255,255,0.1);
    }

    .card:hover {
      transform: translateY(-8px) scale(1.04);
      box-shadow: 0 0 20px #6a00f4, 0 0 35px #00eaff;
    }

    .card h2 {
      font-size: 1.5rem;
      margin-bottom: 10px;
      color: #00eaff;
    }

    .card p {
      font-size: 0.95rem;
      color: #d1d1d1;
    }

    /* Galería */
    .gallery {
      margin-top: 40px;
      display: grid;
      grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
      gap: 20px;
      width: 100%;
      max-width: 900px;
    }

    .gallery img {
      width: 100%;
      border-radius: 12px;
      box-shadow: 0 0 15px rgba(0,0,0,0.4);
      transition: transform 0.3s;
    }

    .gallery img:hover {
      transform: scale(1.05);
    }

    /* Radio */
    .radio-section {
      margin-top: 60px;
      padding: 20px;
      background: rgba(255, 255, 255, 0.08);
      border-radius: 15px;
      box-shadow: 0 0 20px rgba(0,0,0,0.4);
      max-width: 700px;
      width: 90%;
    }

    .radio-section h2 {
      margin-bottom: 15px;
      color: #ff6ec7;
      text-shadow: 0 0 10px rgba(255,255,255,0.3);
    }

    audio {
      width: 100%;
      margin-top: 10px;
    }

    footer {
      text-align: center;
      padding: 18px;
      font-size: 0.95rem;
      background: rgba(255, 255, 255, 0.05);
      text-shadow: 0 0 5px #6a00f4, 0 0 10px #00eaff;
    }

    /* Partículas */
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
      background: #fff;
      border-radius: 50%;
      animation: float 12s linear infinite;
      opacity: 0.5;
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
  <header>🌐 Mi Web Interactiva</header>

  <!-- Partículas -->
  <div class="particles">
    <span style="left:10%; animation-delay:0s;"></span>
    <span style="left:25%; animation-delay:2s;"></span>
    <span style="left:40%; animation-delay:4s;"></span>
    <span style="left:60%; animation-delay:6s;"></span>
    <span style="left:75%; animation-delay:8s;"></span>
    <span style="left:90%; animation-delay:10s;"></span>
  </div>

  <main>
    <h1>Bienvenido 🚀</h1>
    <p>Explora música, imágenes y más en este espacio digital con estilo futurista y vibrante.</p>

    <div class="options">
      <div class="card" onclick="window.location.href='https://www.youtube.com/'">
        <h2>🎬 Videos</h2>
        <p>Descubre el mundo audiovisual.</p>
      </div>

      <div class="card" onclick="window.location.href='https://www.spotify.com/'">
        <h2>🎵 Música</h2>
        <p>Ritmos que acompañan tu día.</p>
      </div>

      <div class="card" onclick="window.location.href='https://news.google.com/'">
        <h2>📰 Noticias</h2>
        <p>Lo último en información global.</p>
      </div>

      <div class="card" onclick="window.location.href='https://github.com/'">
        <h2>💻 Proyectos</h2>
        <p>Código e innovación.</p>
      </div>
    </div>

    <!-- Galería -->
    <div class="gallery">
      <img src="https://picsum.photos/400/300?random=1" alt="Imagen 1">
      <img src="https://picsum.photos/400/300?random=2" alt="Imagen 2">
      <img src="https://picsum.photos/400/300?random=3" alt="Imagen 3">
    </div>

    <!-- Radio -->
    <div class="radio-section">
      <h2>📻 Escucha Radio Estrella del Sur</h2>
      <audio controls autoplay>
        <source src="https://stream.zeno.fm/yqf1z4uh9mzuv" type="audio/mpeg">
        Tu navegador no soporta audio en vivo.
      </audio>
    </div>
  </main>

  <footer>© 2025 - Página futurista con música y estilo</footer>
</body>
</html>
