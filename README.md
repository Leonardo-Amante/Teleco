<html lang="es">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Halloween - Pregunta</title>
  <style>
    body {
      margin: 0;
      padding: 0;
      height: 100vh;
      display: flex;
      justify-content: center;
      align-items: center;
      background: black;
      color: #fff;
      font-family: 'Arial', sans-serif;
      text-align: center;
      overflow: hidden;
    }

    h1 {
      font-size: 2.5rem;
      color: #ff0000;
      text-shadow: 0 0 15px #ff0000;
      margin-bottom: 40px;
    }

    .btn-container {
      display: flex;
      gap: 20px;
      position: relative;
    }

    button {
      padding: 12px 25px;
      font-size: 18px;
      border: none;
      border-radius: 8px;
      cursor: pointer;
      transition: transform 0.2s;
    }

    #yesBtn {
      background: #4caf50;
      color: white;
      box-shadow: 0 0 10px #4caf50;
    }

    #noBtn {
      background: #f44336;
      color: white;
      position: absolute;
      box-shadow: 0 0 10px #f44336;
    }

    #dog {
      display: none;
      position: fixed;
      top: 50%;
      left: 50%;
      transform: translate(-50%, -50%);
      z-index: 1000;
      max-width: 400px;
      border-radius: 15px;
      box-shadow: 0 0 30px #00ffea;
    }
  </style>
</head>
<body>
  <div>
    <h1>¿Quieres divertirte esta noche de Halloween?</h1>
    <div class="btn-container">
      <button id="yesBtn">Sí</button>
      <button id="noBtn">No</button>
    </div>
  </div>
  <img id="dog" src="https://media.tenor.com/4RHDVogd5j0AAAAM/perro-bailando.gif" alt="Perro Bailando">

  <script>
    const noBtn = document.getElementById("noBtn");
    const yesBtn = document.getElementById("yesBtn");
    const dog = document.getElementById("dog");

    // Botón NO que se mueve dentro de la pantalla
    noBtn.addEventListener("mouseenter", () => {
      const maxX = window.innerWidth - noBtn.offsetWidth - 20;
      const maxY = window.innerHeight - noBtn.offsetHeight - 20;
      const x = Math.random() * maxX;
      const y = Math.random() * maxY;

      noBtn.style.left = `${x}px`;
      noBtn.style.top = `${y}px`;
    });

    // Botón SÍ que muestra el perro bailando
    yesBtn.addEventListener("click", () => {
      dog.style.display = "block";
      let audio = new Audio("https://www.myinstants.com/media/sounds/dame-tu-cosita.mp3");
      audio.play();
    });
  </script>
</body>
</html>
