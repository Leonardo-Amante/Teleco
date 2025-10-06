<html lang="es">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Mi Web Interactiva</title>
  <style>
    @import url('https://fonts.googleapis.com/css2?family=Poppins:wght@400;600;700&display=swap');

    * {
      margin: 0;
      padding: 0;
      box-sizing: border-box;
      font-family: 'Poppins', sans-serif;
    }

    body {
      background: linear-gradient(135deg, #141e30, #243b55);
      color: white;
      min-height: 100vh;
      display: flex;
      flex-direction: column;
    }

    header {
      text-align: center;
      padding: 20px;
      font-size: 1.8rem;
      font-weight: bold;
      letter-spacing: 2px;
      background: rgba(255, 255, 255, 0.05);
      box-shadow: 0 2px 10px rgba(0,0,0,0.4);
    }

    main {
      flex: 1;
      display: flex;
      flex-direction: column;
      justify-content: center;
      align-items: center;
      text-align: center;
      padding: 30px;
    }

    h1 {
      font-size: 2.8rem;
      margin-bottom: 15px;
      background: linear-gradient(90deg, #00ffae, #00b3ff);
      -webkit-background-clip: text;
      -webkit-text-fill-color: transparent;
      animation: fadeIn 2s ease;
    }

    p {
      font-size: 1.2rem;
      margin-bottom: 40px;
      color: #dfe6e9;
      max-width: 600px;
      animation: fadeIn 3s ease;
    }

    .options {
      display: grid;
      grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
      gap: 20px;
      width: 100%;
      max-width: 900px;
    }

    .card {
      background: rgba(255, 255, 255, 0.08);
      padding: 30px;
      border-radius: 15px;
      text-align: center;
      cursor: pointer;
      transition: transform 0.3s, background 0.3s;
      box-shadow: 0 5px 15px rgba(0,0,0,0.3);
    }

    .card:hover {
      transform: translateY(-8px) scale(1.05);
      background: rgba(0, 255, 174, 0.2);
    }

    .card h2 {
      font-size: 1.5rem;
      margin-bottom: 10px;
      color: #00ffae;
    }

    .card p {
      font-size: 1rem;
      color: #ccc;
    }

    footer {
      text-align: center;
      padding: 15px;
      font-size: 0.9rem;
      background: rgba(255, 255, 255, 0.05);
    }

    @keyframes fadeIn {
      from { opacity: 0; transform: translateY(20px); }
      to { opacity: 1; transform: translateY(0); }
    }
  </style>
</head>
<body>
  <header>🌐 Mi Web Interactiva</header>

  <main>
    <h1>Bienvenido</h1>
    <p>Explora las distintas secciones y descubre todo lo que tenemos para ti.</p>

    <div class="options">
      <div class="card" onclick="window.location.href='https://www.youtube.com/'">
        <h2>🎬 Videos</h2>
        <p>Mira contenido audiovisual interesante.</p>
      </div>

      <div class="card" onclick="window.location.href='https://www.spotify.com/'">
        <h2>🎵 Música</h2>
        <p>Escucha playlists y estaciones de radio.</p>
      </div>

      <div class="card" onclick="window.location.href='https://news.google.com/'">
        <h2>📰 Noticias</h2>
        <p>Mantente informado con las últimas novedades.</p>
      </div>

      <div class="card" onclick="window.location.href='https://github.com/'">
        <h2>💻 Proyectos</h2>
        <p>Descubre mis trabajos y proyectos en GitHub.</p>
      </div>

      <div class="card" onclick="window.location.href='https://www.instagram.com/'">
        <h2>📸 Galería</h2>
        <p>Explora imágenes y momentos especiales.</p>
      </div>
    </div>
  </main>

  <footer>© 2025 - Creado con estilo ✨</footer>
</body>
</html>
