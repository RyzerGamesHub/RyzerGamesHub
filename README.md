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
}

button {
  font-family:monospace;
  padding:4px 8px;
  border:none;
  background:rgba(255,255,255,.9);
}

#inventory {
  position:absolute;
  top:40px;
  left:0;
  width:48px;
  height:calc(100vh - 40px);
  background:rgba(0,0,0,.25);
  display:flex;
  flex-direction:column;
  align-items:center;
  gap:6px;
  padding-top:6px;
  z-index:1000;
}

.inventory-item {
  width:28px;
  height:28px;
  border:2px solid white;
  cursor:pointer;
}
.selected { border-color:yellow }

#color-select {
  position:absolute;
  top:40px;
  left:50px;
  z-index:1000;
}

#game {
  position:absolute;
  top:40px;
  left:48px;
  width:calc(100vw - 48px);
  height:calc(100vh - 40px);
}

.tile {
  position:absolute;
  width:20px;
  height:20px;
}

.grass { background:#4caf50 }
.tallgrass { background:#3e8f44 }
.water { background:#4aa3df }
.sand { background:#e6d690 }
.tree { background:#2f6f4e }
.rock { background:#6b6b6b }
.cactus { background:#6fbf73 }
.cave { background:#2b2b2b }

/* ENTITIES */
#player, #mob {
  position:absolute;
  width:18px;
  height:18px;
  border-radius:2px;
  z-index:10;
}
#player { background:orange }
#mob { z-index:9 }

/* TOUCH CONTROLS */
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
.touch-btn { background:rgba(255,255,255,.25); border-radius:8px }
.up{grid-column:2;grid-row:1}
.left{grid-column:1;grid-row:2}
.right{grid-column:3;grid-row:2}
.down{grid-column:2;grid-row:3}
</style>
</head>

<body>
<div id="title">Pixel Sandbox Deluxe</div>

<div id="top-buttons">
  <button onclick="resetWorld()">Reset</button>
  <button onclick="clearDoodle()">Clear Doodle</button>
  <button onclick="downloadWorld()">Download</button>
  <button onclick="loadWorld()">Load</button>
</div>

<div id="inventory"></div>
<select id="color-select">
  <option value="rainbow">Rainbow</option>
  <option value="invisible">Invisible</option>
  <option value="#ff0000">Red</option>
  <option value="#00ff00">Green</option>
  <option value="#0000ff">Blue</option>
  <option value="#ffff00">Yellow</option>
  <option value="#ff00ff">Magenta</option>
  <option value="#00ffff">Cyan</option>
  <option value="#ffffff">White</option>
  <option value="#000000">Black</option>
</select>

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
const colorSelect = document.getElementById('color-select');

const tileSize = 20;
const cols = Math.floor((innerWidth-48)/tileSize);
const rows = Math.floor((innerHeight-40)/tileSize);

let world = [];
let selectedTile = 'grass';

/* ENTITIES */
const player = document.createElement('div');
player.id='player';
const mob = document.createElement('div');
mob.id='mob';
let px=Math.floor(cols/2), py=Math.floor(rows/2);
let mx=Math.floor(Math.random()*cols), my=Math.floor(Math.random()*rows);

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
      for(let dy=-1;dy<=1;dy++){
        for(let dx=-1;dx<=1;dx++){
          if(inBounds(x+dx,y+dy)&&world[y+dy][x+dx]==='grass') world[y+dy][x+dx]='sand';
        }
      }
    }
  }}

  if(Math.random()<0.7){
    const cx=rand(cols),cy=rand(rows);
    blob(cx,cy,rand(12,20),'sand',['grass']);
    blob(cx,cy,rand(5,10),'cactus',['sand']);
  }

  for(let i=0;i<5;i++) blob(rand(cols),rand(rows),rand(8,15),'tree',['grass']);
  for(let y=0;y<rows;y++){ for(let x=0;x<cols;x++){
    if(world[y][x]==='grass' && Math.random()<0.15) world[y][x]='tallgrass';
  }}

  if(Math.random()<0.25) blob(rand(cols),rand(rows),rand(6,10),'cave',['grass','dirt']);

  px=Math.floor(cols/2); py=Math.floor(rows/2);
  mx=rand(cols); my=rand(rows);

  drawWorld();
  saveWorld();
}

/* DRAW */
function drawWorld(){
  game.innerHTML='';
  for(let y=0;y<rows;y++){
    for(let x=0;x<cols;x++){
      const t=document.createElement('div');
      t.className='tile '+world[y][x];
      t.style.left=x*tileSize+'px';
      t.style.top=y*tileSize+'px';
      game.appendChild(t);
    }
  }

  game.appendChild(player);
  game.appendChild(mob);
  updatePlayer();
  updateMob();
}

/* PLAYER */
function updatePlayer(){ player.style.left=px*tileSize+1+'px'; player.style.top=py*tileSize+1+'px'; }
function move(dx,dy){ px=Math.max(0,Math.min(cols-1,px+dx)); py=Math.max(0,Math.min(rows-1,py+dy)); updatePlayer(); }

/* MOB */
function updateMob(){ 
  mob.style.left=mx*tileSize+1+'px'; 
  mob.style.top=my*tileSize+1+'px'; 
  mob.style.background='repeating-linear-gradient(45deg,#000 0 2px,#fff 2px 4px)';
}
setInterval(()=>{
  mx+=rand(2,-1); my+=rand(2,-1);
  mx=Math.max(0,Math.min(cols-1,mx)); my=Math.max(0,Math.min(rows-1,my));
  updateMob();
},500);

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
  const x=Math.floor((e.clientX-r.left)/tileSize);
  const y=Math.floor((e.clientY-r.top)/tileSize);
  if(!inBounds(x,y)) return;

  const val = colorSelect.value;
  if(val==='invisible') world[y][x]='';
  else if(val==='rainbow') world[y][x]='rainbow';
  else world[y][x]=val;

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

/* CLEAR DOODLE */
function clearDoodle(){
  for(let y=0;y<rows;y++){
    for(let x=0;x<cols;x++){
      if(!['grass','tallgrass','water','sand','tree','rock','cactus','cave'].includes(world[y][x])){
        world[y][x]='grass';
      }
    }
  }
  drawWorld();
  saveWorld();
}

/* INVENTORY */
['grass','tallgrass','water','sand','tree','rock','cactus','cave'].forEach(type=>{
  const i=document.createElement('div');
  i.className='inventory-item';
  i.style.background=window.getComputedStyle(document.body.appendChild(Object.assign(document.createElement('div'),{className:type}))).backgroundColor;
  document.body.lastChild.remove();
  if(type===selectedTile) i.classList.add('selected');
  i.onclick=()=>{
    document.querySelectorAll('.inventory-item').forEach(e=>e.classList.remove('selected'));
    i.classList.add('selected');
    selectedTile=type;
    colorSelect.value=type;
  };
  inventory.appendChild(i);
});

/* INIT */
const saved=localStorage.getItem('sandboxWorld');
saved ? (world=JSON.parse(saved),drawWorld()) : generateWorld();
</script>

</body>
</html>
