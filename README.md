<!DOCTYPE html>
<html lang="ru">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Битва за Бургер</title>
  <style>
    body {
      margin: 0;
      overflow: hidden;
      background: url('background.jpg') no-repeat center center fixed;
      background-size: cover;
      font-family: sans-serif;
    }
    #gameCanvas {
      display: block;
      margin: 0 auto;
      background-color: rgba(255, 255, 255, 0.8);
    }
    #score {
      position: absolute;
      top: 10px;
      left: 10px;
      font-size: 24px;
      color: #333;
    }
  </style>
</head>
<body>
  <canvas id="gameCanvas" width="400" height="600"></canvas>
  <div id="score">Очки: 0</div>

  <script>
    const canvas = document.getElementById('gameCanvas');
    const ctx = canvas.getContext('2d');

    let score = 0;
    let player = { x: 200, y: 550, width: 50, height: 50 };
    let items = [];

    function drawPlayer() {
      ctx.fillStyle = 'blue';
      ctx.fillRect(player.x, player.y, player.width, player.height);
    }

    function drawItems() {
      ctx.fillStyle = 'green';
      items.forEach(item => {
        ctx.fillRect(item.x, item.y, item.size, item.size);
      });
    }

    function updateItems() {
      items.forEach(item => {
        item.y += item.speed;
        // Check for collision
        if (
          item.x < player.x + player.width &&
          item.x + item.size > player.x &&
          item.y < player.y + player.height &&
          item.y + item.size > player.y
        ) {
          score += 10;
          document.getElementById('score').innerText = 'Очки: ' + score;
          item.y = canvas.height + 1; // Move item out of canvas
        }
      });
      // Remove items that are out of canvas
      items = items.filter(item => item.y <= canvas.height);
    }

    function spawnItem() {
      const size = 30;
      const x = Math.random() * (canvas.width - size);
      const speed = 2 + Math.random() * 3;
      items.push({ x, y: 0, size, speed });
    }

    function gameLoop() {
      ctx.clearRect(0, 0, canvas.width, canvas.height);
      drawPlayer();
      drawItems();
      updateItems();
      requestAnimationFrame(gameLoop);
    }

    setInterval(spawnItem, 1000);
    gameLoop();

    // Device orientation for mobile control
    window.addEventListener('deviceorientation', function(event) {
      // gamma is the left-to-right tilt in degrees, where right is positive
      const tilt = event.gamma;
      if (tilt !== null) {
        player.x += tilt;
        if (player.x < 0) player.x = 0;
        if (player.x + player.width > canvas.width) player.x = canvas.width - player.width;
      }
    }, true);
  </script>
</body>
</html>
