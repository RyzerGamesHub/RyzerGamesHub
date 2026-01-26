<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Mini Open World</title>
<style>
  body {
    margin: 0;
    background: #333;
    display: flex;
    justify-content: center;
    align-items: center;
    height: 100vh;
    overflow: hidden;
    font-family: sans-serif;
  }

  #game {
    position: relative;
    width: 600px;
    height: 400px;
    background: #87ceeb; /* sky color */
    overflow: hidden;
    border: 4px solid #222;
  }

  .tile {
    position: absolute;
    width: 40px;
    height: 40px;
    box-sizing: border-box;
  }

  .grass {
    background: #3cb043;
  }
  .water {
    background: #1e90ff;
  }
  .tree {
    background: #2e8b57;
  }

  #player {
    position: absolute;
    width: 30px;
    height: 30px;
    background: orange;
    border-radius: 6px;
    top: 200px;
    left: 300px;
    z-index: 10;
  }

  #info {
    position: absolute;
    top: 10px;
    left: 10px;
    color: white;
    font-weight: bold;
  }
</style>
</head>
<body>
<div id="game">
  <div id="info">Use Arrow Keys to Move</div>
  <div id="player"></div>
</div>

<script>
const game = document.getElementById('game');
const player = document.getElementById('player');
const tileSize = 40;
const cols = Math.floor(game.offsetWidth / tileSize);
const rows = Math.floor(game.offsetHeight / tileSize);
let tiles = [];

// randomly generate tiles
for(let y=0; y<rows; y++){
  for(let x=0; x<cols; x++){
    const tile = document.createElement('div');
    tile.className = 'tile';
    tile.style.left = x*tileSize + 'px';
    tile.style.top = y*tileSize + 'px';
    // random terrain
    const rand = Math.random();
    if(rand < 0.1) tile.classList.add('water');
    else if(rand < 0.2) tile.classList.add('tree');
    else tile.classList.add('grass');
    game.appendChild(tile);
    tiles.push({el: tile, x, y, type: tile.classList[1]});
  }
}

let playerX = 7; // starting tile
let playerY = 5;
const maxX = cols-1;
const maxY = rows-1;

function movePlayer(dx, dy){
  const newX = playerX + dx;
  const newY = playerY + dy;
  if(newX < 0 || newX > maxX || newY < 0 || newY > maxY) return;

  // block movement on water
  const tile = tiles.find(t => t.x===newX && t.y===newY);
  if(tile.type === 'water') return;

  playerX = newX;
  playerY = newY;
  player.style.left = playerX*tileSize + 5 + 'px';
  player.style.top = playerY*tileSize + 5 + 'px';
}

document.addEventListener('keydown', e => {
  if(e.key === 'ArrowUp') movePlayer(0,-1);
  if(e.key === 'ArrowDown') movePlayer(0,1);
  if(e.key === 'ArrowLeft') movePlayer(-1,0);
  if(e.key === 'ArrowRight') movePlayer(1,0);
});
</script>
</body>
</html>
