<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1.0"/>
<title>Pixel Sandbox Deluxe</title>
<style>
body {
  margin:0; overflow:hidden; background:#000; font-family:monospace;
}
#title {
  position:absolute; top:6px; left:50%; transform:translateX(-50%);
  color:white; font-weight:bold; z-index:1000;
}
#top-buttons {
  position:absolute; top:6px; right:6px; display:flex; gap:6px; z-index:1000;
}
button {
  font-family:monospace; padding:4px 8px; border:none; background:rgba(255,255,255,.9); cursor:pointer;
}
#inventory {
  position:absolute; top:40px; left:0; width:60px; height:calc(100vh - 40px);
  background:rgba(0,0,0,.25); display:flex; flex-direction:column; align-items:center; gap:6px; padding-top:6px; z-index:1000; overflow-y:auto;
}
#inventory::-webkit-scrollbar { width:6px; }
#inventory::-webkit-scrollbar-thumb { background:rgba(255,255,255,.5); border-radius:3px; }
.inventory-item {
  width:40px; height:40px; border:2px solid white; cursor:pointer; flex-shrink:0;
}
.selected { border-color:yellow; }
#game {
  position:absolute; top:40px; left:60px; width:calc(100vw - 60px); height:calc(100vh - 40px);
  overflow:hidden; background:#fff;
}
.tile {
  position:absolute; width:20px; height:20px;
}
#player {
  position:absolute; width:18px; height:18px; border-radius:2px; background:#ffa500; z-index:10;
}
.mob {
  position:absolute; width:18px; height:18px; border-radius:50%; background:#fff; display:flex; justify-content:center; align-items:center; font-size:10px; z-index:9;
}
#touch-controls {
  position:absolute; bottom:12px; right:12px; width:120px; height:120px; display:grid; grid-template-columns:repeat(3,1fr); grid-template-rows:repeat(3,1fr); gap:6px; z-index:1000;
}
.touch-btn { background:rgba(255,255,255,.25); border-radius:8px; touch-action:none; }
.up { grid-column:2; grid-row:1; }
.left { grid-column:1; grid-row:2; }
.right { grid-column:3; grid-row:2; }
.down { grid-column:2; grid-row:3; }
@media(max-width:768px){
  #top-buttons { flex-direction:column; top:auto; bottom:12px; right:12px; }
}
</style>
</head>
<body>
<div id="title">Pixel Sandbox Deluxe</div>
<div id="top-buttons">
  <button onclick="resetWorld()">Reset</button>
  <button onclick="clearDoodle()">Clear Page</button>
  <button onclick="downloadWorld()">Download</button>
  <button onclick="loadWorld()">Load</button>
  <button onclick="togglePlayer()"><span id="playerToggleLabel">Disable Player</span></button>
  <button onclick="toggleMobs()"><span id="mobToggleLabel">Disable Mobs</span></button>
</div>
<div id="inventory"></div>
<div id="game"></div>
<div id="touch-controls">
  <div class="touch-btn up"></div>
  <div class="touch-btn left"></div>
  <div class="touch-btn right"></div>
  <div class="touch-btn down"></div>
</div>
<input type="file" id="fileInput" hidden />

<script>
const game = document.getElementById('game');
const inventory = document.getElementById('inventory');

const tileSize = 20;
const cols = 200;
const rows = 150;

let world = [];
let selectedTile = 'grass';
let playerEnabled = true;
let mobsEnabled = true;

const player = document.createElement('div');
player.id='player';
game.appendChild(player);

const mobs = [];
for(let i=0;i<5;i++){
  const m=document.createElement('div');
  m.className='mob';
  m.innerText='🐰';
  game.appendChild(m);
  mobs.push({el:m, x:rand(cols), y:rand(rows), dx:0, dy:0});
}

let px=Math.floor(cols/2), py=Math.floor(rows/2);
let camX=0, camY=0;

/* HELPERS */
function inBounds(x,y){ return x>=0 && y>=0 && x<cols && y<rows }
function rand(max,min=0){ return Math.floor(Math.random()*(max-min))+min; }

/* WORLD GENERATION WITH BIOMES AND FEATURES */
function blob(cx,cy,r,type,allowed=['grass']){
  for(let y=-r;y<=r;y++){
    for(let x=-r;x<=r;x++){
      if(Math.hypot(x,y)<=r && inBounds(cx+x,cy+y)){
        if(allowed.includes(world[cy+y][cx+x])) world[cy+y][cx+x]=type;
      }
    }
  }
}
function generateWorld(){
  world = Array.from({length:rows}, ()=> Array(cols).fill('grass'));

  // Water blobs
  for(let i=0;i<4;i++) blob(rand(cols), rand(rows), rand(10,25), 'water');

  // Surround water with sand
  for(let y=0;y<rows;y++){
    for(let x=0;x<cols;x++){
      if(world[y][x]==='water'){
        for(let dy=-1;dy<=1;dy++){
          for(let dx=-1;dx<=1;dx++){
            if(inBounds(x+dx,y+dy) && world[y+dy][x+dx]==='grass') world[y+dy][x+dx]='sand';
          }
        }
      }
    }
  }

  // Desert (sand + cacti)
  if(Math.random()<0.7){
    const cX=rand(cols), cY=rand(rows);
    blob(cX,cY,rand(12,20),'sand',['grass']);
    blob(cX,cY,rand(5,10),'cactus',['sand']);
  }

  // Forest (trees)
  for(let i=0;i<5;i++) blob(rand(cols), rand(rows), rand(8,15), 'tree',['grass']);

  // Caves
  if(Math.random()<0.25){
    const cX=rand(cols), cY=rand(rows);
    blob(cX,cY,rand(6,10),'cave',['grass','dirt']);
  }

  // Tall grass
  for(let y=0;y<rows;y++){
    for(let x=0;x<cols;x++){
      if(world[y][x]==='grass' && Math.random()<0.15) world[y][x]='tallgrass';
    }
  }
  drawWorld();
  saveWorld();
}

