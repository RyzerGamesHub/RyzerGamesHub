<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Pixel Sandbox Deluxe</title>

  <style>
    * {
      box-sizing: border-box;
      user-select: none;
    }

    body {
      margin: 0;
      background: #000;
      font-family: monospace;
      overflow: hidden;
      color: white;
    }

    /* TOP BAR */
    #top-bar {
      position: fixed;
      top: 0;
      left: 0;
      right: 0;
      height: 40px;
      display: flex;
      align-items: center;
      justify-content: space-between;
      padding: 0 8px;
      background: rgba(0,0,0,0.85);
      z-index: 1000;
    }

    #title {
      font-weight: bold;
    }

    #buttons {
      display: flex;
      gap: 6px;
      flex-wrap: wrap;
    }

    button {
      font-family: monospace;
      padding: 4px 8px;
      border: none;
      cursor: pointer;
      background: rgba(255,255,255,0.9);
      color: black;
    }

    /* LAYOUT */
    #app {
      position: absolute;
      top: 40px;
      left: 0;
      right: 0;
      bottom: 0;
      display: flex;
    }

    /* INVENTORY */
    #inventory {
      width: 60px;
      background: rgba(0,0,0,0.25);
      overflow-y: auto;
      padding: 6px 0;
      display: flex;
      flex-direction: column;
      align-items: center;
      gap: 6px;
    }

    #inventory::-webkit-scrollbar {
      width: 6px;
    }

    #inventory::-webkit-scrollbar-thumb {
      background: rgba(255,255,255,0.5);
      border-radius: 3px;
    }

    .inventory-item {
      width: 40px;
      height: 40px;
      border: 2px solid white;
      cursor: pointer;
      flex-shrink: 0;
    }

    .inventory-item.selected {
      border-color: yellow;
    }

    /* GAME AREA */
    #game {
      flex: 1;
      position: relative;
      overflow: hidden;
      background: #ffffff;
    }

    /* TOUCH CONTROLS (hidden by default) */
    #touch-controls {
      position: absolute;
      bottom: 12px;
      right: 12px;
      width: 120px;
      height: 120px;
      display: none;
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

    .up { grid-column: 2; grid-row: 1; }
    .left { grid-column: 1; grid-row: 2; }
    .right { grid-column: 3; grid-row: 2; }
    .down { grid-column: 2; grid-row: 3; }

    @media (max-width: 768px) {
      #touch-controls {
        display: grid;
      }
    }
  </style>
</head>

<body>

  <div id="top-bar">
    <div id="title">Pixel Sandbox Deluxe</div>
    <div id="buttons">
      <button>Reset</button>
      <button>Clear</button>
      <button>Download</button>
      <button>Load</button>
      <button>Player</button>
      <button>Mobs</button>
    </div>
  </div>

  <div id="app">
    <div id="inventory"></div>
    <div id="game"></div>
  </div>

  <div id="touch-controls">
    <div class="touch-btn up"></div>
    <div class="touch-btn left"></div>
    <div class="touch-btn right"></div>
    <div class="touch-btn down"></div>
  </div>

</body>
</html>

<script>
/* =========================
   WORLD CONFIG
========================= */
const tileSize = 20;
const cols = 200;
const rows = 150;

let world = [];
let selectedTile = 'grass';

/* =========================
   HELPERS
========================= */
function rand(max, min = 0) {
  return Math.floor(Math.random() * (max - min)) + min;
}

function inBounds(x, y) {
  return x >= 0 && y >= 0 && x < cols && y < rows;
}

/* =========================
   WORLD GENERATION
========================= */
function blob(cx, cy, r, type, allowed = ['grass']) {
  for (let y = -r; y <= r; y++) {
    for (let x = -r; x <= r; x++) {
      if (Math.hypot(x, y) <= r) {
        const nx = cx + x;
        const ny = cy + y;
        if (inBounds(nx, ny) && allowed.includes(world[ny][nx])) {
          world[ny][nx] = type;
        }
      }
    }
  }
}

