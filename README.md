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
    font-size: 18px;
    font-weight: bold;
    z-index: 1000;
  }

  #game {
    position: absolute;
    top: 40px;
    left: 48px;
    width: calc(100vw - 48px);
    height: calc(100vh - 40px);
    overflow: hidden;
    background: #87ceeb;
  }

  .tile {
    position: absolute;
    width: 20px;
    height: 20px;
  }

  /* WORLD COLORS */
  .grass  { background: #4caf50; }
  .water  { background: #4aa3df; }
  .tree   { background: #2f6f4e; }
  .sand   { background: #e6d690; }
  .rock   { background: #6b6b6b; }
  .flower { background: #e58bb6; }
  .dirt   { background: #8b5a2b; }
  .lava   { background: #d84315; }

  #player, #mob {
    position: absolute;
    width: 18px;
    height: 18px;
    border-radius: 2px;
    z-index: 10;
  }

  #player { background: orange; }
  #mob { background: crimson; z-index: 9; }

  /* LEFT TILE BAR */
  #inventory {
    position: absolute;
    top: 40px;
    left: 0;
    width: 48px;
    height: calc(100vh - 40px);
    background: rgba(0,0,0,0.25);
    display: flex;
    flex-direction: column;
    align-items: center;
    padding-top: 6px;
    gap: 6px;
    z-index: 1000;
  }

  .inventory-item {
    width: 28px;
    height: 28px;
    border: 2px solid white;
  }

  .selected {
    border-color: yellow;
  }

  /* TOUCH CONTROLS */
  #touch-controls {
    position: absolute;
    bottom: 12px;
    right: 12px;
    width: 120px;
    height: 120px;
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    grid-template-rows: repeat(3, 1fr);
    gap: 6px;
    z-index: 1000;
  }

  .touch-btn {
    background: rgba(255,255,255,0.25);
    border-radius: 8px;
    touch-action: none;
  }

  .touch-btn:active {
    background: rgba(255,255,255,0.45);
  }

  .up    { grid-column: 2; grid-row: 1; }
  .left  { grid-column: 1; grid-row: 2; }
  .right { grid-column: 3; grid-row: 2; }
  .down  { grid-column: 2; grid-row: 3; }
</style>
</head>

<body>

<div id="title">Move • Build • Break</div>
<div id="inventory"></div>
<div id="game"></div>

<div id="touch-controls">
  <div class="touch-btn up"></div>
  <div class="touch-btn left"></div>
  <div class="touch-btn right"></div>
  <div class="touch-btn down"></div>
</div>

<script>
const game = document.getElementById('game');
const inventoryDiv = document.getElementById('inventory');

const tileSize = 20;
const cols = Math.floor((window.innerWidth - 48) / tileSize);
const rows = Math.floor((window.innerHeight - 40) / tileSize);

const tileTypes = ['grass','water','tree','sand','rock','flower','dirt','lava'];
const colors = {
  grass:'#4caf50', water:'#4aa3df', tree:'#2f6f4e',
  sand:'#e6d690', rock:'#6b6b6b', flower:'#e58bb6',
  dirt:'#8b5a2b', lava:'#d84315'
};

let selectedTile = 'grass';
let tiles = [];

/* TERRAIN */
function randomTileType() {
  const r = Math.random();
  if (r < 0.65) return 'grass';
  if (r < 0.75) return 'water';
  if (r < 0.82) return 'sand';
  if (r < 0.9)  return 'tree';
  if (r < 0.95) return 'flower';
  if (r < 0.98) return 'rock';
  if (r < 0.995) return 'dirt';
  return 'lava';
}

for (let y = 0; y < rows; y++) {
  for (let x = 0; x < cols; x++) {
    const tile = document.createElement('div');
    tile.className = 'tile ' + randomTileType();
    tile.style.left = x * tileSize + 'px';
    tile.style.top  = y * tileSize + 'px';
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
  player.style.top  = playerY * tileSize + 1 + 'px';
}
updatePlayer();

function movePlayer(dx, dy) {
  playerX = Math.max(0, Math.min(cols - 1, playerX + dx));
  playerY = Math.max(0, Math.min(rows - 1, playerY + dy));
  updatePlayer();
}

/* KEYBOARD */
document.addEventListener('keydown', e => {
  if (e.key === 'ArrowUp') movePlayer(0,-1);
  if (e.key === 'ArrowDown') movePlayer(0,1);
  if (e.key === 'ArrowLeft') movePlayer(-1,0);
  if (e.key === 'ArrowRight') movePlayer(1,0);
});

/* TOUCH */
document.querySelector('.up').ontouchstart    = () => movePlayer(0,-1);
document.querySelector('.down').ontouchstart  = () => movePlayer(0,1);
document.querySelector('.left').ontouchstart  = () => movePlayer(-1,0);
document.querySelector('.right').ontouchstart = () => movePlayer(1,0);

/* BUILD / BREAK */
game.addEventListener('click', e => {
  const rect = game.getBoundingClientRect();
  const x = Math.floor((e.clientX - rect.left) / tileSize);
  const y = Math.floor((e.clientY - rect.top) / tileSize);
  const tile = tiles.find(t => t.x === x && t.y === y);
  if (!tile) return;

  tile.type = tile.type !== 'grass' ? 'grass' : selectedTile;
  tile.el.className = 'tile ' + tile.type;
});

/* INVENTORY */
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

setInterval(() => {
  mobX += Math.floor(Math.random()*3)-1;
  mobY += Math.floor(Math.random()*3)-1;
  mobX = Math.max(0, Math.min(cols - 1, mobX));
  mobY = Math.max(0, Math.min(rows - 1, mobY));
  mob.style.left = mobX * tileSize + 1 + 'px';
  mob.style.top  = mobY * tileSize + 1 + 'px';
}, 450);
</script>

</body>
</html>

