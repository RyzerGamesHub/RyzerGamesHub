<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Pixel Sandbox Deluxe</title>
<style>
body {
  margin:0;
  overflow:hidden;
  background:#000;
  font-family:monospace;
}

#title {
  position:absolute;
  top:6px;
  left:50%;
  transform:translateX(-50%);
  color:white;
  font-weight:bold;
  z-index:1000;
}

#top-buttons {
  position:absolute;
  top:6px;
  right:6px;
  display:flex;
  gap:6px;
  z-index:1000;
  flex-wrap:wrap;
}

button {
  font-family:monospace;
  padding:4px 8px;
  border:none;
  background:rgba(255,255,255,.9);
  cursor:pointer;
}

#inventory {
  position:absolute;
  top:40px;
  left:0;
  width:60px;
  height:calc(100vh - 40px);
  background:rgba(0,0,0,.25);
  display:flex;
  flex-direction:column;
  align-items:center;
  gap:6px;
  padding-top:6px;
  z-index:1000;
  overflow-y:auto;
  scrollbar-width:thin;
  scrollbar-color: rgba(255,255,255,.5) transparent;
}
#inventory::-webkit-scrollbar { width:6px; }
#inventory::-webkit-scrollbar-thumb { background:rgba(255,255,255,.5); border-radius:3px; }

.inventory-item {
  width:40px;
  height:40px;
  border:2px solid white;
  cursor:pointer;
  flex-shrink:0;
}
.selected { border-color:yellow }

#game {
  position:absolute;
  top:40px;
  left:60px;
  width:calc(100vw - 60px);
  height:calc(100vh - 40px);
  overflow:hidden;
  touch-action: none;
}

.tile {
  position:absolute;
  width:20px;
  height:20px;
}

#player {
  position:absolute;
  width:18px;
  height:18px;
  border-radius:2px;
  background:orange;
  z-index:10;
}

.mob {
  position:absolute;
  width:18px;
  height:18px;
  border-radius:50%;
  z-index:9;
  display:flex;
  justify-content:center;
  align-items:center;
  font-size:10px;
}

#touch-controls {
  position:absolute;
  bottom:12px;
  right:12px;
  width:120px;
  height:120px;
  display:grid;
  grid-template-columns:repeat(3,1fr);
  grid-template-rows:repeat(3,1fr);
  gap:6px;
  z-index:1000;
}
.touch-btn { background:rgba(255,255,255,.25); border-radius:8px; touch-action:none; }
.up{grid-column:2;grid-row:1}
.left{grid-column:1;grid-row:2}
.right{grid-column:3;grid-row:2}
.down{grid-column:2;grid-row:3}

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
  <button id="togglePlayerBtn">Disable Player</button>
  <button id="toggleMobsBtn">Disable Mobs</button>
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
const cols = 200;
const rows = 150;

let world = [];
let selectedTile = 'grass';

/* ENTITIES */
let playerEnabled = true;
const player = document.createElement('div');
player.id='player';
game.appendChild(player);

let mobsEnabled = true;
const mobs = [];
for(let i=0;i<5;i++){
  const m=document.createElement('div');
  m.className='mob';
  m.innerText='🐰';
  game.appendChild(m);
  mobs.push({el:m, x:rand(cols), y:rand(rows), dx:0, dy:0});
}

let px=Math.floor(cols/2), py=Math.floor(rows/2);
let camX=0, camY=0; // camera for free zoom

/* HELPERS */
function inBounds(x,y){ return x>=0 && y>=0 && x<cols && y<rows }
function rand(max,min=0){ return Math.floor(Math.random()*(max-min))+min }

/* WORLD GEN */
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
  world = Array.from({length:rows},()=>Array(cols).fill('grass'));
  for(let i=0;i<4;i++) blob(rand(cols),rand(rows),rand(10,25),'water');
  for(let y=0;y<rows;y++){ for(let x=0;x<cols;x++){
    if(world[y][x]==='water'){
      for(let dy=-1;dy<=1;dy++){ for(let dx=-1;dx<=1;dx++){
        if(inBounds(x+dx,y+dy)&&world[y+dy][x+dx]==='grass') world[y+dy][x+dx]='sand';
      }}
    }
  }}
  drawWorld();
  saveWorld();
}

/* DRAW WORLD */
function drawWorld(){
  game.innerHTML='';

  const viewportWidth = game.clientWidth;
  const viewportHeight = game.clientHeight;

  if(playerEnabled){
    camX = Math.min(Math.max(px*tileSize - viewportWidth/2,0), cols*tileSize-viewportWidth);
    camY = Math.min(Math.max(py*tileSize - viewportHeight/2,0), rows*tileSize-viewportHeight);
  }

  for(let y=0;y<rows;y++){
    for(let x=0;x<cols;x++){
      const t=document.createElement('div');
      t.className='tile';
      t.style.left=(x*tileSize-camX)+'px';
      t.style.top=(y*tileSize-camY)+'px';
      const val = world[y][x];
      if(val==='grass') t.style.background='#4caf50';
      else if(val==='tallgrass') t.style.background='#3e8f44';
      else if(val==='water') t.style.background='#4aa3df';
      else if(val==='sand') t.style.background='#e6d690';
      else if(val==='tree') t.style.background='#2f6f4e';
      else if(val==='rock') t.style.background='#6b6b6b';
      else if(val==='cactus') t.style.background='#6fbf73';
      else if(val==='cave') t.style.background='#2b2b2b';
      else if(val==='rainbow') t.style.background='linear-gradient(45deg,#f00,#ff0,#0f0,#0ff,#00f,#f0f)';
      else if(val==='invisible') t.style.background='transparent';
      else t.style.background=val;
      game.appendChild(t);
    }
  }

  if(playerEnabled) game.appendChild(player);
  if(mobsEnabled) mobs.forEach(m=>game.appendChild(m.el));

  updatePlayer();
  updateMobs();
}