function generateWorld() {
  world = Array.from({ length: rows }, () =>
    Array(cols).fill('grass')
  );

  // Water blobs
  for (let i = 0; i < 4; i++) {
    blob(rand(cols), rand(rows), rand(10, 25), 'water');
  }

  // Sand around water
  for (let y = 0; y < rows; y++) {
    for (let x = 0; x < cols; x++) {
      if (world[y][x] === 'water') {
        for (let dy = -1; dy <= 1; dy++) {
          for (let dx = -1; dx <= 1; dx++) {
            if (
              inBounds(x + dx, y + dy) &&
              world[y + dy][x + dx] === 'grass'
            ) {
              world[y + dy][x + dx] = 'sand';
            }
          }
        }
      }
    }
  }

  // Desert biome
  if (Math.random() < 0.7) {
    const cx = rand(cols);
    const cy = rand(rows);
    blob(cx, cy, rand(12, 20), 'sand', ['grass']);
    blob(cx, cy, rand(5, 10), 'cactus', ['sand']);
  }

  // Forests
  for (let i = 0; i < 5; i++) {
    blob(rand(cols), rand(rows), rand(8, 15), 'tree', ['grass']);
  }

  // Caves
  if (Math.random() < 0.25) {
    blob(rand(cols), rand(rows), rand(6, 10), 'cave', ['grass']);
  }

  // Tall grass
  for (let y = 0; y < rows; y++) {
    for (let x = 0; x < cols; x++) {
      if (world[y][x] === 'grass' && Math.random() < 0.15) {
        world[y][x] = 'tallgrass';
      }
    }
  }

  saveWorld();
}

/* =========================
   SAVE / LOAD
========================= */
function saveWorld() {
  localStorage.setItem('sandboxWorld', JSON.stringify(world));
}

function loadWorldFromStorage() {
  const saved = localStorage.getItem('sandboxWorld');
  if (saved) {
    world = JSON.parse(saved);
    return true;
  }
  return false;
}

function resetWorld() {
  localStorage.removeItem('sandboxWorld');
  generateWorld();
}

function clearWorld() {
  world = Array.from({ length: rows }, () =>
    Array(cols).fill('#ffffff')
  );
  saveWorld();
}

/* =========================
   INIT
========================= */
if (!loadWorldFromStorage()) {
  generateWorld();
}
</script>

<script>
/* =========================
   CANVAS SETUP
========================= */
const canvas = document.createElement('canvas');
const ctx = canvas.getContext('2d');
const gameContainer = document.getElementById('game');
gameContainer.appendChild(canvas);


function resizeCanvas() {
  canvas.width = gameContainer.clientWidth;
  canvas.height = gameContainer.clientHeight;
}
window.addEventListener('resize', resizeCanvas);
resizeCanvas();

/* =========================
   CAMERA
========================= */
const camera = {
  x: 0,
  y: 0,
  speed: 1
};

function clampCamera() {
  camera.x = Math.max(
    0,
    Math.min(camera.x, cols * tileSize - canvas.width)
  );
  camera.y = Math.max(
    0,
    Math.min(camera.y, rows * tileSize - canvas.height)
  );
}

/* =========================
   TILE COLORS
========================= */
const tileColors = {
  grass: '#3cb043',
  tallgrass: '#2f8f3a',
  water: '#2b65ec',
  sand: '#e2c290',
  tree: '#1f6b2d',
  cactus: '#3a8f3a',
  cave: '#444',
  '#ffffff': '#ffffff'
};

/* =========================
   RENDERING
========================= */
function render() {
  ctx.clearRect(0, 0, canvas.width, canvas.height);

  const startX = Math.floor(camera.x / tileSize);
  const startY = Math.floor(camera.y / tileSize);
  const endX = startX + Math.ceil(canvas.width / tileSize) + 1;
  const endY = startY + Math.ceil(canvas.height / tileSize) + 1;

  for (let y = startY; y < endY; y++) {
    for (let x = startX; x < endX; x++) {
      if (!inBounds(x, y)) continue;

      const tile = world[y][x];
      ctx.fillStyle = tileColors[tile] || '#000';

      ctx.fillRect(
        x * tileSize - camera.x,
        y * tileSize - camera.y,
        tileSize,
        tileSize
      );
    }
  }

  requestAnimationFrame(render);
}

