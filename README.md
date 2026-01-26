<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Mini Sandbox World</title>
<style>
  body {
    margin: 0;
    background: #87ceeb;
    display: flex;
    justify-content: center;
    align-items: center;
    height: 100vh;
    font-family: sans-serif;
  }
  #game {
    position: relative;
    width: 600px;
    height: 400px;
    background: #87ceeb;
    border: 4px solid #222;
    overflow: hidden;
  }
  .tile {
    position: absolute;
    width: 40px;
    height: 40px;
    box-sizing: border-box;
    cursor: pointer;
  }
  .grass { background: #3cb043; }
  .water { background: #1e90ff; }
  .tree  { background: #2e8b57; }
  .sand  { background: #f4e27c; }

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
    z-index: 20;
  }
</style>
</head>
<body>
<div id="game">
  <div id="info">Arrow keys to move | Click tile to break/place</div>
  <div id="player"></div>
</div>

<script>
const game = document.getElementById('game');
const player = document.getElementById('player');
const tileSize = 40;
const cols = Math.floor(game.offsetWidth / tileSize);
const rows = Math.floor(game.offsetHeight / tileSize);
let tiles = [];

// random terrain generator
const types = ['grass','water','tree','sand'];

function randomTileType() {
  const r = Math.random();
  if(r < 0.5) return 'grass';
  else if(r < 0.65) return 'water';
  else if(r < 0.85) return 'tree';
  else return 'sand';
}

// create tiles
for(let y=0; y<rows; y++){
  for(let x=0; x<cols; x++){
    const tile = document.createElement('div');
    tile.className = 'tile ' + randomTileType();
    tile.style.left = x*tileSize + 'px';
    tile.style.top = y*tileSize + 'px';
    game.appendChild(tile);
    tiles.push({el: tile, x, y, type: tile.classList[1]});
  }
}

// player start position
let playerX = 7;
let playerY = 5;
player.style.left = playerX*tileSize + 5 + 'px';
player.style.top = playerY*tileSize + 5 + 'px';

const maxX = cols-1;
const maxY = rows-1;

function movePlayer(dx, dy){
  const newX = playerX + dx;
  const newY = playerY + dy;
  if(newX < 0 || newX > maxX || newY < 0 || newY > maxY) return;

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

// break/build tiles on click
game.addEventListener('click', e => {
  const rect = game.getBoundingClientRect();
  const x = Math.floor((e.clientX - rect.left) / tileSize);
  const y = Math.floor((e.clientY - rect.top) / tileSize);
  const tile = tiles.find(t => t.x === x && t.y === y);
  if(!tile) return;

  if(tile.type !== 'grass') {
    // break tile
    tile.type = 'grass';
    tile.el.className = 'tile grass';
  } else {
    // build random tile
    const newType = types[Math.floor(Math.random()*types.length)];
    tile.type = newType;
    tile.el.className = 'tile ' + newType;
  }
});
</script>
</body>
</html>
