<html lang="es">
<head>
  <meta charset="UTF-8">
  <title>Video Fullscreen</title>
  <style>
    html, body {
      margin: 0;
      padding: 0;
      height: 100%;
      overflow: hidden;
      background: #000;
    }

    #videoContainer {
      position: fixed;
      top: 0;
      left: 0;
      width: 100%;
      height: 100%;
      z-index: 9999;
    }

    #videoContainer iframe {
      width: 100%;
      height: 100%;
      border: none;
    }

    #infoButton {
      position: absolute;
      top: 50%;
      left: 50%;
      transform: translate(-50%, -50%);
      padding: 20px 40px;
      font-size: 24px;
      background: #00ffae;
      color: #000;
      border: none;
      border-radius: 10px;
      cursor: pointer;
      z-index: 10000;
      font-weight: bold;
    }

    #infoText {
      position: absolute;
      top: 30%;
      left: 50%;
      transform: translateX(-50%);
      color: #00ffae;
      font-size: 28px;
      text-align: center;
      font-family: 'Orbitron', monospace;
      z-index: 10000;
    }
  </style>
</head>
<body>

<div id="infoText">Infórmate sobre nuestra carrera Telecomunicaciones</div>
<button id="infoButton" onclick="playVideo()">Información</button>
<div id="videoContainer"></div>

<script>
  function playVideo() {
    const videoContainer = document.getElementById('videoContainer');
    videoContainer.innerHTML = `
      <iframe 
        src="https://www.youtube.com/embed/Ae2uKbFbt1U?autoplay=1&controls=0&modestbranding=1&rel=0&fs=1&disablekb=1" 
        frameborder="0" 
        allow="autoplay; fullscreen; encrypted-media" 
        allowfullscreen>
      </iframe>
    `;

    // Oculta el botón y el texto
    document.getElementById('infoButton').style.display = 'none';
    document.getElementById('infoText').style.display = 'none';
  }
</script>

</body>
</html>
