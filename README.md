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

  #top-buttons {
    position: absolute;
    top: 6px;
    right: 6px;
    display: flex;
    gap: 6px;
    z-index: 1000;
  }

  button {
    font-family: monospace;
    padding: 4px 8px;
    border: none;
    cursor: pointer;
    background: rgba(255,255,255,0.85);
  }

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
    gap: 6px;
    padding-top: 6px;
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

  #game {
    position: absolute;
    top: 40px;
    left: 48px;
    width: calc(100vw - 48px);
    height: calc(100vh - 40px);
  }

  .tile {
    position: absolute;
    width: 20px;
    height: 20px;
  }

  .grass  { background:#4caf50 }
  .water  { background:#4aa3df }
  .tree   { background:#2f6f4e }
  .sand   { background:#e6d690 }
  .rock   { background:#6b6b6b }
  .flower { background:#e58bb6 }
  .dirt   { background:#8b5a2b }
  .lava   { background:#d84315 }

  #player, #mob {
    position: absolute;
    width: 18px;
    height: 18px;
    border-radius: 2px;
    z-index: 10;
  }

  #player { background: orange }
  #mob { background: crimson; z-index: 9 }

  #touch-controls {
    position: absolute;
    bottom: 12px;
    right: 12px;
    width: 120px;
    height: 120px;
    display: grid;
    grid-template-columns: repeat(3,1fr);
    grid-template-rows: repeat(3,1fr);
    gap: 6px;
    z-index: 1000;
  }

  .touch-btn {
    background: rgba(255,255,255,0.25);
    border-radius: 8px;
  }

  .up { grid-column:2;grid-row:1 }
  .left { grid-column:1;grid-row:2 }
  .right { grid-column:3;grid-row:2 }
  .down { grid-column:2;grid-row:3 }
</style>
</head>

<body>

<div id="title">Pixel Sandbox</div>

<div id="top-buttons">
  <button onclick="resetWorld()">Reset</button>
  <button onclick="downloadWorld()">Download</button>
  <button onclick="loadWorld()">Load</button>
</div>

<div id="inventory"></div>
<div id="game"></div>

<div id="touch-controls">
  <div class="touch-btn up"></div>
  <div class="touch-btn left"></div>
  <div class="touch-btn right"></div>
  <div class="touch-btn down"></div>
</div>

<input type="file" id="fileInput" hidden>

<script>
const game = document.getElementById('game');
const inventory = document.getElementById('inventory');
const tileSize = 20;
const cols = Math.floor((window.innerWidth - 48) / tileSize);
const rows = Math.floor((window.innerHeight - 40) / tileSize);

const tileTypes = ['grass','water','tree','sand','rock','flower','dirt','lava'];
const colors = {
  grass:'#4caf50', water:'#4aa3df', tree:'#2f6f4e',
  sand:'#e6d690', rock:'#6b6b6b', flower:'#e58bb6',
  dirt:'#8b5a2b', lava:'#d84315'
};

let tiles = [];
let selectedTile = 'grass';

/* WORLD GEN */
function randomTile() {
  const r = Math.random();
  if (r < .65) return 'grass';
  if (r < .75) return 'water';
  if (r < .82) return 'sand';
  if (r < .9) return 'tree';
  if (r < .95) return 'flower';
  if (r < .98) return 'rock';
  if (r < .995) return 'dirt';
  return 'lava';
}

function generateWorld(data=null) {
  game.innerHTML = '';
  tiles = [];

  for (let y=0;y<rows;y++){
    for (let x=0;x<cols;x++){
      const type = data ? data[y][x] : randomTile();
      const t = document.createElement('div');
      t.className = 'tile ' + type;
      t.style.left = x*tileSize+'px';
      t.style.top = y*tileSize+'px';
      game.appendChild(t);
      tiles.push({x,y,type,el:t});
    }
  }
  saveWorld();
}

function saveWorld() {
  const map = [];
  for (let y=0;y<rows;y++){
    map[y] = [];
    for (let x=0;x<cols;x++){
      map[y][x] = tiles.find(t=>t.x===x&&t.y===y).type;
    }
  }
  localStorage.setItem('sandboxWorld', JSON.stringify(map));
}

/* LOAD SAVED */
const saved = localStorage.getItem('sandboxWorld');
generateWorld(saved ? JSON.parse(saved) : null);

/* RESET */
function resetWorld(){
  localStorage.removeItem('sandboxWorld');
  generateWorld();
}

/* DOWNLOAD */
function downloadWorld(){
  const data = localStorage.getItem('sandboxWorld');
  const blob = new Blob([data],{type:'application/json'});
  const a = document.createElement('a');
  a.href = URL.createObjectURL(blob);
  a.download = 'sandbox-world.json';
  a.click();
}

/* LOAD FILE */
function loadWorld(){
  document.getElementById('fileInput').click();
}

document.getElementById('fileInput').onchange = e => {
  const reader = new FileReader();
  reader.onload = () => {
    const data = JSON.parse(reader.result);
    generateWorld(data);
  };
  reader.readAsText(e.target.files[0]);
};

/* PLAYER */
const player = document.createElement('div');
player.id = 'player';
game.appendChild(player);
let px = Math.floor(cols/2), py = Math.floor(rows/2);
function updatePlayer(){
  player.style.left = px*tileSize+1+'px';
  player.style.top = py*tileSize+1+'px';
}
updatePlayer();

function move(dx,dy){
  px = Math.max(0,Math.min(cols-1,px+dx));
  py = Math.max(0,Math.min(rows-1,py+dy));
  updatePlayer();
}

document.addEventListener('keydown',e=>{
  if(e.key==='ArrowUp')move(0,-1);
  if(e.key==='ArrowDown')move(0,1);
  if(e.key==='ArrowLeft')move(-1,0);
  if(e.key==='ArrowRight')move(1,0);
});

document.querySelector('.up').ontouchstart=()=>move(0,-1);
document.querySelector('.down').ontouchstart=()=>move(0,1);
document.querySelector('.left').ontouchstart=()=>move(-1,0);
document.querySelector('.right').ontouchstart=()=>move(1,0);

/* BUILD */
game.onclick = e => {
  const r = game.getBoundingClientRect();
  const x = Math.floor((e.clientX-r.left)/tileSize);
  const y = Math.floor((e.clientY-r.top)/tileSize);
  const t = tiles.find(t=>t.x===x&&t.y===y);
  if(!t)return;
  t.type = t.type!=='grass'?'grass':selectedTile;
  t.el.className='tile '+t.type;
  saveWorld();
};

/* INVENTORY */
tileTypes.forEach(type=>{
  const i=document.createElement('div');
  i.className='inventory-item';
  i.style.background=colors[type];
  if(type===selectedTile)i.classList.add('selected');
  i.onclick=()=>{
    document.querySelectorAll('.inventory-item').forEach(e=>e.classList.remove('selected'));
    i.classList.add('selected');
    selectedTile=type;
  };
  inventory.appendChild(i);
});
</script>

</body>
</html>
