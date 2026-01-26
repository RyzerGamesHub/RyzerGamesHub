<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Pixel Sandbox Game Game</title>
<style>
  body {
    margin: 0;
    overflow: hidden;
    background: #87ceeb;
    font-family: monospace;
  }
  #title {
    position: absolute;
    top: 5px;
    left: 50%;
    transform: translateX(-50%);
    color: white;
    font-weight: bold;
    font-size: 20px;
    z-index: 1000;
  }
  #game {
    position: absolute;
    top: 40px; 
    left: 0;
    width: 100vw;
    height: calc(100vh - 40px);
    background: #87ceeb;
    overflow: hidden;
  }
  .tile {
    position: absolute;
    width: 20px;
    height: 20px;
    box-sizing: border-box;
    cursor: pointer;
  }

  /* Pixel-art style colors */
  .grass { background: #3cb043; }
  .water { background: #1e90ff; }
  .tree  { background: #2e8b57; }
  .sand  { background: #f4e27c; }
  .rock  { background: #555555; }
  .flower { background: #ff69b4; }
  .dirt { background: #8b4513; }
  .lava { background: #ff4500; }

  #player {
    position: absolute;
    width: 18px;
    height: 18px;
    background: orange;
    border-radius: 2px;
    z-index: 10;
  }

  #mob {
    position: absolute;
    width: 18px;
    height: 18px;
    background: red;
    border-radius: 2px;
    z-index: 9;
  }

  #inventory {
    position: absolute;
    bottom: 5px;
    left: 50%;
    transform: translateX(-50%);
    z-index: 1000;
    display: flex;
    gap: 4px;
  }

  .inventory-item {
    width: 20px;
    height: 20px;
    border: 2px solid white;
    cursor: pointer;
  }

  .selected {
    border: 2px solid yellow;
  }
</style>
</head>
<body>

<div id="title">Arrow Keys Move | Click to Build/Break | Pick Tile Below</div>
<div id="game"></div>
<div id="inventory"></div>

<script>
const game = document.getElementById('game');
const inventoryDiv = document.getElementById('inventory');
const tileTypes = ['grass','water','tree','sand','rock','flower','dirt','lava'];
let selectedTile = 'grass';
const tileSize = 20;
const cols = Math.floor(window.innerWidth / tileSize);
const rows = Math.floor((window.innerHeight - 40) / tileSize);
let tiles = [];

// generate random terrain
function randomTileType() {
  const r = Math.random();
  if(r < 0.4) return 'grass';
  if(r < 0.55) return 'water';
  if(r < 0.65) return 'tree';
  if(r < 0.75) return 'sand';
  if(r < 0.8) return 'rock';
  if(r < 0.88) return 'flower';
  if(r < 0.95) return 'dirt';
  return 'lava';
}

// create map
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

// player
const player = document.createElement('div');
player.id = 'player';
game.appendChild(player);
let playerX = Math.floor(cols/2);
let playerY = Math.floor(rows/2);
player.style.left = playerX*tileSize + 1 + 'px';
player.style.top = playerY*tileSize + 1 + 'px';

// mob
const mob = document.createElement('div');
mob.id = 'mob';
game.appendChild(mob);
let mobX = Math.floor(Math.random()*cols);
let mobY = Math.floor(Math.random()*rows);
mob.style.left = mobX*tileSize + 1 + 'px';
mob.style.top = mobY*tileSize + 1 + 'px';

// move player
function movePlayer(dx, dy){
  const newX = playerX + dx;
  const newY = playerY + dy;
  if(newX < 0 || newX >= cols || newY < 0 || newY >= rows) return;
  playerX = newX;
  playerY = newY;
  player.style.left = playerX*tileSize + 1 + 'px';
  player.style.top = playerY*tileSize + 1 + 'px';
}

document.addEventListener('keydown', e => {
  if(e.key === 'ArrowUp') movePlayer(0,-1);
  if(e.key === 'ArrowDown') movePlayer(0,1);
  if(e.key === 'ArrowLeft') movePlayer(-1,0);
  if(e.key === 'ArrowRight') movePlayer(1,0);
});

// click to build/break
game.addEventListener('click', e => {
  const rect = game.getBoundingClientRect();
  const x = Math.floor((e.clientX - rect.left) / tileSize);
  const y = Math.floor((e.clientY - rect.top) / tileSize);
  const tile = tiles.find(t => t.x === x && t.y === y);
  if(!tile) return;

  if(tile.type !== 'grass'){
    tile.type = 'grass';
    tile.el.className = 'tile grass';
  } else {
    tile.type = selectedTile;
    tile.el.className = 'tile ' + selectedTile;
  }
});

// inventory
tileTypes.forEach(type => {
  const item = document.createElement('div');
  item.className = 'inventory-item ' + type;
  const colors = {
    'grass':'#3cb043',
    'water':'#1e90ff',
    'tree':'#2e8b57',
    'sand':'#f4e27c',
    'rock':'#555555',
    'flower':'#ff69b4',
    'dirt':'#8b4513',
    'lava':'#ff4500'
  };
  item.style.backgroundColor = colors[type];
  if(type === selectedTile) item.classList.add('selected');
  item.addEventListener('click', ()=>{
    document.querySelectorAll('.inventory-item').forEach(i=>i.classList.remove('selected'));
    item.classList.add('selected');
    selectedTile = type;
  });
  inventoryDiv.appendChild(item);
});

// mob movement
function moveMob() {
  const dx = Math.floor(Math.random()*3)-1;
  const dy = Math.floor(Math.random()*3)-1;
  const newX = Math.max(0, Math.min(cols-1, mobX + dx));
  const newY = Math.max(0, Math.min(rows-1, mobY + dy));
  mobX = newX;
  mobY = newY;
  mob.style.left = mobX*tileSize + 1 + 'px';
  mob.style.top = mobY*tileSize + 1 + 'px';
}
setInterval(moveMob, 400);
</script>

</body>
</html>