/* DRAW: create all tiles at once, position will be updated in gameLoop() */
function drawWorld(){
  game.innerHTML=''; // clear previous
  for(let y=0;y<rows;y++){
    for(let x=0;x<cols;x++){
      const t=document.createElement('div');
      t.className='tile';
      t.dataset.x=x;
      t.dataset.y=y;
      game.appendChild(t);
    }
  }
  // Draw entities
  if(playerEnabled) game.appendChild(player);
  if(mobsEnabled) mobs.forEach(m=>game.appendChild(m.el));
  updatePlayer();
  updateMobs();
}

/* UPDATE: position of player & mobs */
function updatePlayer() {
  if(!playerEnabled) return;
  player.style.left=(px*tileSize-camX)+'px';
  player.style.top=(py*tileSize-camY)+'px';
}
function updateMobs() {
  if(!mobsEnabled) return;
  for(let m of mobs){
    m.el.style.left=(m.x*tileSize - camX)+'px';
    m.el.style.top=(m.y*tileSize - camY)+'px';
  }
}

/* MOVE player or camera */
function move(dx,dy){
  if(playerEnabled){
    px=Math.max(0, Math.min(cols-1, px+dx));
    py=Math.max(0, Math.min(rows-1, py+dy));
  } else {
    camX=Math.max(0, Math.min(cols*tileSize - game.clientWidth, camX + dx*tileSize));
    camY=Math.max(0, Math.min(rows*tileSize - game.clientHeight, camY + dy*tileSize));
  }
}
 
/* GAME LOOP: update camera to follow player, then update tile positions for smooth movement */
function gameLoop() {
  if(playerEnabled){
    const vw=game.clientWidth, vh=game.clientHeight;
    camX = Math.min(Math.max(px*tileSize - vw/2, 0), cols*tileSize - vw);
    camY = Math.min(Math.max(py*tileSize - vh/2, 0), rows*tileSize - vh);
  }

  // Update tile positions based on camera
  document.querySelectorAll('.tile').forEach(tile => {
    const x=parseInt(tile.dataset.x);
    const y=parseInt(tile.dataset.y);
    tile.style.left=(x*tileSize - camX)+'px';
    tile.style.top=(y*tileSize - camY)+'px';

    // set background based on world data
    const val=world[y][x];
    if(val==='grass') tile.style.background='#4caf50';
    else if(val==='tallgrass') tile.style.background='#3e8f44';
    else if(val==='water') tile.style.background='#4aa3df';
    else if(val==='sand') tile.style.background='#e6d690';
    else if(val==='tree') tile.style.background='#2f6f4e';
    else if(val==='rock') tile.style.background='#6b6b6b';
    else if(val==='cactus') tile.style.background='#6fbf73';
    else if(val==='cave') tile.style.background='#2b2b2b';
    else if(val==='rainbow') tile.style.background='linear-gradient(45deg,#f00,#ff0,#0f0,#0ff,#00f,#f0f)';
    else if(val==='invisible') tile.style.background='transparent';
    else tile.style.background=val;
  });

  updatePlayer();
  updateMobs();
  requestAnimationFrame(gameLoop);
}

/* Touch controls & keyboard */
document.addEventListener('keydown', e => {
  if(e.key==='ArrowUp') move(0,-1);
  if(e.key==='ArrowDown') move(0,1);
  if(e.key==='ArrowLeft') move(-1,0);
  if(e.key==='ArrowRight') move(1,0);
});
document.querySelector('.up').ontouchstart=()=>move(0,-1);
document.querySelector('.down').ontouchstart=()=>move(0,1);
document.querySelector('.left').ontouchstart=()=>move(-1,0);
document.querySelector('.right').ontouchstart=()=>move(1,0);

