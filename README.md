<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Mini Sandbox Adventure</title>
<style>
  body {
    margin: 0;
    overflow: hidden;
    background: #87ceeb;
    font-family: sans-serif;
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
    width: 40px;
    height: 40px;
    box-sizing: border-box;
    cursor: pointer;
  }
  .grass { background: #3cb043; }
  .water { background: #1e90ff; }
  .tree  { background: #2e8b57; }
  .sand  { background: #f4e27c; }
  .rock  { background: #555555; }

  #player {
    position: absolute;
    width: 30px;
    height: 30px;
    background: orange;
    border-radius: 6px;
    z-index: 10;
  }

  #mob {
    position: absolute;
    width: 30px;
    height: 30px;
    background: red;
    border-radius: 6px;
    z-index: 9;
  }

  #inventory {
    position: absolute;
    bottom: 5px;
    left: 50%;
    transform: translateX(-50%);
    z-index: 1000;
    display: flex;
    gap: 8px;
  }

  .inventory-item {
    width: 40px;
    height: 40px;
    border: 2px solid white;
    cursor: pointer;
  }

  .selected {
    border: 2px solid yellow;
  }
</style>
</head>
<body>

<div id="title">Arrow Keys to Move | Click to Place/Break | Pick Tile Below</div>
<div id="game"></div>
<div id="inventory"></div>

<script>
const game = document.getElementById('game');
const inventoryDiv = document.getElementById('inventory');
const tileTypes = ['grass','water','tree','sand','rock'];
let selectedTile = 'grass';
const tileSize = 40;
const cols = Math.floor(window.innerWidth / tileSize);
const rows = Math.floor((window.innerHeight - 40) / tileSize);
let tiles = [];

// create tile map
function randomTileType() {
  const r = Math.random();
  if(r < 0.5) return 'grass';
  else if(r < 0.65) return 'water';
  else if(r < 0.8) return 'tree';
  else if(r < 0.9) return 'sand';
  else return 'rock';
}

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

// create player
const player = document.createElement('div');
player.id = 'player';
game.appendChild(player);
let playerX = Math.floor(cols/2);
let playerY = Math.floor(rows/2);
player.style.left = playerX*tileSize + 5 + 'px';
player.style.top = playerY*tileSize + 5 + 'px';

// create mob
const mob = document.createElement('div');
mob.id = 'mob';
game.appendChild(mob);
let mobX = Math.floor(Math.random()*cols);
let mobY = Math.floor(Math.random()*rows);
mob.style.left = mobX*tileSize + 5 + 'px';
mob.style.top = mobY*tileSize + 5 + 'px';

// movement
function movePlayer(dx, dy){
  const newX = playerX + dx;
  const newY = playerY + dy;
  if(newX < 0 || newX >= cols || newY < 0 || newY >= rows) return;
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

// click to break/place
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

// create inventory
tileTypes.forEach(type => {
  const item = document.createElement('div');
  item.className = 'inventory-item ' + type;
  item.style.backgroundColor = type === 'grass' ? '#3cb043' :
                               type === 'water' ? '#1e90ff' :
                               type === 'tree' ? '#2e8b57' :
                               type === 'sand' ? '#f4e27c' :
                               '#555';
  if(type === selectedTile) item.classList.add('selected');
  item.addEventListener('click', ()=>{
    document.querySelectorAll('.inventory-item').forEach(i=>i.classList.remove('selected'));
    item.classList.add('selected');
    selectedTile = type;
  });
  inventoryDiv.appendChild(item);
});

// simple mob movement
function moveMob() {
  const dx = Math.floor(Math.random()*3)-1; // -1,0,1
  const dy = Math.floor(Math.random()*3)-1;
  const newX = Math.max(0, Math.min(cols-1, mobX + dx));
  const newY = Math.max(0, Math.min(rows-1, mobY + dy));
  mobX = newX;
  mobY = newY;
  mob.style.left = mobX*tileSize + 5 + 'px';
  mob.style.top = mobY*tileSize + 5 + 'px';
}

setInterval(moveMob, 500);

</script>
</body>
</html>
