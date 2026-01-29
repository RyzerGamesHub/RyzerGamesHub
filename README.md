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
