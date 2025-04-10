<!DOCTYPE html>
<html lang="ko">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>밥밥 게임</title>
  <style>
    html, body {
      margin: 0;
      padding: 0;
      overflow: hidden;
      height: 100%;
    }
    canvas {
      display: block;
      margin: auto;
      background-color: #fff;
      width: 100vw;
      height: 100vh;
    }
    #score {
      position: absolute;
      top: 20px;
      left: 50%;
      transform: translateX(-50%);
      font-size: 24px;
      font-family: 'Arial', sans-serif;
      font-weight: bold;
      color: #333;
      z-index: 10;
    }
  </style>
</head>
<body>
<div id="score">잡은 빵: 0개</div>
<canvas id="gameCanvas"></canvas>
<script>
  const canvas = document.getElementById('gameCanvas');
  const ctx = canvas.getContext('2d');
  canvas.width = window.innerWidth;
  canvas.height = window.innerHeight;

  const scoreDisplay = document.getElementById('score');
  let score = 0;

  const bg = new Image();
  bg.src = '피크닉배경.jpg';

  const character = new Image();
  character.src = '호두캐릭터.png';

  const breadImgs = ['bread1.png', 'bread2.png', 'bread3.png'];
  const breads = [];

  const loadedBreadImgs = breadImgs.map(src => {
    const img = new Image();
    img.src = src;
    return img;
  });

  const characterPos = {
    x: canvas.width / 2 - 50,
    y: canvas.height - 150,
    width: 100,
    height: 100,
    speed: 20
  };

  let targetX = characterPos.x;

  let bgY = 0;
  const bgSpeed = 1;

  function createBread() {
    const i = Math.floor(Math.random() * loadedBreadImgs.length);
    breads.push({
      img: loadedBreadImgs[i],
      x: Math.random() * (canvas.width - 60),
      y: -60,
      width: 60,
      height: 60,
      speed: 3 + Math.random() * 3
    });
  }

  function draw() {
    // 타일 배경 반복 스크롤
    bgY += bgSpeed;
    if (bgY >= canvas.height) bgY = 0;
    ctx.drawImage(bg, 0, bgY - canvas.height, canvas.width, canvas.height);
    ctx.drawImage(bg, 0, bgY, canvas.width, canvas.height);

    // 캐릭터 위치 갱신
    characterPos.x = targetX;
    characterPos.x = Math.max(0, Math.min(characterPos.x, canvas.width - characterPos.width));
    ctx.drawImage(character, characterPos.x, characterPos.y, characterPos.width, characterPos.height);

    // 빵 처리
    breads.forEach((b, i) => {
      b.y += b.speed;
      ctx.drawImage(b.img, b.x, b.y, b.width, b.height);

      if (
        b.x < characterPos.x + characterPos.width &&
        b.x + b.width > characterPos.x &&
        b.y < characterPos.y + characterPos.height &&
        b.y + b.height > characterPos.y
      ) {
        breads.splice(i, 1);
        score++;
        scoreDisplay.textContent = `잡은 빵: ${score}개`;
      }

      if (b.y > canvas.height) breads.splice(i, 1);
    });

    requestAnimationFrame(draw);
  }

  // PC 방향키 이동
  document.addEventListener('keydown', e => {
    if (e.key === 'ArrowLeft') targetX -= characterPos.speed;
    if (e.key === 'ArrowRight') targetX += characterPos.speed;
  });

  // 터치 이동
  canvas.addEventListener('touchmove', function(e) {
    e.preventDefault();
    const touch = e.touches[0];
    const rect = canvas.getBoundingClientRect();
    targetX = touch.clientX - rect.left - characterPos.width / 2;
  }, { passive: false });

  setInterval(createBread, 1000);
  draw();
</script>
</body>
</html>
