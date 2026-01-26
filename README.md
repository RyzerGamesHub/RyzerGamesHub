<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Catch the Stars!</title>
<style>
  body {
    margin: 0;
    background: linear-gradient(to top, #1e3c72, #2a5298);
    display: flex;
    justify-content: center;
    align-items: center;
    height: 100vh;
    font-family: 'Arial', sans-serif;
    overflow: hidden;
    color: white;
  }
  #game {
    position: relative;
    width: 400px;
    height: 600px;
    background: rgba(0,0,0,0.3);
    border-radius: 12px;
    overflow: hidden;
    box-shadow: 0 0 20px rgba(0,0,0,0.5);
  }
  .basket {
    position: absolute;
    bottom: 20px;
    width: 80px;
    height: 20px;
    background: orange;
    border-radius: 10px;
  }
  .star {
    position: absolute;
    width: 20px;
    height: 20px;
    background: yellow;
    border-radius: 50%;
  }
  #score {
    position: absolute;
    top: 10px;
    left: 10px;
    font-size: 18px;
  }
</style>
</head>
<body>

<div id="game">
  <div id="score">Score: 0</div>
  <div class="basket" id="basket"></div>
</div>

<script>
const game = document.getElementById('game');
const basket = document.getElementById('basket');
const scoreDisplay = document.getElementById('score');
let score = 0;
let basketX = 160; // starting position
const basketSpeed = 20;
basket.style.left = basketX + 'px';

document.addEventListener('keydown', (e) => {
  if(e.key === 'ArrowLeft') basketX = Math.max(0, basketX - basketSpeed);
  if(e.key === 'ArrowRight') basketX = Math.min(game.offsetWidth - 80, basketX + basketSpeed);
  basket.style.left = basketX + 'px';
});

function createStar() {
  const star = document.createElement('div');
  star.className = 'star';
  const x = Math.random() * (game.offsetWidth - 20);
  star.style.left = x + 'px';
  star.style.top = '0px';
  game.appendChild(star);

  let fallSpeed = 2 + Math.random() * 3;

  const fallInterval = setInterval(() => {
    let top = parseFloat(star.style.top);
    if(top + 20 >= game.offsetHeight - 20 && top + 20 <= game.offsetHeight){
      // check collision with basket
      const basketLeft = basketX;
      const basketRight = basketX + 80;
      const starLeft = parseFloat(star.style.left);
      const starRight = starLeft + 20;
      if(starRight > basketLeft && starLeft < basketRight){
        score++;
        scoreDisplay.innerText = 'Score: ' + score;
        star.remove();
        clearInterval(fallInterval);
        return;
      }
    }
    if(top > game.offsetHeight){
      star.remove();
      clearInterval(fallInterval);
      return;
    }
    star.style.top = top + fallSpeed + 'px';
  }, 20);
}

// spawn stars randomly
setInterval(createStar, 1000);
</script>

</body>
</html>