/* PLAYER */
function updatePlayer(){
  if(!playerEnabled) return;
  const viewportWidth = game.clientWidth;
  const viewportHeight = game.clientHeight;
  player.style.left=(px*tileSize-camX)+'px';
  player.style.top=(py*tileSize-camY)+'px';
}

function move(dx,dy){
  if(playerEnabled){
    px=Math.max(0,Math.min(cols-1,px+dx));
    py=Math.max(0,Math.min(rows-1,py+dy));
  } else {
    camX-=dx*tileSize; camY-=dy*tileSize;
    camX=Math.max(0,Math.min(camX, cols*tileSize-game.clientWidth));
    camY=Math.max(0,Math.min(camY, rows*tileSize-game.clientHeight));
  }
  drawWorld();
}

/* MOBS */
function updateMobs(){
  if(!mobsEnabled) return;
  mobs.forEach(m=>{
    if(Math.random()<0.02){ m.dx=rand(3)-1; m.dy=rand(3)-1; }
    m.x=Math.max(0,Math.min(cols-1,m.x+m.dx));
    m.y=Math.max(0,Math.min(rows-1,m.y+m.dy));
    m.el.style.left=(m.x*tileSize-camX)+'px';
    m.el.style.top=(m.y*tileSize-camY)+'px';
  });
}

function gameLoop(){ requestAnimationFrame(gameLoop); updateMobs(); }
gameLoop();

/* INPUT */
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

/* BUILD / DOODLE */
game.onclick=e=>{
  const r=game.getBoundingClientRect();
  const x=Math.floor((e.clientX-r.left)/tileSize + camX/tileSize);
  const y=Math.floor((e.clientY-r.top)/tileSize + camY/tileSize);
  if(!inBounds(x,y)) return;
  world[y][x]=selectedTile==='rainbow'?'rainbow':
               selectedTile==='invisible'?'invisible':
               selectedTile;
  drawWorld();
  saveWorld();
};

/* SAVE / LOAD */
function saveWorld(){ localStorage.setItem('sandboxWorld',JSON.stringify(world)); }
function resetWorld(){ localStorage.removeItem('sandboxWorld'); generateWorld(); }
function downloadWorld(){
  const blob=new Blob([JSON.stringify(world)],{type:'application/json'});
  const a=document.createElement('a');
  a.href=URL.createObjectURL(blob);
  a.download='world.json';
  a.click();
}
function loadWorld(){ fileInput.click(); }
fileInput.onchange=e=>{
  const r=new FileReader();
  r.onload=()=>{ world=JSON.parse(r.result); drawWorld(); saveWorld(); };
  r.readAsText(e.target.files[0]);
}

/* CLEAR DOODLE - Blank white page */
function clearDoodle(){
  world = Array.from({length:rows},()=>Array(cols).fill('invisible'));
  drawWorld();
}

/* TOGGLE PLAYER / MOBS */
document.getElementById('togglePlayerBtn').onclick=()=>{
  playerEnabled=!playerEnabled;
  document.getElementById('togglePlayerBtn').innerText=playerEnabled?'Disable Player':'Enable Player';
  drawWorld();
};
document.getElementById('toggleMobsBtn').onclick=()=>{
  mobsEnabled=!mobsEnabled;
  document.getElementById('toggleMobsBtn').innerText=mobsEnabled?'Disable Mobs':'Enable Mobs';
  drawWorld();
};

/* INVENTORY */
const colors = ['grass','tallgrass','water','sand','tree','rock','cactus','cave','rainbow','invisible','#ff0000','#00ff00','#0000ff','#ffff00','#ff00ff','#00ffff','#ffffff','#000000'];
colors.forEach(c=>{
  const i=document.createElement('div');
  i.className='inventory-item';
  if(c==='grass') i.style.background='#4caf50';
  else if(c==='tallgrass') i.style.background='#3e8f44';
  else if(c==='water') i.style.background='#4aa3df';
  else if(c==='sand') i.style.background='#e6d690';
  else if(c==='tree') i.style.background='#2f6f4e';
  else if(c==='rock') i.style.background='#6b6b6b';
  else if(c==='cactus') i.style.background='#6fbf73';
  else if(c==='cave') i.style.background='#2b2b2b';
  else if(c==='rainbow') i.style.background='linear-gradient(45deg,#f00,#ff0,#0f0,#0ff,#00f,#f0f)';
  else if(c==='invisible') i.style.background='transparent';
  else i.style.background=c;
  if(c===selectedTile) i.classList.add('selected');
  i.onclick=()=>{
    document.querySelectorAll('.inventory-item').forEach(e=>e.classList.remove('selected'));
    i.classList.add('selected');
    selectedTile=c;
  };
  inventory.appendChild(i);
});

/* INIT */
const saved=localStorage.getItem('sandboxWorld');
saved ? (world=JSON.parse(saved),drawWorld()) : generateWorld();
</script>
</body>
</html>
