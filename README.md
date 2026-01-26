<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Pixel Sandbox Deluxe</title>
<style>
body {margin:0;overflow:hidden;background:#000;font-family:monospace;}
#title {position:absolute;top:6px;left:50%;transform:translateX(-50%);color:white;font-weight:bold;z-index:1000;}
#top-buttons {position:absolute;top:6px;right:6px;display:flex;gap:6px;z-index:1000;flex-wrap:wrap;}
button {font-family:monospace;padding:4px 8px;border:none;background:rgba(255,255,255,.9);cursor:pointer;}
#inventory {position:absolute;top:40px;left:0;width:60px;height:calc(100vh - 40px);background:rgba(0,0,0,.25);display:flex;flex-direction:column;align-items:center;gap:6px;padding-top:6px;z-index:1000;overflow-y:auto;scrollbar-width:thin;scrollbar-color: rgba(255,255,255,.5) transparent;}
#inventory::-webkit-scrollbar { width:6px; }
#inventory::-webkit-scrollbar-thumb { background:rgba(255,255,255,.5); border-radius:3px; }
.inventory-item { width:40px; height:40px; border:2px solid white; cursor:pointer; flex-shrink:0; }
.selected { border-color:yellow }
#game { position:absolute; top:40px; left:60px; width:calc(100vw - 60px); height:calc(100vh - 40px); overflow:hidden; }
.tile { position:absolute; width:20px; height:20px; }
#player, .mob { position:absolute; width:18px; height:18px; border-radius:2px; z-index:10; display:flex; align-items:center; justify-content:center; font-size:10px; color:#fff; font-weight:bold; }
#player { background:orange; }
#touch-controls { position:absolute; bottom:12px; right:12px; width:120px; height:120px; display:grid; grid-template-columns:repeat(3,1fr); grid-template-rows:repeat(3,1fr); gap:6px; z-index:1000; }
.touch-btn { background:rgba(255,255,255,.25); border-radius:8px; touch-action:none; }
.up{grid-column:2;grid-row:1} .left{grid-column:1;grid-row:2} .right{grid-column:3;grid-row:2} .down{grid-column:2;grid-row:3}
@media(max-width:768px){#top-buttons{flex-direction:column;top:auto;bottom:12px;right:12px;}}
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

const tileSize=20;
const worldCols=200;  // bigger world for exploration
const worldRows=200;

let world=[];
let selectedTile='grass';

/* ENTITIES */
const player = document.createElement('div'); player.id='player'; game.appendChild(player);
let px=Math.floor(worldCols/2), py=Math.floor(worldRows/2);

/* MULTIPLE MOBS */
const mobs=[];
for(let i=0;i<5;i++){
  const m=document.createElement('div');
  m.className='mob';
  m.textContent='🐾'; // small face
  game.appendChild(m);
  mobs.push({x:Math.floor(Math.random()*worldCols),y:Math.floor(Math.random()*worldRows),dx:0,dy:0,el:m});
}

/* HELPERS */
function inBounds(x,y){ return x>=0 && y>=0 && x<worldCols && y<worldRows; }
function rand(max,min=0){ return Math.floor(Math.random()*(max-min))+min; }
function blob(cx,cy,r,type,allowed=['grass']){for(let y=-r;y<=r;y++){for(let x=-r;x<=r;x++){if(Math.hypot(x,y)<=r && inBounds(cx+x,cy+y)){if(allowed.includes(world[cy+y][cx+x])) world[cy+y][cx+x]=type;}}}}

/* WORLD GEN */
function generateWorld(){
  world=Array.from({length:worldRows},()=>Array(worldCols).fill('grass'));
  for(let i=0;i<10;i++) blob(rand(worldCols),rand(worldRows),rand(10,25),'water');
  for(let y=0;y<worldRows;y++){for(let x=0;x<worldCols;x++){if(world[y][x]==='water'){for(let dy=-1;dy<=1;dy++){for(let dx=-1;dx<=1;dx++){if(inBounds(x+dx,y+dy)&&world[y+dy][x+dx]==='grass') world[y+dy][x+dx]='sand';}}}}}
  if(Math.random()<0.7){ const cx=rand(worldCols),cy=rand(worldRows); blob(cx,cy,rand(12,20),'sand',['grass']); blob(cx,cy,rand(5,10),'cactus',['sand']); }
  for(let i=0;i<10;i++) blob(rand(worldCols),rand(worldRows),rand(8,15),'tree',['grass']);
  for(let y=0;y<worldRows;y++){for(let x=0;x<worldCols;x++){if(world[y][x]==='grass' && Math.random()<0.15) world[y][x]='tallgrass';}}
  if(Math.random()<0.25) blob(rand(worldCols),rand(worldRows),rand(6,10),'cave',['grass','dirt']);
  px=Math.floor(worldCols/2); py=Math.floor(worldRows/2);
  drawWorld();
  saveWorld();
}

/* DRAW & CAMERA */
function drawWorld(){
  game.innerHTML='';
  for(let y=0;y<worldRows;y++){for(let x=0;x<worldCols;x++){
    const t=document.createElement('div');
    t.className='tile';
    t.style.left=x*tileSize+'px';
    t.style.top=y*tileSize+'px';
    const val=world[y][x];
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
  }}
  game.appendChild(player);
  mobs.forEach(m=>game.appendChild(m.el));
  updateCamera();
  updatePlayer();
  updateMobs();
}

/* CAMERA FOLLOW PLAYER */
function updateCamera(){
  const camX=Math.min(Math.max(px*tileSize - (window.innerWidth-60)/2,0), worldCols*tileSize-(window.innerWidth-60));
  const camY=Math.min(Math.max(py*tileSize - (window.innerHeight-40)/2,0), worldRows*tileSize-(window.innerHeight-40));
  game.style.left=(60-camX)+'px';
  game.style.top=(40-camY)+'px';
}

/* PLAYER */
function updatePlayer(){ player.style.left=px*tileSize+1+'px'; player.style.top=py*tileSize+1+'px'; }
function move(dx,dy){ px=Math.max(0,Math.min(worldCols-1,px+dx)); py=Math.max(0,Math.min(worldRows-1,py+dy)); updateCamera(); updatePlayer(); }

/* MOB MOVEMENT SMOOTH */
function updateMobs(){
  mobs.forEach(m=>{
    if(Math.random()<0.05){ m.dx=rand(3,-1); m.dy=rand(3,-1); }
    m.x=Math.max(0,Math.min(worldCols-1,m.x+m.dx*0.1));
    m.y=Math.max(0,Math.min(worldRows-1,m.y+m.dy*0.1));
    m.el.style.left=m.x*tileSize+1+'px';
    m.el.style.top=m.y*tileSize+1+'px';
  });
}
function animate(){
  updateMobs();
  requestAnimationFrame(animate);
}
animate();

/* INPUT */
document.addEventListener('keydown',e=>{
  if(e.key==='ArrowUp') move(0,-1);
  if(e.key==='ArrowDown') move(0,1);
  if(e.key==='ArrowLeft') move(-1,0);
  if(e.key==='ArrowRight') move(1,0);
});
document.querySelector('.up').ontouchstart=()=>move(0,-1);
document.querySelector('.down').ontouchstart=()=>move(0,1);
document.querySelector('.left').ontouchstart=()=>move(-1,0);
document.querySelector('.right').ontouchstart=()=>move(1,0);

/* BUILD / DOODLE */
const colors=['grass','tallgrass','water','sand','tree','rock','cactus','cave','rainbow','invisible','#ff0000','#00ff00','#0000ff','#ffff00','#ff00ff','#00ffff','#ffffff','#000000'];
colors.forEach(c=>{
  const i=document.createElement('div'); i.className='inventory-item';
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
  i.onclick=()=>{ document.querySelectorAll('.inventory-item').forEach(e=>e.classList.remove('selected')); i.classList.add('selected'); selectedTile=c; };
  inventory.appendChild(i);
});
game.onclick=e=>{
  const r=game.getBoundingClientRect();
  const x=Math.floor((e.clientX-r.left)/tileSize);
  const y=Math.floor((e.clientY-r.top)/tileSize);
  if(!inBounds(x,y)) return;
  world[y][x] = selectedTile==='rainbow'?'rainbow':selectedTile==='invisible'?'invisible':selectedTile;
  drawWorld(); saveWorld();
};

/* SAVE / LOAD */
function saveWorld(){ localStorage.setItem('sandboxWorld',JSON.stringify(world)); }
function resetWorld(){ localStorage.removeItem('sandboxWorld'); generateWorld(); }
function downloadWorld(){ const blob=new Blob([JSON.stringify(world)],{type:'application/json'}); const a=document.createElement('a'); a.href=URL.createObjectURL(blob); a.download='world.json'; a.click(); }
function loadWorld(){ fileInput.click(); }
fileInput.onchange=e=>{ const r=new FileReader(); r.onload=()=>{ world=JSON.parse(r.result); drawWorld(); saveWorld(); }; r.readAsText(e.target.files[0]); }

/* CLEAR DOODLE */
function clearDoodle(){ for(let y=0;y<worldRows;y++){for(let x=0;x<worldCols;x++){if(!['grass','tallgrass','water','sand','tree','rock','cactus','cave'].includes(world[y][x])) world[y][x]='grass';}} drawWorld(); saveWorld();}

/* INIT */
const saved=localStorage.getItem('sandboxWorld'); saved? (world=JSON.parse(saved),drawWorld()) : generateWorld();
</script>
</body>
</html>