/* =========================
   CAMERA DRAG (MOUSE)
========================= */
let dragging = false;
let lastMouse = { x: 0, y: 0 };

canvas.addEventListener('mousedown', e => {
  dragging = true;
  lastMouse.x = e.clientX;
  lastMouse.y = e.clientY;
});

window.addEventListener('mouseup', () => {
  dragging = false;
});

window.addEventListener('mousemove', e => {
  if (!dragging) return;

  const dx = e.clientX - lastMouse.x;
  const dy = e.clientY - lastMouse.y;

  camera.x -= dx * camera.speed;
  camera.y -= dy * camera.speed;

  clampCamera();

  lastMouse.x = e.clientX;
  lastMouse.y = e.clientY;
});

/* =========================
   START
========================= */
render();
</script>

<script>
/* =========================
   TILE PALETTE
========================= */
const palette = document.createElement('div');
palette.style.position = 'fixed';
palette.style.left = '10px';
palette.style.bottom = '10px';
palette.style.background = '#111';
palette.style.padding = '8px';
palette.style.borderRadius = '8px';
palette.style.display = 'flex';
palette.style.gap = '6px';
palette.style.zIndex = 10;
document.body.appendChild(palette);

const tiles = [
  'grass',
  'tallgrass',
  'water',
  'sand',
  'tree',
  'cactus',
  'cave'
];

function rebuildPalette() {
  palette.innerHTML = '';
  tiles.forEach(t => {
    const btn = document.createElement('div');
    btn.style.width = '24px';
    btn.style.height = '24px';
    btn.style.background = tileColors[t];
    btn.style.border =
      selectedTile === t ? '2px solid white' : '2px solid #000';
    btn.style.cursor = 'pointer';

    btn.onclick = () => {
      selectedTile = t;
      rebuildPalette();
    };

    palette.appendChild(btn);
  });
}

rebuildPalette();

/* =========================
   TILE EDITING
========================= */
function screenToWorld(x, y) {
  return {
    x: Math.floor((x + camera.x) / tileSize),
    y: Math.floor((y + camera.y) / tileSize)
  };
}

let painting = false;

canvas.addEventListener('contextmenu', e => e.preventDefault());

canvas.addEventListener('mousedown', e => {
  if (e.button === 0 || e.button === 2) {
    painting = true;
    paintTile(e);
  }
});

window.addEventListener('mouseup', () => {
  if (painting) saveWorld();
  painting = false;
});

canvas.addEventListener('mousemove', e => {
  if (painting) paintTile(e);
});

function paintTile(e) {
  const { x, y } = screenToWorld(e.clientX, e.clientY);
  if (!inBounds(x, y)) return;

  if (e.buttons === 2) {
    world[y][x] = 'grass';
  } else {
    world[y][x] = selectedTile;
  }
}

/* =========================
   KEYBOARD SHORTCUTS
========================= */
window.addEventListener('keydown', e => {
  if (e.key === 'r') {
    resetWorld();
  }
  if (e.key === 'c') {
    clearWorld();
  }
});

/* =========================
   HUD TEXT
========================= */
function drawHUD() {
  ctx.fillStyle = 'rgba(0,0,0,0.5)';
  ctx.fillRect(10, 10, 210, 52);

  ctx.fillStyle = '#fff';
  ctx.font = '14px monospace';
  ctx.fillText(`Tile: ${selectedTile}`, 20, 30);
  ctx.fillText(`R = reset | C = clear`, 20, 48);
}

/* inject HUD into render loop */
const oldRender = render;
render = function () {
  oldRender();
  drawHUD();
};
</script>
<script>
/* =========================
   PLAYER CONFIG
========================= */
const player = {
  x: cols * tileSize / 2,
  y: rows * tileSize / 2,
  w: tileSize * 0.6,
  h: tileSize * 0.6,
  speed: 3
};

