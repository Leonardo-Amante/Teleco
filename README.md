<html lang="es">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>🌟 Radio Estrella LS</title>
  <style>
    @import url('https://fonts.googleapis.com/css2?family=Orbitron:wght@500;700&family=Roboto:wght@400;700&display=swap');

    * {
      margin: 0;
      padding: 0;
      box-sizing: border-box;
      font-family: 'Roboto', sans-serif;
    }

    body {
      background: linear-gradient(135deg, #1a1a2e, #16213e, #0f3460);
      color: white;
      min-height: 100vh;
      display: flex;
      flex-direction: column;
    }

    header {
      padding: 20px 40px;
      display: flex;
      justify-content: space-between;
      align-items: center;
      background: rgba(255, 255, 255, 0.05);
      backdrop-filter: blur(8px);
      box-shadow: 0 2px 20px rgba(0,0,0,0.5);
    }

    header h1 {
      font-family: 'Orbitron', sans-serif;
      font-size: 1.8rem;
      color: #00eaff;
      text-shadow: 0 0 10px #00eaff, 0 0 20px #ff6ec7;
    }

    nav a {
      margin-left: 20px;
      text-decoration: none;
      color: #fff;
      font-weight: bold;
      transition: color 0.3s;
    }

    nav a:hover {
      color: #00eaff;
    }

    .hero {
      text-align: center;
      padding: 80px 20px;
      background: linear-gradient(180deg, rgba(0,0,0,0.6), rgba(0,0,0,0.9)),
                  url("https://images.unsplash.com/photo-1526470608268-f674ce90ebd4?auto=format&fit=crop&w=1600&q=80") center/cover no-repeat;
      color: white;
    }

    .hero h2 {
      font-size: 3rem;
      margin-bottom: 20px;
      background: linear-gradient(90deg, #ff6ec7, #00eaff, #a46bff);
      -webkit-background-clip: text;
      -webkit-text-fill-color: transparent;
    }

    .hero p {
      font-size: 1.2rem;
      max-width: 600px;
      margin: auto;
      color: #ddd;
    }

    main {
      flex: 1;
      padding: 40px 20px;
      text-align: center;
    }

    .options {
      display: grid;
      grid-template-columns: repeat(auto-fit, minmax(220px, 1fr));
      gap: 25px;
      margin: 50px auto;
      max-width: 1000px;
    }

    .card {
      background: rgba(255, 255, 255, 0.08);
      padding: 30px;
      border-radius: 15px;
      transition: transform 0.3s, box-shadow 0.3s;
      cursor: pointer;
      box-shadow: 0 0 15px rgba(0,0,0,0.3);
    }

    .card:hover {
      transform: translateY(-8px);
      box-shadow: 0 0 20px #6a00f4, 0 0 35px #00eaff;
    }

    .card h3 {
      margin-bottom: 10px;
      color: #00eaff;
    }

    .gallery {
      margin: 60px auto;
      display: grid;
      grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
      gap: 20px;
      max-width: 1000px;
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

    .radio-section {
      margin: 60px auto;
      padding: 30px;
      background: rgba(255, 255, 255, 0.08);
      border-radius: 15px;
      max-width: 700px;
      box-shadow: 0 0 20px rgba(0,0,0,0.5);
    }

    .radio-section h2 {
      margin-bottom: 15px;
      color: #ff6ec7;
    }

    audio {
      width: 100%;
      margin-top: 10px;
    }

    footer {
      text-align: center;
      padding: 18px;
      background: rgba(255, 255, 255, 0.05);
      font-size: 0.9rem;
      text-shadow: 0 0 5px #6a00f4, 0 0 10px #00eaff;
    }
  </style>
</head>
<body>
  <header>
    <h1>🌟 Radio Estrella LS</h1>
    <nav>
      <a href="#opciones">Opciones</a>
      <a href="#galeria">Galería</a>
      <a href="#radio">Radio</a>
    </nav>
  </header>

  <section class="hero">
    <h2>La radio que ilumina tu día</h2>
    <p>Sintoniza lo mejor de la música popular, cumbias, rancheras y mucho más. Bienvenido a Radio Estrella LS.</p>
  </section>

  <main>
    <!-- Opciones -->
    <div id="opciones" class="options">
      <div class="card">
        <h3>🎵 Música</h3>
        <p>Explora los mejores ritmos de Chile y Latinoamérica.</p>
      </div>
      <div class="card">
        <h3>📰 Noticias</h3>
        <p>Información local y nacional actualizada.</p>
      </div>
      <div class="card">
        <h3>🎉 Eventos</h3>
        <p>Conciertos, festivales y transmisiones especiales.</p>
      </div>
      <div class="card">
        <h3>📸 Galería</h3>
        <p>Revive los mejores momentos en imágenes.</p>
      </div>
    </div>

    <!-- Galería -->
    <div id="galeria" class="gallery">
      <img src="https://picsum.photos/500/300?random=10" alt="Imagen 1">
      <img src="https://picsum.photos/500/300?random=20" alt="Imagen 2">
      <img src="https://picsum.photos/500/300?random=30" alt="Imagen 3">
      <img src="https://picsum.photos/500/300?random=40" alt="Imagen 4">
    </div>

    <!-- Radio -->
    <div id="radio" class="radio-section">
      <h2>📻 Escucha en vivo Radio Estrella LS</h2>
      <audio controls autoplay>
        <source src="https://radioestrellals.cl:8443/live" type="audio/mpeg">
        Tu navegador no soporta la radio en vivo.
      </audio>
    </div>
  </main>

  <footer>
    © 2025 Radio Estrella LS - Todos los derechos reservados
  </footer>
</body>
</html>
