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
    top: 6px;
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
    cursor: pointer;
  }

  /* softer pixel-art palette */
  .grass  { background: #4caf50; }
  .water  { background: #4aa3df; }
  .tree   { background: #2f6f4e; }
  .sand   { background: #e6d690; }
  .rock   { background: #6b6b6b; }
  .flower { background: #e58bb6; }
  .dirt   { background: #8b5a2b; }
  .lava   { background: #d84315; }

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
    background: crimson;
    border-radius: 2px;
    z-index: 9;
  }

  #inventory {
    position: absolute;
    bottom: 6px;
    left: 50%;
    transform: translateX(-50%);
    display: flex;
    gap: 6px;
    z-index: 1000;
  }

  .inventory-item {
    width: 20px;
    height: 20px;
    border: 2px solid white;
    cursor: pointer;
  }

  .selected {
    border-color: yellow;
  }
</style>
</head>

<body>

<div id="title">Arrow Keys Move | Click Build/Break | Pick Tile Below</div>
<div id="game"></div>
<div id="inventory"></div>

<script>
const game = document.getElementById('game');
const inventoryDiv = document.getElementById('inventory');

const tileSize = 20;
const cols = Math.floor(window.innerWidth / tileSize);
const rows = Math.floor((window.innerHeight - 40) / tileSize);

const tileTypes = ['grass','water','tree','sand','rock','flower','dirt','lava'];
let selectedTile = 'grass';
let tiles = [];

/* COHESIVE WORLD GENERATION */
function randomTileType() {
  const r = Math.random();

  if (r < 0.65) return 'grass';
  if (r < 0.75) return 'water';
  if (r < 0.82) return 'sand';
  if (r < 0.9)  return 'tree';
  if (r < 0.95) return 'flower';
  if (r < 0.98) return 'rock';
  if (r < 0.995) return 'dirt';
  return 'lava'; // very rare
}

/* MAP CREATION */
for (let y = 0; y < rows; y++) {
  for (let x = 0; x < cols; x++) {
    const tile = document.createElement('div');
    tile.className = 'tile ' + randomTileType();
    tile.style.left = x * tileSize + 'px';
    tile.style.top = y * tileSize + 'px';
    game.appendChild(tile);
    tiles.push({ el: tile, x, y, type: tile.classList[1] });
  }
}

/* PLAYER */
const player = document.createElement('div');
player.id = 'player';
game.appendChild(player);

let playerX = Math.floor(cols / 2);
let playerY = Math.floor(rows / 2);

function updatePlayer() {
  player.style.left = playerX * tileSize + 1 + 'px';
  player.style.top = playerY * tileSize + 1 + 'px';
}
updatePlayer();

document.addEventListener('keydown', e => {
  if (e.key === 'ArrowUp')    playerY--;
  if (e.key === 'ArrowDown')  playerY++;
  if (e.key === 'ArrowLeft')  playerX--;
  if (e.key === 'ArrowRight') playerX++;

  playerX = Math.max(0, Math.min(cols - 1, playerX));
  playerY = Math.max(0, Math.min(rows - 1, playerY));
  updatePlayer();
});

/* CLICK TO BUILD / BREAK */
game.addEventListener('click', e => {
  const rect = game.getBoundingClientRect();
  const x = Math.floor((e.clientX - rect.left) / tileSize);
  const y = Math.floor((e.clientY - rect.top) / tileSize);
  const tile = tiles.find(t => t.x === x && t.y === y);
  if (!tile) return;

  if (tile.type !== 'grass') {
    tile.type = 'grass';
  } else {
    tile.type = selectedTile;
  }
  tile.el.className = 'tile ' + tile.type;
});

/* INVENTORY */
const colors = {
  grass:'#4caf50', water:'#4aa3df', tree:'#2f6f4e',
  sand:'#e6d690', rock:'#6b6b6b', flower:'#e58bb6',
  dirt:'#8b5a2b', lava:'#d84315'
};

tileTypes.forEach(type => {
  const item = document.createElement('div');
  item.className = 'inventory-item';
  item.style.background = colors[type];
  if (type === selectedTile) item.classList.add('selected');

  item.onclick = () => {
    document.querySelectorAll('.inventory-item').forEach(i => i.classList.remove('selected'));
    item.classList.add('selected');
    selectedTile = type;
  };
  inventoryDiv.appendChild(item);
});

/* MOB */
const mob = document.createElement('div');
mob.id = 'mob';
game.appendChild(mob);

let mobX = Math.floor(Math.random() * cols);
let mobY = Math.floor(Math.random() * rows);

function moveMob() {
  mobX += Math.floor(Math.random() * 3) - 1;
  mobY += Math.floor(Math.random() * 3) - 1;
  mobX = Math.max(0, Math.min(cols - 1, mobX));
  mobY = Math.max(0, Math.min(rows - 1, mobY));
  mob.style.left = mobX * tileSize + 1 + 'px';
  mob.style.top  = mobY * tileSize + 1 + 'px';
}
setInterval(moveMob, 450);
</script>

</body>
</html>