const keys = {};

/* =========================
   SOLID TILES
========================= */
const solidTiles = new Set([
  'water',
  'tree',
  'cactus',
  'cave'
]);

function isSolid(x, y) {
  if (!inBounds(x, y)) return true;
  return solidTiles.has(world[y][x]);
}

/* =========================
   INPUT
========================= */
window.addEventListener('keydown', e => {
  keys[e.key.toLowerCase()] = true;
});

window.addEventListener('keyup', e => {
  keys[e.key.toLowerCase()] = false;
});

/* =========================
   COLLISION
========================= */
function canMove(nx, ny) {
  const left = Math.floor(nx / tileSize);
  const right = Math.floor((nx + player.w) / tileSize);
  const top = Math.floor(ny / tileSize);
  const bottom = Math.floor((ny + player.h) / tileSize);

  for (let y = top; y <= bottom; y++) {
    for (let x = left; x <= right; x++) {
      if (isSolid(x, y)) return false;
    }
  }
  return true;
}

/* =========================
   UPDATE LOOP
========================= */
function updatePlayer() {
  let dx = 0;
  let dy = 0;

  if (keys['w'] || keys['arrowup']) dy -= player.speed;
  if (keys['s'] || keys['arrowdown']) dy += player.speed;
  if (keys['a'] || keys['arrowleft']) dx -= player.speed;
  if (keys['d'] || keys['arrowright']) dx += player.speed;

  // Normalize diagonal movement
  if (dx && dy) {
    dx *= 0.707;
    dy *= 0.707;
  }

  if (canMove(player.x + dx, player.y)) {
    player.x += dx;
  }
  if (canMove(player.x, player.y + dy)) {
    player.y += dy;
  }

  // Camera follows
  camera.x = player.x - canvas.width / 2 + player.w / 2;
  camera.y = player.y - canvas.height / 2 + player.h / 2;
  clampCamera();
}

/* =========================
   DRAW PLAYER
========================= */
function drawPlayer() {
  ctx.fillStyle = '#ffcc66';
  ctx.fillRect(
    player.x - camera.x,
    player.y - camera.y,
    player.w,
    player.h
  );
}

/* =========================
   INJECT INTO RENDER
========================= */
const prevRender = render;
render = function () {
  updatePlayer();
  prevRender();
  drawPlayer();
};
</script>

<script>
let zoom = 1;
const zoomMin = 0.5;
const zoomMax = 2;

canvas.addEventListener('wheel', e => {
  e.preventDefault();
  zoom += e.deltaY * -0.001;
  zoom = Math.max(zoomMin, Math.min(zoomMax, zoom));
}, { passive: false });

const baseRender = render;
render = function () {
  ctx.save();
  ctx.scale(zoom, zoom);
  baseRender();
  ctx.restore();
};


/* =========================
   MINIMAP
========================= */
const minimap = {
  size: 160,
  padding: 10
};

function drawMinimap() {
  const scaleX = minimap.size / (cols * tileSize);
  const scaleY = minimap.size / (rows * tileSize);

  const x = canvas.width - minimap.size - minimap.padding;
  const y = minimap.padding;

  ctx.fillStyle = 'rgba(0,0,0,0.6)';
  ctx.fillRect(x - 4, y - 4, minimap.size + 8, minimap.size + 8);

  for (let ty = 0; ty < rows; ty += 4) {
    for (let tx = 0; tx < cols; tx += 4) {
      const tile = world[ty][tx];
      ctx.fillStyle = tileColors[tile] || '#000';

      ctx.fillRect(
        x + tx * tileSize * scaleX,
        y + ty * tileSize * scaleY,
        3,
        3
      );
    }
  }

  // Player dot
  ctx.fillStyle = '#ffcc66';
  ctx.fillRect(
    x + player.x * scaleX,
    y + player.y * scaleY,
    4,
    4
  );
}

/* =========================
   INJECT MINIMAP
========================= */
const renderWithMinimap = render;
render = function () {
  renderWithMinimap();
  drawMinimap();
};
</script>
