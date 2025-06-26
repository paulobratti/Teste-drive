<!DOCTYPE html>
<html lang="pt-BR">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Mini Flappy Bird</title>
  <style>
    * { margin: 0; padding: 0; box-sizing: border-box; }
    html, body {
      width: 100%;
      height: 100%;
      overflow: hidden;
    }
    canvas {
      background: skyblue;
      display: block;
    }
  </style>
</head>
<body>
  <canvas id="gameCanvas"></canvas>

  <script>
    const canvas = document.getElementById("gameCanvas");
    const ctx = canvas.getContext("2d");

    // Redimensionar o canvas para ocupar a tela toda
    canvas.width = window.innerWidth;
    canvas.height = window.innerHeight;

    let frames = 0;
    const gravity = 0.4;
    const jump = -8;
    const pipeGap = 180;
    const pipeWidth = 60;

    const birdImg = new Image();
    birdImg.src = "https://raw.githubusercontent.com/sourabhv/FlapPyBird/master/assets/sprites/yellowbird-midflap.png"; // imagem de passaro

    const bird = {
      x: 100,
      y: 200,
      width: 40,
      height: 40,
      velocity: 0,

      draw() {
        ctx.drawImage(birdImg, this.x, this.y, this.width, this.height);
      },

      update() {
        this.velocity += gravity;
        this.y += this.velocity;
      },

      flap() {
        this.velocity = jump;
      }
    };

    const pipes = [];

    function createPipe() {
      const topHeight = Math.floor(Math.random() * (canvas.height - pipeGap - 100)) + 50;
      pipes.push({
        x: canvas.width,
        top: topHeight,
        bottom: canvas.height - topHeight - pipeGap
      });
    }

    function drawPipes() {
      ctx.fillStyle = "green";
      pipes.forEach(pipe => {
        ctx.fillRect(pipe.x, 0, pipeWidth, pipe.top);
        ctx.fillRect(pipe.x, canvas.height - pipe.bottom, pipeWidth, pipe.bottom);
      });
    }

    function updatePipes() {
      pipes.forEach(pipe => pipe.x -= 2);

      if (pipes.length && pipes[0].x + pipeWidth < 0) {
        pipes.shift();
      }

      if (frames % 100 === 0) createPipe();
    }

    function detectCollision() {
      for (let pipe of pipes) {
        if (
          bird.x < pipe.x + pipeWidth &&
          bird.x + bird.width > pipe.x &&
          (bird.y < pipe.top || bird.y + bird.height > canvas.height - pipe.bottom)
        ) {
          return true;
        }
      }
      return bird.y > canvas.height || bird.y < 0;
    }

    function gameLoop() {
      frames++;
      ctx.clearRect(0, 0, canvas.width, canvas.height);
      bird.update();
      updatePipes();
      drawPipes();
      bird.draw();

      if (detectCollision()) {
        alert("Game Over!");
        document.location.reload();
      }

      requestAnimationFrame(gameLoop);
    }

    document.addEventListener("keydown", function (e) {
      if (e.code === "Space") bird.flap();
    });

    createPipe();
    gameLoop();
  </script>
</body>
</html>
