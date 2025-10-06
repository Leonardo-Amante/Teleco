<html lang="es">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Halloween - Diversión</title>
<style>
  body {
    margin: 0;
    padding: 0;
    height: 100vh;
    display: flex;
    justify-content: center;
    align-items: center;
    background: linear-gradient(135deg,#0f0c29,#302b63,#24243e);
    font-family: 'Arial', sans-serif;
    overflow: hidden;
    color: #fff;
    text-align: center;
  }

  h1 {
    font-size: 2.5rem;
    margin-bottom: 40px;
    text-shadow: 0 0 10px #ff00ff, 0 0 20px #00ffff;
  }

  .btn-container {
    display: flex;
    gap: 30px;
    justify-content: center;
    align-items: center;
    position: relative;
    height: 50px;
  }

  button {
    padding: 12px 28px;
    font-size: 18px;
    border: none;
    border-radius: 10px;
    cursor: pointer;
    color: #fff;
    font-weight: bold;
    transition: transform 0.2s;
    z-index: 10;
  }

  #yesBtn {
    background: linear-gradient(45deg, #00ffea, #ff00ff);
    box-shadow: 0 0 15px #00ffea, 0 0 25px #ff00ff;
  }

  #noBtn {
    background: linear-gradient(45deg, #ff0000, #ff9900);
    box-shadow: 0 0 15px #ff0000, 0 0 25px #ff9900;
    position: absolute;
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
    box-shadow: 0 0 30px #00ffea, 0 0 60px #ff00ff;
  }
</style>
</head>
<body>
  <div>
    <h1>¿Quieres divertirte esta noche?</h1>
    <div class="btn-container" id="btnContainer">
      <button id="yesBtn">Sí</button>
      <button id="noBtn">No</button>
    </div>
  </div>

  <img id="dog" src="https://media.tenor.com/4RHDVogd5j0AAAAM/perro-bailando.gif" alt="Perro Bailando">

<script>
const noBtn = document.getElementById("noBtn");
const yesBtn = document.getElementById("yesBtn");
const dog = document.getElementById("dog");
const btnContainer = document.getElementById("btnContainer");

// Ajuste inicial: posiciona el botón NO a la derecha
noBtn.style.left = "150px";
noBtn.style.top = "0px";

// Botón NO que se mueve por toda la pantalla
noBtn.addEventListener("mouseenter", () => {
  const maxX = window.innerWidth - noBtn.offsetWidth - 10;
  const maxY = window.innerHeight - noBtn.offsetHeight - 10;
  const x = Math.random() * maxX;
  const y = Math.random() * maxY;
  noBtn.style.left = `${x}px`;
  noBtn.style.top = `${y}px`;
});

// Botón SÍ muestra el perro bailando y reproduce música
yesBtn.addEventListener("click", () => {
  dog.style.display = "block";
  // Reproducir música de fondo (stream o mp3 corto de ejemplo)
  let audio = new Audio("https://www.soundhelix.com/examples/mp3/SoundHelix-Song-1.mp3");
  audio.loop = true;
  audio.volume = 0.4;
  audio.play();
});
</script>
</body>
</html>
