<html lang="es">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Página Telecomunicaciones</title>
  <style>
    /* Fondo animado */
    body {
      margin: 0;
      font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
      color: #fff;
      background: linear-gradient(270deg, #0f0f0f, #1c1c1c, #141414);
      background-size: 600% 600%;
      animation: fondo 15s ease infinite;
      overflow-x: hidden;
    }

    @keyframes fondo {
      0% { background-position: 0% 50%; }
      50% { background-position: 100% 50%; }
      100% { background-position: 0% 50%; }
    }

    header {
      text-align: center;
      padding: 20px;
      font-size: 2.5rem;
      font-weight: bold;
      color: #00ffff;
      text-shadow: 0 0 10px #00ffff, 0 0 20px #009999;
    }

    nav {
      text-align: center;
      margin: 20px 0;
    }

    nav button {
      margin: 10px;
      padding: 10px 20px;
      border: none;
      border-radius: 8px;
      background: #111;
      color: #0ff;
      font-size: 1rem;
      cursor: pointer;
      box-shadow: 0 0 5px #0ff, 0 0 10px #0ff;
      transition: transform 0.2s;
    }

    nav button:hover {
      transform: scale(1.1);
      background: #0ff;
      color: #000;
    }

    section {
      max-width: 900px;
      margin: 30px auto;
      padding: 20px;
      background: rgba(20, 20, 20, 0.8);
      border-radius: 12px;
      box-shadow: 0 0 20px rgba(0,255,255,0.3);
      text-align: center;
    }

    section h2 {
      color: #ff00ff;
      text-shadow: 0 0 8px #ff00ff;
    }

    section p {
      font-size: 1.2rem;
      line-height: 1.5;
    }

    /* Imagenes */
    .imagenes {
      display: flex;
      justify-content: center;
      gap: 20px;
      margin: 20px 0;
    }

    .imagenes img {
      width: 180px;
      border-radius: 12px;
      box-shadow: 0 0 15px #0ff;
      transition: transform 0.3s;
    }

    .imagenes img:hover {
      transform: scale(1.1);
    }

    /* Radio */
    .radio {
      margin-top: 20px;
      padding: 15px;
      background: #111;
      border-radius: 12px;
      box-shadow: 0 0 15px #0ff;
    }

    footer {
      text-align: center;
      padding: 20px;
      font-size: 0.9rem;
      color: #888;
    }
  </style>
</head>
<body>
  <header>
    🌐 Telecomunicaciones del Futuro
  </header>

  <nav>
    <button onclick="mostrar('quees')">¿Qué es?</button>
    <button onclick="mostrar('importancia')">Importancia</button>
    <button onclick="mostrar('aplicaciones')">Aplicaciones</button>
    <button onclick="mostrar('radio')">Escuchar Radio</button>
  </nav>

  <section id="quees">
    <h2>¿Qué son las Telecomunicaciones?</h2>
    <p>
      Las telecomunicaciones permiten la transmisión de información a distancia 
      mediante diversos medios como ondas de radio, cables, satélites y redes digitales.
    </p>
    <div class="imagenes">
      <img src="https://upload.wikimedia.org/wikipedia/commons/thumb/3/3c/Satellite_dish_1.png/600px-Satellite_dish_1.png" alt="Antena">
      <img src="https://upload.wikimedia.org/wikipedia/commons/thumb/1/14/Satellite_icon.svg/800px-Satellite_icon.svg.png" alt="Satélite">
    </div>
  </section>

  <section id="importancia" style="display:none;">
    <h2>Importancia</h2>
    <p>
      Gracias a las telecomunicaciones estamos conectados en todo momento. 
      Desde llamadas telefónicas hasta Internet, son clave en la sociedad moderna.
    </p>
    <div class="imagenes">
      <img src="https://upload.wikimedia.org/wikipedia/commons/thumb/a/ae/Internet_map_1024.jpg/640px-Internet_map_1024.jpg" alt="Redes">
      <img src="https://upload.wikimedia.org/wikipedia/commons/thumb/d/d4/Internet_map_1024_-_transparent.png/600px-Internet_map_1024_-_transparent.png" alt="Mapa">
    </div>
  </section>

  <section id="aplicaciones" style="display:none;">
    <h2>Aplicaciones</h2>
    <p>
      Se aplican en telefonía móvil, televisión, radiodifusión, Internet, redes sociales,
      telemedicina, educación en línea y mucho más.
    </p>
    <div class="imagenes">
      <img src="https://upload.wikimedia.org/wikipedia/commons/thumb/f/fb/Cell_tower_from_below.JPG/640px-Cell_tower_from_below.JPG" alt="Torre celular">
      <img src="https://upload.wikimedia.org/wikipedia/commons/thumb/e/e9/Fiber_optic_cable_1.jpg/640px-Fiber_optic_cable_1.jpg" alt="Fibra óptica">
    </div>
  </section>

  <section id="radio" class="radio" style="display:none;">
    <h2>🎶 Radio Estrella en Vivo</h2>
    <audio controls autoplay>
      <source src="https://stream.zeno.fm/2vbhcrv82hhvv" type="audio/mpeg">
      Tu navegador no soporta audio en vivo.
    </audio>
  </section>

  <footer>
    © 2025 - Proyecto de Telecomunicaciones
  </footer>

  <script>
    function mostrar(id) {
      document.querySelectorAll("section").forEach(sec => sec.style.display = "none");
      document.getElementById(id).style.display = "block";
    }
  </script>
</body>
</html>