/* Painting tiles on click */
game.onclick=e => {
  const rect=game.getBoundingClientRect();
  const x=Math.floor((e.clientX-rect.left+camX)/tileSize);
  const y=Math.floor((e.clientY-rect.top+camY)/tileSize);
  if(!inBounds(x,y)) return;
  world[y][x]=selectedTile==='rainbow'?'rainbow':
               selectedTile==='invisible'?'invisible':
               selectedTile;
  // no full redraw, just update tile style
  const t=document.querySelector(`.tile[data-x="${x}"][data-y="${y}"]`);
  if(t){
    if(selectedTile==='rainbow'){
      t.style.background='linear-gradient(45deg,#f00,#ff0,#0f0,#0ff,#00f,#f0f)';
    } else if(selectedTile==='invisible'){
      t.style.background='transparent';
    } else {
      // set color based on tile type
      if(selectedTile==='grass') t.style.background='#4caf50';
      else if(selectedTile==='tallgrass') t.style.background='#3e8f44';
      else if(selectedTile==='water') t.style.background='#4aa3df';
      else if(selectedTile==='sand') t.style.background='#e6d690';
      else if(selectedTile==='tree') t.style.background='#2f6f4e';
      else if(selectedTile==='cactus') t.style.background='#6fbf73';
      else if(selectedTile==='cave') t.style.background='#2b2b2b';
      else if(selectedTile==='rock') t.style.background='#6b6b6b';
    }
  }
  saveWorld();
};

/* Save, Load, Reset, Clear */
function saveWorld(){ localStorage.setItem('sandboxWorld',JSON.stringify(world)); }
function resetWorld(){ localStorage.removeItem('sandboxWorld'); generateWorld(); }
function downloadWorld(){
  const blob=new Blob([JSON.stringify(world)], {type:'application/json'});
  const a=document.createElement('a');
  a.href=URL.createObjectURL(blob);
  a.download='world.json';
  a.click();
}
function loadWorld(){ document.getElementById('fileInput').click(); }
document.getElementById('fileInput').onchange=e => {
  const r=new FileReader();
  r.onload=()=>{ world=JSON.parse(r.result); drawWorld(); };
  r.readAsText(e.target.files[0]);
}
function clearDoodle(){ world=Array.from({length:rows}, ()=>Array(cols).fill('#ffffff')); drawWorld(); }

/* INVENTORY: add color tiles, including hex support for custom colors */
const colors=[
  '#4caf50','#3e8f44','#4aa3df','#e6d690','#2f6f4e','#6b6b6b','#6fbf73','#2b2b2b',
  'linear-gradient(45deg,#f00,#ff0,#0f0,#0ff,#00f,#f0f)','transparent',

  // Reds & Pinks
  '#FF0000','#FF7F7F','#FFAAAA','#FFC0CB','#FF69B4','#FF1493','#DB7093','#C71585','#FFB6C1','#FFA07A',

  // Reds & Browns
  '#8B0000','#A0522D','#D2691E','#B22222','#8B4513',

  // Yellows & Oranges
  '#FFFF00','#FFFF7F','#FFFFAA','#FFD700','#FFA500','#FF8C00','#FF6347','#FF4500','#F4A460','#DAA520',

  // Greens
  '#008000','#7CFC00','#32CD32','#006400','#228B22','#ADFF2F','#9ACD32','#556B2F',

  // Blues & Cyan
  '#0000FF','#7B68EE','#1E90FF','#00BFFF','#4682B4','#5F9EA0','#00CED1','#20B2AA',
  '#40E0D0','#ADD8E6','#B0E0E6','#87CEFA','#00FA9A','#7FFFD4','#66CDAA','#008080',

  // Purples & Violets
  '#4B0082','#8A2BE2','#9400D3','#8B008B','#800080','#BA55D3','#D8BFD8',
  '#DDA0DD','#EE82EE','#DA70D6',

  // Greys / Black / White
  '#191970','#000080','#00008B','#0000CD','#4169E1',
  '#708090','#2F4F4F','#A9A9A9','#C0C0C0','#D3D3D3','#FFFFFF','#000000',

  // Earth tones
  '#CD853F','#DEB887','#D2B48C','#BC8F8F',

  // Pastels / extra
  '#FFFFE0','#F0E68C','#E6E6FA','#9370DB','#808080','#696969'
 ];
colors.forEach(c => {
  const item=document.createElement('div');
  item.className='inventory-item';
  if(c.startsWith('linear-gradient')){
    item.style.background=c;
  } else if(c==='transparent'){
    item.style.background='transparent';
    item.style.border='2px dashed #fff';
  } else {
    item.style.background=c;
  }
  if(c===selectedTile) item.classList.add('selected');
  item.onclick=()=>{
    document.querySelectorAll('.inventory-item').forEach(e=>e.classList.remove('selected'));
    item.classList.add('selected');
    selectedTile=c;
  };
  inventory.appendChild(item);
});

/* Toggle player/mobs */
function togglePlayer() {
  playerEnabled=!playerEnabled;
  document.getElementById('playerToggleLabel').innerText=playerEnabled?'Disable Player':'Enable Player';
}
function toggleMobs() {
  mobsEnabled=!mobsEnabled;
  document.getElementById('mobToggleLabel').innerText=mobsEnabled?'Disable Mobs':'Enable Mobs';
}

/* Initialize: load or generate world */
const saved=localStorage.getItem('sandboxWorld');
if(saved){ world=JSON.parse(saved); } else { generateWorld(); }
drawWorld();

/* Start game loop for smooth camera follow and tile update */
gameLoop();
</script>
</body>
</html>
