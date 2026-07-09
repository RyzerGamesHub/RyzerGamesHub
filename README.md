
html_content = '''<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no">
    <title>Pixel Sandbox Deluxe</title>
    <style>
        * { margin: 0; padding: 0; box-sizing: border-box; }
        body {
            background: #1a1a2e;
            color: #e0e0e0;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            overflow: hidden;
            touch-action: none;
            user-select: none;
            -webkit-user-select: none;
        }
        #game-container {
            position: relative;
            width: 100vw;
            height: 100vh;
            overflow: hidden;
        }
        canvas { display: block; }
        #world-canvas {
            position: absolute;
            top: 0; left: 0;
            image-rendering: pixelated;
            image-rendering: crisp-edges;
        }
        #ui-layer {
            position: absolute;
            top: 0; left: 0;
            width: 100%; height: 100%;
            pointer-events: none;
            z-index: 10;
        }
        .panel {
            pointer-events: auto;
            background: rgba(20, 20, 35, 0.92);
            border: 1px solid rgba(100, 100, 150, 0.3);
            border-radius: 12px;
            backdrop-filter: blur(8px);
            box-shadow: 0 4px 20px rgba(0,0,0,0.4);
        }
        #toolbar {
            position: absolute;
            top: 10px;
            left: 10px;
            display: flex;
            flex-direction: column;
            gap: 6px;
            padding: 10px;
            max-width: 180px;
        }
        .tool-btn {
            background: rgba(60, 60, 90, 0.7);
            border: 1px solid rgba(100, 100, 150, 0.4);
            color: #ccc;
            padding: 8px 12px;
            border-radius: 8px;
            cursor: pointer;
            font-size: 13px;
            transition: all 0.2s;
            display: flex;
            align-items: center;
            gap: 6px;
            white-space: nowrap;
        }
        .tool-btn:hover { background: rgba(80, 80, 120, 0.8); color: #fff; }
        .tool-btn.active { background: rgba(100, 150, 200, 0.6); border-color: rgba(150, 200, 255, 0.6); color: #fff; }
        .tool-btn svg { width: 16px; height: 16px; fill: currentColor; flex-shrink: 0; }
        #palette-panel {
            position: absolute;
            bottom: 10px;
            left: 50%;
            transform: translateX(-50%);
            padding: 10px;
            display: flex;
            flex-direction: column;
            gap: 8px;
            max-width: 90vw;
        }
        #palette-scroll {
            display: flex;
            gap: 4px;
            overflow-x: auto;
            padding: 4px;
            max-width: 600px;
            scrollbar-width: thin;
            scrollbar-color: rgba(100,100,150,0.5) transparent;
        }
        #palette-scroll::-webkit-scrollbar { height: 6px; }
        #palette-scroll::-webkit-scrollbar-thumb { background: rgba(100,100,150,0.5); border-radius: 3px; }
        .color-swatch {
            width: 32px; height: 32px;
            border-radius: 6px;
            border: 2px solid transparent;
            cursor: pointer;
            flex-shrink: 0;
            transition: transform 0.15s, border-color 0.15s;
            position: relative;
        }
        .color-swatch:hover { transform: scale(1.15); }
        .color-swatch.active { border-color: #fff; box-shadow: 0 0 8px rgba(255,255,255,0.3); }
        .color-swatch.eraser {
            background: repeating-conic-gradient(#333 0% 25%, #444 0% 50%);
            background-size: 8px 8px;
        }
        .color-swatch.eraser::after {
            content: "✕";
            position: absolute;
            top: 50%; left: 50%;
            transform: translate(-50%, -50%);
            color: #fff;
            font-size: 14px;
            font-weight: bold;
        }
        #minimap {
            position: absolute;
            top: 10px;
            right: 10px;
            width: 140px;
            height: 140px;
            border-radius: 10px;
            overflow: hidden;
            border: 2px solid rgba(100, 100, 150, 0.4);
        }
        #minimap canvas {
            width: 100%; height: 100%;
            image-rendering: pixelated;
        }
        #info-bar {
            position: absolute;
            bottom: 10px;
            right: 10px;
            padding: 8px 12px;
            font-size: 12px;
            color: #888;
            text-align: right;
        }
        #mobile-controls {
            position: absolute;
            bottom: 80px;
            right: 10px;
            display: none;
            flex-direction: column;
            gap: 6px;
        }
        .mobile-btn {
            width: 50px; height: 50px;
            border-radius: 50%;
            background: rgba(60, 60, 90, 0.7);
            border: 1px solid rgba(100, 100, 150, 0.4);
            color: #ccc;
            font-size: 20px;
            display: flex;
            align-items: center;
            justify-content: center;
            cursor: pointer;
            touch-action: manipulation;
        }
        .mobile-btn:active { background: rgba(100, 150, 200, 0.6); }
        #d-pad {
            position: absolute;
            bottom: 80px;
            left: 10px;
            display: none;
            width: 120px; height: 120px;
        }
        .dpad-btn {
            position: absolute;
            width: 40px; height: 40px;
            background: rgba(60, 60, 90, 0.6);
            border: 1px solid rgba(100, 100, 150, 0.3);
            border-radius: 8px;
            color: #ccc;
            font-size: 18px;
            display: flex;
            align-items: center;
            justify-content: center;
            cursor: pointer;
        }
        .dpad-btn:active { background: rgba(100, 150, 200, 0.5); }
        .dpad-up { top: 0; left: 40px; }
        .dpad-down { bottom: 0; left: 40px; }
        .dpad-left { top: 40px; left: 0; }
        .dpad-right { top: 40px; right: 0; }
        #brush-size {
            display: flex;
            align-items: center;
            gap: 8px;
            padding: 4px 0;
        }
        #brush-size input[type="range"] {
            flex: 1;
            accent-color: rgba(100, 150, 200, 0.8);
        }
        #brush-size-label {
            font-size: 12px;
            color: #888;
            min-width: 40px;
            text-align: center;
        }
        .separator {
            height: 1px;
            background: rgba(100, 100, 150, 0.2);
            margin: 4px 0;
        }
        .section-label {
            font-size: 10px;
            text-transform: uppercase;
            letter-spacing: 1px;
            color: #666;
            margin-bottom: 2px;
        }
        @media (max-width: 768px) {
            #mobile-controls, #d-pad { display: flex; }
            #toolbar { max-width: 140px; }
            .tool-btn { font-size: 11px; padding: 6px 8px; }
            #minimap { width: 100px; height: 100px; }
        }
        #load-input { display: none; }
        .toggle-row {
            display: flex;
            align-items: center;
            justify-content: space-between;
            padding: 4px 0;
            font-size: 12px;
        }
        .toggle-switch {
            width: 36px; height: 20px;
            background: #444;
            border-radius: 10px;
            position: relative;
            cursor: pointer;
            transition: background 0.2s;
        }
        .toggle-switch.on { background: rgba(100, 150, 200, 0.7); }
        .toggle-switch::after {
            content: "";
            position: absolute;
            width: 16px; height: 16px;
            background: #fff;
            border-radius: 50%;
            top: 2px; left: 2px;
            transition: left 0.2s;
        }
        .toggle-switch.on::after { left: 18px; }
        #notification {
            position: absolute;
            top: 50%; left: 50%;
            transform: translate(-50%, -50%);
            background: rgba(20, 20, 35, 0.95);
            border: 1px solid rgba(100, 150, 200, 0.5);
            padding: 16px 24px;
            border-radius: 12px;
            font-size: 14px;
            pointer-events: none;
            opacity: 0;
            transition: opacity 0.3s;
            z-index: 100;
        }
        #notification.show { opacity: 1; }
    </style>
</head>
<body>
    <div id="game-container">
        <canvas id="world-canvas"></canvas>
        
        <div id="ui-layer">
            <div id="toolbar" class="panel">
                <div class="section-label">World</div>
                <button class="tool-btn" id="btn-generate" title="Generate New World">
                    <svg viewBox="0 0 24 24"><path d="M12 2C6.48 2 2 6.48 2 12s4.48 10 10 10 10-4.48 10-10S17.52 2 12 2zm-1 17.93c-3.95-.49-7-3.85-7-7.93 0-.62.08-1.21.21-1.79L9 15v1c0 1.1.9 2 2 2v1.93zm6.9-2.54c-.26-.81-1-1.39-1.9-1.39h-1v-3c0-.55-.45-1-1-1H8v-2h2c.55 0 1-.45 1-1V7h2c1.1 0 2-.9 2-2v-.41c2.93 1.19 5 4.06 5 7.41 0 2.08-.8 3.97-2.1 5.39z"/></svg>
                    Generate
                </button>
                <button class="tool-btn" id="btn-clear" title="Clear to Blank Canvas">
                    <svg viewBox="0 0 24 24"><path d="M19 6.41L17.59 5 12 10.59 6.41 5 5 6.41 10.59 12 5 17.59 6.41 19 12 13.41 17.59 19 19 17.59 13.41 12z"/></svg>
                    Clear Canvas
                </button>
                <div class="separator"></div>
                <div class="section-label">Mode</div>
                <button class="tool-btn active" id="mode-paint" title="Paint Mode">
                    <svg viewBox="0 0 24 24"><path d="M3 17.25V21h3.75L17.81 9.94l-3.75-3.75L3 17.25zM20.71 7.04c.39-.39.39-1.02 0-1.41l-2.34-2.34c-.39-.39-1.02-.39-1.41 0l-1.83 1.83 3.75 3.75 1.83-1.83z"/></svg>
                    Paint
                </button>
                <button class="tool-btn" id="mode-explore" title="Explore Mode">
                    <svg viewBox="0 0 24 24"><path d="M12 2C8.13 2 5 5.13 5 9c0 5.25 7 13 7 13s7-7.75 7-13c0-3.87-3.13-7-7-7zm0 9.5c-1.38 0-2.5-1.12-2.5-2.5s1.12-2.5 2.5-2.5 2.5 1.12 2.5 2.5-1.12 2.5-2.5 2.5z"/></svg>
                    Explore
                </button>
                <div class="separator"></div>
                <div class="section-label">Entities</div>
                <div class="toggle-row">
                    <span>Player</span>
                    <div class="toggle-switch on" id="toggle-player"></div>
                </div>
                <div class="toggle-row">
                    <span>Mobs</span>
                    <div class="toggle-switch on" id="toggle-mobs"></div>
                </div>
                <div class="separator"></div>
                <div class="section-label">Atmosphere</div>
                <div class="toggle-row">
                    <span>Day/Night</span>
                    <div class="toggle-switch on" id="toggle-daynight"></div>
                </div>
                <div class="toggle-row">
                    <span>Weather</span>
                    <div class="toggle-switch" id="toggle-weather"></div>
                </div>
                <div class="separator"></div>
                <div class="section-label">File</div>
                <button class="tool-btn" id="btn-save" title="Save World to File">
                    <svg viewBox="0 0 24 24"><path d="M19 12v7H5v-7H3v7c0 1.1.9 2 2 2h14c1.1 0 2-.9 2-2v-7h-2zm-6 .67l2.59-2.58L17 11.5l-5 5-5-5 1.41-1.41L11 12.67V3h2z"/></svg>
                    Save
                </button>
                <button class="tool-btn" id="btn-load" title="Load World from File">
                    <svg viewBox="0 0 24 24"><path d="M19.35 10.04C18.67 6.59 15.64 4 12 4 9.11 4 6.6 5.64 5.35 8.04 2.34 8.36 0 10.91 0 14c0 3.31 2.69 6 6 6h13c2.76 0 5-2.24 5-5 0-2.64-2.05-4.78-4.65-4.96zM14 13v4h-4v-4H7l5-5 5 5h-3z"/></svg>
                    Load
                </button>
                <input type="file" id="load-input" accept=".json,.psd,.txt">
            </div>

            <div id="minimap" class="panel">
                <canvas id="minimap-canvas" width="140" height="140"></canvas>
            </div>

            <div id="palette-panel" class="panel">
                <div id="brush-size">
                    <span style="font-size:12px;">Brush:</span>
                    <input type="range" id="brush-slider" min="1" max="10" value="1">
                    <span id="brush-size-label">1px</span>
                </div>
                <div id="palette-scroll"></div>
            </div>

            <div id="mobile-controls">
                <button class="mobile-btn" id="zoom-in">+</button>
                <button class="mobile-btn" id="zoom-out">−</button>
            </div>

            <div id="d-pad">
                <button class="dpad-btn dpad-up" id="dpad-up">▲</button>
                <button class="dpad-btn dpad-down" id="dpad-down">▼</button>
                <button class="dpad-btn dpad-left" id="dpad-left">◀</button>
                <button class="dpad-btn dpad-right" id="dpad-right">▶</button>
            </div>

            <div id="info-bar" class="panel">
                <div id="coords">0, 0</div>
                <div id="time-display">Day 1 — 08:00</div>
            </div>
        </div>

        <div id="notification"></div>
    </div>

<script>
// ==================== CONFIGURATION ====================
const WORLD_WIDTH = 256;
const WORLD_HEIGHT = 256;
const TILE_SIZE = 16;
const CHUNK_SIZE = 16;

// ==================== COLOR PALETTE ====================
const PALETTE = {
    terrain: [
        { name: "Void", color: "#0f0f1a" },
        { name: "Deep Water", color: "#1a3a5c" },
        { name: "Water", color: "#2e7dbd" },
        { name: "Shallow Water", color: "#4a9fd4" },
        { name: "Sand", color: "#e6c288" },
        { name: "Grass", color: "#4a8c3f" },
        { name: "Dark Grass", color: "#3a6e32" },
        { name: "Dirt", color: "#8b6914" },
        { name: "Stone", color: "#7a7a7a" },
        { name: "Deep Stone", color: "#5a5a5a" },
        { name: "Cave", color: "#3a3a3a" },
        { name: "Snow", color: "#e8e8e8" },
        { name: "Ice", color: "#a8d8ea" },
    ],
    nature: [
        { name: "Wood", color: "#6b4423" },
        { name: "Bark", color: "#4a2e15" },
        { name: "Leaves", color: "#2d5a27" },
        { name: "Autumn Leaves", color: "#c75b39" },
        { name: "Cactus", color: "#2d6e32" },
        { name: "Flower Red", color: "#d43d3d" },
        { name: "Flower Yellow", color: "#e6c82e" },
        { name: "Flower Purple", color: "#8e44ad" },
        { name: "Mushroom", color: "#c0392b" },
        { name: "Mushroom Stem", color: "#f5f5dc" },
    ],
    building: [
        { name: "Brick", color: "#a0522d" },
        { name: "Plank", color: "#d4a574" },
        { name: "Roof", color: "#8b4513" },
        { name: "Glass", color: "#add8e6" },
        { name: "Concrete", color: "#999999" },
        { name: "Metal", color: "#708090" },
        { name: "Gold", color: "#ffd700" },
        { name: "Obsidian", color: "#1a1a2e" },
    ],
    vibrant: [
        { name: "Red", color: "#e74c3c" },
        { name: "Orange", color: "#e67e22" },
        { name: "Yellow", color: "#f1c40f" },
        { name: "Green", color: "#2ecc71" },
        { name: "Teal", color: "#1abc9c" },
        { name: "Blue", color: "#3498db" },
        { name: "Indigo", color: "#5b6ef5" },
        { name: "Purple", color: "#9b59b6" },
        { name: "Pink", color: "#e91e63" },
        { name: "White", color: "#ffffff" },
        { name: "Light Gray", color: "#bdc3c7" },
        { name: "Dark Gray", color: "#555555" },
        { name: "Black", color: "#111111" },
    ]
};

const ALL_COLORS = [...PALETTE.terrain, ...PALETTE.nature, ...PALETTE.building, ...PALETTE.vibrant];

// ==================== TILE TYPES ====================
const TILES = {
    VOID: 0, DEEP_WATER: 1, WATER: 2, SHALLOW_WATER: 3,
    SAND: 4, GRASS: 5, DARK_GRASS: 6, DIRT: 7,
    STONE: 8, DEEP_STONE: 9, CAVE: 10, SNOW: 11, ICE: 12,
    WOOD: 13, BARK: 14, LEAVES: 15, AUTUMN_LEAVES: 16,
    CACTUS: 17, FLOWER_RED: 18, FLOWER_YELLOW: 19, FLOWER_PURPLE: 20,
    MUSHROOM: 21, MUSHROOM_STEM: 22, BRICK: 23, PLANK: 24,
    ROOF: 25, GLASS: 26, CONCRETE: 27, METAL: 28, GOLD: 29, OBSIDIAN: 30
};

const TILE_COLORS = [
    "#0f0f1a", "#1a3a5c", "#2e7dbd", "#4a9fd4", "#e6c288",
    "#4a8c3f", "#3a6e32", "#8b6914", "#7a7a7a", "#5a5a5a",
    "#3a3a3a", "#e8e8e8", "#a8d8ea", "#6b4423", "#4a2e15",
    "#2d5a27", "#c75b39", "#2d6e32", "#d43d3d", "#e6c82e",
    "#8e44ad", "#c0392b", "#f5f5dc", "#a0522d", "#d4a574",
    "#8b4513", "#add8e6", "#999999", "#708090", "#ffd700", "#1a1a2e"
];

// ==================== GAME STATE ====================
const state = {
    world: new Uint8Array(WORLD_WIDTH * WORLD_HEIGHT),
    camera: { x: WORLD_WIDTH * TILE_SIZE / 2, y: WORLD_HEIGHT * TILE_SIZE / 2, zoom: 1 },
    mode: 'paint', // 'paint' or 'explore'
    selectedColor: 5,
    brushSize: 1,
    isDrawing: false,
    lastDrawPos: null,
    playerEnabled: true,
    mobsEnabled: true,
    dayNightEnabled: true,
    weatherEnabled: false,
    time: 8 * 60, // minutes from midnight
    day: 1,
    weather: 'clear', // 'clear', 'rain', 'snow'
    weatherIntensity: 0,
    player: { x: WORLD_WIDTH / 2, y: WORLD_HEIGHT / 2, dir: 0, frame: 0 },
    mobs: [],
    keys: {},
    touchStart: null,
    touchLast: null,
    pinchStartDist: 0,
    pinchStartZoom: 1,
    particles: [],
    clouds: []
};

// ==================== CANVAS SETUP ====================
const canvas = document.getElementById('world-canvas');
const ctx = canvas.getContext('2d', { alpha: false });
const minimapCanvas = document.getElementById('minimap-canvas');
const minimapCtx = minimapCanvas.getContext('2d');

function resizeCanvas() {
    canvas.width = window.innerWidth;
    canvas.height = window.innerHeight;
    ctx.imageSmoothingEnabled = false;
}
resizeCanvas();
window.addEventListener('resize', resizeCanvas);

// ==================== NOISE & GENERATION ====================
function hash(x, y) {
    let h = x * 374761393 + y * 668265263;
    h = (h ^ (h >> 13)) * 1274126177;
    return (h ^ (h >> 16)) & 0x7fffffff;
}

function noise2D(x, y) {
    const ix = Math.floor(x), iy = Math.floor(y);
    const fx = x - ix, fy = y - iy;
    const a = hash(ix, iy) / 0x7fffffff;
    const b = hash(ix + 1, iy) / 0x7fffffff;
    const c = hash(ix, iy + 1) / 0x7fffffff;
    const d = hash(ix + 1, iy + 1) / 0x7fffffff;
    const u = fx * fx * (3 - 2 * fx);
    const v = fy * fy * (3 - 2 * fy);
    return a + (b - a) * u + (c - a) * v + (a - b - c + d) * u * v;
}

function fbm(x, y, octaves) {
    let val = 0, amp = 0.5, freq = 1;
    for (let i = 0; i < octaves; i++) {
        val += amp * noise2D(x * freq, y * freq);
        amp *= 0.5;
        freq *= 2;
    }
    return val;
}

function generateWorld() {
    const seed = Math.random() * 10000;
    for (let y = 0; y < WORLD_HEIGHT; y++) {
        for (let x = 0; x < WORLD_WIDTH; x++) {
            const nx = (x + seed) / 40;
            const ny = (y + seed) / 40;
            const elevation = fbm(nx, ny, 4);
            const moisture = fbm(nx + 100, ny + 100, 3);
            const caves = fbm(nx * 2, ny * 2, 2);
            
            let tile;
            if (elevation < -0.3) tile = TILES.DEEP_WATER;
            else if (elevation < -0.1) tile = TILES.WATER;
            else if (elevation < 0) tile = TILES.SHALLOW_WATER;
            else if (elevation < 0.15) tile = TILES.SAND;
            else if (elevation < 0.5) {
                if (moisture > 0.3) tile = TILES.GRASS;
                else tile = TILES.DARK_GRASS;
            } else if (elevation < 0.7) {
                if (caves > 0.6) tile = TILES.CAVE;
                else tile = TILES.DIRT;
            } else if (elevation < 0.85) tile = TILES.STONE;
            else if (elevation < 0.95) tile = TILES.DEEP_STONE;
            else tile = TILES.SNOW;
            
            // Trees
            if ((tile === TILES.GRASS || tile === TILES.DARK_GRASS) && Math.random() < 0.08) {
                // Leave as grass, render tree overlay
            }
            
            // Flowers
            if (tile === TILES.GRASS && Math.random() < 0.03) {
                const flowers = [TILES.FLOWER_RED, TILES.FLOWER_YELLOW, TILES.FLOWER_PURPLE];
                tile = flowers[Math.floor(Math.random() * flowers.length)];
            }
            
            state.world[y * WORLD_WIDTH + x] = tile;
        }
    }
    
    // Generate trees as 2x2 or 3x3 clusters
    for (let i = 0; i < 150; i++) {
        const tx = Math.floor(Math.random() * (WORLD_WIDTH - 4)) + 2;
        const ty = Math.floor(Math.random() * (WORLD_HEIGHT - 4)) + 2;
        if (state.world[ty * WORLD_WIDTH + tx] === TILES.GRASS || 
            state.world[ty * WORLD_WIDTH + tx] === TILES.DARK_GRASS) {
            const size = Math.random() < 0.5 ? 2 : 3;
            for (let dy = -1; dy < size - 1; dy++) {
                for (let dx = -1; dx < size - 1; dx++) {
                    const nx = tx + dx, ny = ty + dy;
                    if (nx >= 0 && nx < WORLD_WIDTH && ny >= 0 && ny < WORLD_HEIGHT) {
                        if (dy === -1 && dx === 0) {
                            state.world[ny * WORLD_WIDTH + nx] = TILES.BARK; // trunk
                        } else if (dy <= 0) {
                            state.world[ny * WORLD_WIDTH + nx] = TILES.LEAVES;
                        }
                    }
                }
            }
        }
    }
    
    // Cacti in sand
    for (let i = 0; i < 50; i++) {
        const cx = Math.floor(Math.random() * WORLD_WIDTH);
        const cy = Math.floor(Math.random() * WORLD_HEIGHT);
        if (state.world[cy * WORLD_WIDTH + cx] === TILES.SAND) {
            state.world[cy * WORLD_WIDTH + cx] = TILES.CACTUS;
        }
    }
    
    // Reset player
    state.player.x = WORLD_WIDTH / 2;
    state.player.y = WORLD_HEIGHT / 2;
    state.camera.x = state.player.x * TILE_SIZE;
    state.camera.y = state.player.y * TILE_SIZE;
    
    // Spawn mobs
    state.mobs = [];
    for (let i = 0; i < 20; i++) {
        spawnMob();
    }
    
    // Generate clouds
    state.clouds = [];
    for (let i = 0; i < 15; i++) {
        state.clouds.push({
            x: Math.random() * WORLD_WIDTH * TILE_SIZE,
            y: Math.random() * WORLD_HEIGHT * TILE_SIZE * 0.3,
            w: 60 + Math.random() * 100,
            h: 20 + Math.random() * 30,
            speed: 0.2 + Math.random() * 0.3
        });
    }
    
    showNotification("New world generated!");
}

function spawnMob() {
    let x, y, attempts = 0;
    do {
        x = Math.floor(Math.random() * WORLD_WIDTH);
        y = Math.floor(Math.random() * WORLD_HEIGHT);
        attempts++;
    } while (attempts < 100 && isSolid(state.world[y * WORLD_WIDTH + x]));
    
    if (attempts < 100) {
        const types = ['rabbit', 'bird', 'butterfly', 'fish'];
        const type = types[Math.floor(Math.random() * types.length)];
        state.mobs.push({
            x, y,
            type,
            dir: Math.random() * Math.PI * 2,
            speed: 0.02 + Math.random() * 0.03,
            frame: 0,
            hop: 0
        });
    }
}

function isSolid(tile) {
    return tile === TILES.DEEP_WATER || tile === TILES.WATER || 
           tile === TILES.STONE || tile === TILES.DEEP_STONE || 
           tile === TILES.CAVE || tile === TILES.BARK || tile === TILES.LEAVES;
}

function clearWorld() {
    state.world.fill(TILES.VOID);
    state.mobs = [];
    state.particles = [];
    state.player.x = WORLD_WIDTH / 2;
    state.player.y = WORLD_HEIGHT / 2;
    state.camera.x = state.player.x * TILE_SIZE;
    state.camera.y = state.player.y * TILE_SIZE;
    showNotification("Canvas cleared");
}

// ==================== RENDERING ====================
function getTileColor(tile, x, y) {
    let color = TILE_COLORS[tile] || "#ff00ff";
    
    // Day/night tint
    if (state.dayNightEnabled) {
        const hour = state.time / 60;
        let brightness = 1;
        if (hour < 5 || hour > 21) brightness = 0.3;
        else if (hour < 7) brightness = 0.3 + (hour - 5) / 2 * 0.7;
        else if (hour > 19) brightness = 1 - (hour - 19) / 2 * 0.7;
        
        const r = parseInt(color.slice(1, 3), 16);
        const g = parseInt(color.slice(3, 5), 16);
        const b = parseInt(color.slice(5, 7), 16);
        color = `rgb(${Math.floor(r * brightness)}, ${Math.floor(g * brightness)}, ${Math.floor(b * brightness)})`;
    }
    
    return color;
}

function render() {
    // Background
    ctx.fillStyle = state.dayNightEnabled && (state.time < 5 * 60 || state.time > 21 * 60) ? "#0a0a15" : "#87CEEB";
    ctx.fillRect(0, 0, canvas.width, canvas.height);
    
    // Camera transform
    const scale = state.camera.zoom;
    const offsetX = canvas.width / 2 - state.camera.x * scale;
    const offsetY = canvas.height / 2 - state.camera.y * scale;
    
    ctx.save();
    ctx.translate(offsetX, offsetY);
    ctx.scale(scale, scale);
    
    // Visible range
    const startX = Math.max(0, Math.floor(-offsetX / scale / TILE_SIZE) - 1);
    const endX = Math.min(WORLD_WIDTH, Math.ceil((canvas.width - offsetX) / scale / TILE_SIZE) + 1);
    const startY = Math.max(0, Math.floor(-offsetY / scale / TILE_SIZE) - 1);
    const endY = Math.min(WORLD_HEIGHT, Math.ceil((canvas.height - offsetY) / scale / TILE_SIZE) + 1);
    
    // Draw tiles
    for (let y = startY; y < endY; y++) {
        for (let x = startX; x < endX; x++) {
            const tile = state.world[y * WORLD_WIDTH + x];
            ctx.fillStyle = getTileColor(tile, x, y);
            ctx.fillRect(x * TILE_SIZE, y * TILE_SIZE, TILE_SIZE + 0.5, TILE_SIZE + 0.5);
            
            // Subtle grid for void/blank canvas
            if (tile === TILES.VOID) {
                ctx.fillStyle = "rgba(255,255,255,0.03)";
                if ((x + y) % 2 === 0) {
                    ctx.fillRect(x * TILE_SIZE, y * TILE_SIZE, TILE_SIZE, TILE_SIZE);
                }
            }
        }
    }
    
    // Draw mobs
    if (state.mobsEnabled) {
        state.mobs.forEach(mob => {
            const px = mob.x * TILE_SIZE + TILE_SIZE / 2;
            const py = mob.y * TILE_SIZE + TILE_SIZE / 2;
            
            ctx.save();
            ctx.translate(px, py);
            
            if (mob.type === 'rabbit') {
                ctx.fillStyle = "#d4a574";
                ctx.fillRect(-4, -4, 8, 8);
                ctx.fillStyle = "#f5f5dc";
                ctx.fillRect(-2, -6, 2, 4);
                ctx.fillRect(2, -6, 2, 4);
            } else if (mob.type === 'bird') {
                ctx.fillStyle = "#e74c3c";
                ctx.beginPath();
                ctx.arc(0, 0, 4, 0, Math.PI * 2);
                ctx.fill();
                ctx.fillStyle = "#fff";
                ctx.fillRect(-2, -2, 2, 2);
                // Wings
                ctx.fillStyle = "#c0392b";
                const wingY = Math.sin(mob.frame * 0.3) * 3;
                ctx.fillRect(-6, wingY - 2, 4, 3);
                ctx.fillRect(2, wingY - 2, 4, 3);
            } else if (mob.type === 'butterfly') {
                const colors = ['#e74c3c', '#f1c40f', '#9b59b6', '#3498db'];
                ctx.fillStyle = colors[Math.floor(mob.frame) % colors.length];
                ctx.beginPath();
                ctx.ellipse(-3, 0, 4, 3, Math.PI / 4, 0, Math.PI * 2);
                ctx.fill();
                ctx.beginPath();
                ctx.ellipse(3, 0, 4, 3, -Math.PI / 4, 0, Math.PI * 2);
                ctx.fill();
                ctx.fillStyle = "#333";
                ctx.fillRect(-1, -4, 2, 8);
            } else if (mob.type === 'fish') {
                ctx.fillStyle = "#3498db";
                ctx.beginPath();
                ctx.ellipse(0, 0, 5, 3, 0, 0, Math.PI * 2);
                ctx.fill();
                ctx.fillStyle = "#2980b9";
                ctx.beginPath();
                ctx.moveTo(3, 0);
                ctx.lineTo(7, -3);
                ctx.lineTo(7, 3);
                ctx.fill();
            }
            
            ctx.restore();
        });
    }
    
    // Draw player
    if (state.playerEnabled) {
        const px = state.player.x * TILE_SIZE + TILE_SIZE / 2;
        const py = state.player.y * TILE_SIZE + TILE_SIZE / 2;
        
        ctx.save();
        ctx.translate(px, py);
        
        // Body
        ctx.fillStyle = "#3498db";
        ctx.fillRect(-5, -5, 10, 10);
        // Head
        ctx.fillStyle = "#f5cba7";
        ctx.fillRect(-3, -10, 6, 5);
        // Eyes
        ctx.fillStyle = "#2c3e50";
        ctx.fillRect(-2, -8, 1.5, 1.5);
        ctx.fillRect(1, -8, 1.5, 1.5);
        // Direction indicator
        ctx.fillStyle = "#2980b9";
        if (state.player.dir === 0) ctx.fillRect(-1, 5, 2, 3); // down
        else if (state.player.dir === 1) ctx.fillRect(5, -1, 3, 2); // right
        else if (state.player.dir === 2) ctx.fillRect(-1, -8, 2, 3); // up
        else ctx.fillRect(-8, -1, 3, 2); // left
        
        ctx.restore();
    }
    
    // Weather particles
    if (state.weatherEnabled && state.weatherIntensity > 0) {
        state.particles.forEach(p => {
            ctx.globalAlpha = p.alpha;
            if (state.weather === 'rain') {
                ctx.strokeStyle = "#aaddff";
                ctx.lineWidth = 1;
                ctx.beginPath();
                ctx.moveTo(p.x, p.y);
                ctx.lineTo(p.x - 2, p.y + 8);
                ctx.stroke();
            } else if (state.weather === 'snow') {
                ctx.fillStyle = "#fff";
                ctx.beginPath();
                ctx.arc(p.x, p.y, p.size, 0, Math.PI * 2);
                ctx.fill();
            }
        });
        ctx.globalAlpha = 1;
    }
    
    ctx.restore();
    
    // Clouds (parallax, drawn in screen space)
    if (state.dayNightEnabled) {
        const hour = state.time / 60;
        if (hour > 5 && hour < 20) {
            ctx.save();
            state.clouds.forEach(cloud => {
                const cx = (cloud.x - state.camera.x * 0.3) % (canvas.width + cloud.w) - cloud.w / 2;
                const cy = cloud.y - state.camera.y * 0.1;
                ctx.fillStyle = "rgba(255, 255, 255, 0.4)";
                ctx.beginPath();
                ctx.ellipse(cx, cy, cloud.w / 2, cloud.h / 2, 0, 0, Math.PI * 2);
                ctx.fill();
            });
            ctx.restore();
        }
    }
    
    // Night overlay
    if (state.dayNightEnabled) {
        const hour = state.time / 60;
        let darkness = 0;
        if (hour < 5 || hour > 21) darkness = 0.5;
        else if (hour < 7) darkness = 0.5 - (hour - 5) / 2 * 0.5;
        else if (hour > 19) darkness = (hour - 19) / 2 * 0.5;
        
        if (darkness > 0) {
            ctx.fillStyle = `rgba(10, 10, 30, ${darkness})`;
            ctx.fillRect(0, 0, canvas.width, canvas.height);
        }
    }
    
    // Minimap
    renderMinimap();
}

function renderMinimap() {
    const mmw = minimapCanvas.width;
    const mmh = minimapCanvas.height;
    const scaleX = mmw / WORLD_WIDTH;
    const scaleY = mmh / WORLD_HEIGHT;
    
    minimapCtx.clearRect(0, 0, mmw, mmh);
    
    for (let y = 0; y < WORLD_HEIGHT; y++) {
        for (let x = 0; x < WORLD_WIDTH; x++) {
            const tile = state.world[y * WORLD_WIDTH + x];
            if (tile !== TILES.VOID) {
                minimapCtx.fillStyle = TILE_COLORS[tile];
                minimapCtx.fillRect(x * scaleX, y * scaleY, scaleX + 0.5, scaleY + 0.5);
            }
        }
    }
    
    // Viewport rect
    const vw = canvas.width / TILE_SIZE / state.camera.zoom;
    const vh = canvas.height / TILE_SIZE / state.camera.zoom;
    const vx = (state.camera.x / TILE_SIZE) - vw / 2;
    const vy = (state.camera.y / TILE_SIZE) - vh / 2;
    
    minimapCtx.strokeStyle = "#fff";
    minimapCtx.lineWidth = 1;
    minimapCtx.strokeRect(vx * scaleX, vy * scaleY, vw * scaleX, vh * scaleY);
    
    // Player dot
    if (state.playerEnabled) {
        minimapCtx.fillStyle = "#ff0";
        minimapCtx.fillRect(state.player.x * scaleX - 1, state.player.y * scaleY - 1, 3, 3);
    }
}

// ==================== UPDATE ====================
function update(dt) {
    // Time
    if (state.dayNightEnabled) {
        state.time += dt * 0.5; // 2 real seconds = 1 game minute
        if (state.time >= 24 * 60) {
            state.time = 0;
            state.day++;
        }
    }
    
    // Weather
    if (state.weatherEnabled) {
        if (Math.random() < 0.001) {
            state.weather = Math.random() < 0.5 ? 'rain' : 'snow';
            state.weatherIntensity = 0.5 + Math.random() * 0.5;
        }
        
        // Spawn particles
        const particleCount = Math.floor(state.weatherIntensity * 5);
        for (let i = 0; i < particleCount; i++) {
            state.particles.push({
                x: state.camera.x - canvas.width / 2 / state.camera.zoom + Math.random() * canvas.width / state.camera.zoom,
                y: state.camera.y - canvas.height / 2 / state.camera.zoom - 20,
                vx: (Math.random() - 0.5) * 2,
                vy: state.weather === 'rain' ? 8 + Math.random() * 4 : 1 + Math.random() * 2,
                alpha: 0.3 + Math.random() * 0.5,
                size: state.weather === 'snow' ? 1 + Math.random() * 2 : 0,
                life: 100
            });
        }
        
        // Update particles
        state.particles = state.particles.filter(p => {
            p.x += p.vx;
            p.y += p.vy;
            p.life--;
            return p.life > 0;
        });
    } else {
        state.particles = [];
        state.weatherIntensity = Math.max(0, state.weatherIntensity - dt * 0.01);
    }
    
    // Player movement
    if (state.playerEnabled && state.mode === 'explore') {
        let dx = 0, dy = 0;
        if (state.keys['ArrowUp'] || state.keys['w']) dy = -1;
        if (state.keys['ArrowDown'] || state.keys['s']) dy = 1;
        if (state.keys['ArrowLeft'] || state.keys['a']) dx = -1;
        if (state.keys['ArrowRight'] || state.keys['d']) dx = 1;
        
        if (dx !== 0 || dy !== 0) {
            const speed = 0.08 * dt;
            const newX = state.player.x + dx * speed;
            const newY = state.player.y + dy * speed;
            
            if (!isSolid(state.world[Math.floor(newY) * WORLD_WIDTH + Math.floor(newX)])) {
                state.player.x = newX;
                state.player.y = newY;
            }
            
            if (dx > 0) state.player.dir = 1;
            else if (dx < 0) state.player.dir = 3;
            else if (dy > 0) state.player.dir = 0;
            else if (dy < 0) state.player.dir = 2;
            
            state.player.frame += dt * 0.1;
        }
        
        // Camera follow
        state.camera.x += (state.player.x * TILE_SIZE - state.camera.x) * 0.1;
        state.camera.y += (state.player.y * TILE_SIZE - state.camera.y) * 0.1;
    }
    
    // Mob AI
    if (state.mobsEnabled) {
        state.mobs.forEach(mob => {
            mob.frame += dt * 0.05;
            
            if (Math.random() < 0.02) {
                mob.dir += (Math.random() - 0.5) * 2;
            }
            
            const speed = mob.speed * dt;
            const newX = mob.x + Math.cos(mob.dir) * speed;
            const newY = mob.y + Math.sin(mob.dir) * speed;
            
            if (newX > 1 && newX < WORLD_WIDTH - 1 && newY > 1 && newY < WORLD_HEIGHT - 1) {
                const tile = state.world[Math.floor(newY) * WORLD_WIDTH + Math.floor(newX)];
                if (!isSolid(tile)) {
                    mob.x = newX;
                    mob.y = newY;
                } else {
                    mob.dir += Math.PI;
                }
            } else {
                mob.dir += Math.PI;
            }
        });
    }
    
    // Clouds
    state.clouds.forEach(cloud => {
        cloud.x += cloud.speed * dt;
        if (cloud.x > WORLD_WIDTH * TILE_SIZE + 200) {
            cloud.x = -200;
        }
    });
    
    // Update info
    document.getElementById('coords').textContent = 
        `${Math.floor(state.camera.x / TILE_SIZE)}, ${Math.floor(state.camera.y / TILE_SIZE)}`;
    const hours = Math.floor(state.time / 60);
    const mins = Math.floor(state.time % 60);
    document.getElementById('time-display').textContent = 
        `Day ${state.day} — ${hours.toString().padStart(2, '0')}:${mins.toString().padStart(2, '0')}`;
}

// ==================== INPUT HANDLING ====================
function getWorldPos(clientX, clientY) {
    const rect = canvas.getBoundingClientRect();
    const scale = state.camera.zoom;
    const x = (clientX - rect.left - canvas.width / 2) / scale + state.camera.x;
    const y = (clientY - rect.top - canvas.height / 2) / scale + state.camera.y;
    return { x: Math.floor(x / TILE_SIZE), y: Math.floor(y / TILE_SIZE) };
}

function paintAt(gx, gy) {
    if (gx < 0 || gx >= WORLD_WIDTH || gy < 0 || gy >= WORLD_HEIGHT) return;
    const half = Math.floor(state.brushSize / 2);
    for (let dy = -half; dy <= half; dy++) {
        for (let dx = -half; dx <= half; dx++) {
            const nx = gx + dx, ny = gy + dy;
            if (nx >= 0 && nx < WORLD_WIDTH && ny >= 0 && ny < WORLD_HEIGHT) {
                state.world[ny * WORLD_WIDTH + nx] = state.selectedColor;
            }
        }
    }
}

// Mouse
let isMouseDown = false;
canvas.addEventListener('mousedown', e => {
    isMouseDown = true;
    if (state.mode === 'paint') {
        const pos = getWorldPos(e.clientX, e.clientY);
        paintAt(pos.x, pos.y);
        state.lastDrawPos = pos;
    }
});

canvas.addEventListener('mousemove', e => {
    if (isMouseDown && state.mode === 'paint') {
        const pos = getWorldPos(e.clientX, e.clientY);
        if (!state.lastDrawPos || state.lastDrawPos.x !== pos.x || state.lastDrawPos.y !== pos.y) {
            paintAt(pos.x, pos.y);
            state.lastDrawPos = pos;
        }
    }
});

window.addEventListener('mouseup', () => {
    isMouseDown = false;
    state.lastDrawPos = null;
});

// Touch
canvas.addEventListener('touchstart', e => {
    e.preventDefault();
    if (e.touches.length === 1) {
        const touch = e.touches[0];
        state.touchStart = { x: touch.clientX, y: touch.clientY, time: Date.now() };
        state.touchLast = { x: touch.clientX, y: touch.clientY };
        
        if (state.mode === 'paint') {
            const pos = getWorldPos(touch.clientX, touch.clientY);
            paintAt(pos.x, pos.y);
            state.lastDrawPos = pos;
        }
    } else if (e.touches.length === 2) {
        const dx = e.touches[0].clientX - e.touches[1].clientX;
        const dy = e.touches[0].clientY - e.touches[1].clientY;
        state.pinchStartDist = Math.sqrt(dx * dx + dy * dy);
        state.pinchStartZoom = state.camera.zoom;
    }
}, { passive: false });

canvas.addEventListener('touchmove', e => {
    e.preventDefault();
    if (e.touches.length === 1 && state.touchStart) {
        const touch = e.touches[0];
        
        if (state.mode === 'paint') {
            const pos = getWorldPos(touch.clientX, touch.clientY);
            if (!state.lastDrawPos || state.lastDrawPos.x !== pos.x || state.lastDrawPos.y !== pos.y) {
                paintAt(pos.x, pos.y);
                state.lastDrawPos = pos;
            }
        } else if (state.mode === 'explore') {
            const dx = touch.clientX - state.touchLast.x;
            const dy = touch.clientY - state.touchLast.y;
            state.camera.x -= dx / state.camera.zoom;
            state.camera.y -= dy / state.camera.zoom;
            state.touchLast = { x: touch.clientX, y: touch.clientY };
        }
    } else if (e.touches.length === 2) {
        const dx = e.touches[0].clientX - e.touches[1].clientX;
        const dy = e.touches[0].clientY - e.touches[1].clientY;
        const dist = Math.sqrt(dx * dx + dy * dy);
        const scale = dist / state.pinchStartDist;
        state.camera.zoom = Math.max(0.5, Math.min(5, state.pinchStartZoom * scale));
    }
}, { passive: false });

canvas.addEventListener('touchend', e => {
    e.preventDefault();
    if (e.touches.length === 0) {
        state.touchStart = null;
        state.touchLast = null;
        state.lastDrawPos = null;
    }
});

// Wheel zoom
canvas.addEventListener('wheel', e => {
    e.preventDefault();
    const delta = e.deltaY > 0 ? 0.9 : 1.1;
    state.camera.zoom = Math.max(0.5, Math.min(5, state.camera.zoom * delta));
}, { passive: false });

// Keyboard
window.addEventListener('keydown', e => {
    state.keys[e.key] = true;
    if (e.key === ' ' && state.mode === 'explore') {
        state.camera.x = state.player.x * TILE_SIZE;
        state.camera.y = state.player.y * TILE_SIZE;
    }
});
window.addEventListener('keyup', e => state.keys[e.key] = false);

// ==================== UI CONTROLS ====================
function buildPalette() {
    const scroll = document.getElementById('palette-scroll');
    scroll.innerHTML = '';
    
    // Eraser
    const eraser = document.createElement('div');
    eraser.className = 'color-swatch eraser' + (state.selectedColor === TILES.VOID ? ' active' : '');
    eraser.title = 'Eraser (Void)';
    eraser.onclick = () => selectColor(TILES.VOID);
    scroll.appendChild(eraser);
    
    ALL_COLORS.forEach((c, i) => {
        const swatch = document.createElement('div');
        swatch.className = 'color-swatch' + (state.selectedColor === i ? ' active' : '');
        swatch.style.backgroundColor = c.color;
        swatch.title = c.name;
        swatch.onclick = () => selectColor(i);
        scroll.appendChild(swatch);
    });
}

function selectColor(index) {
    state.selectedColor = index;
    document.querySelectorAll('.color-swatch').forEach((s, i) => {
        s.classList.toggle('active', i === (index === TILES.VOID ? 0 : index + 1));
    });
}

buildPalette();

// Brush size
const brushSlider = document.getElementById('brush-slider');
const brushLabel = document.getElementById('brush-size-label');
brushSlider.addEventListener('input', e => {
    state.brushSize = parseInt(e.target.value);
    brushLabel.textContent = state.brushSize + 'px';
});

// Mode buttons
document.getElementById('mode-paint').addEventListener('click', function() {
    state.mode = 'paint';
    document.getElementById('mode-paint').classList.add('active');
    document.getElementById('mode-explore').classList.remove('active');
});

document.getElementById('mode-explore').addEventListener('click', function() {
    state.mode = 'explore';
    document.getElementById('mode-explore').classList.add('active');
    document.getElementById('mode-paint').classList.remove('active');
});

// World buttons
document.getElementById('btn-generate').addEventListener('click', generateWorld);
document.getElementById('btn-clear').addEventListener('click', clearWorld);

// Toggles
function setupToggle(id, stateKey) {
    const el = document.getElementById(id);
    el.addEventListener('click', () => {
        state[stateKey] = !state[stateKey];
        el.classList.toggle('on', state[stateKey]);
    });
}

setupToggle('toggle-player', 'playerEnabled');
setupToggle('toggle-mobs', 'mobsEnabled');
setupToggle('toggle-daynight', 'dayNightEnabled');
setupToggle('toggle-weather', 'weatherEnabled');

// Save/Load
function saveWorld() {
    const data = {
        version: 1,
        world: Array.from(state.world),
        width: WORLD_WIDTH,
        height: WORLD_HEIGHT,
        player: state.player,
        mobs: state.mobs,
        time: state.time,
        day: state.day
    };
    const blob = new Blob([JSON.stringify(data)], { type: 'application/json' });
    const url = URL.createObjectURL(blob);
    const a = document.createElement('a');
    a.href = url;
    a.download = `pixel-sandbox-${Date.now()}.json`;
    a.click();
    URL.revokeObjectURL(url);
    showNotification("World saved!");
}

function loadWorld(file) {
    const reader = new FileReader();
    reader.onload = e => {
        try {
            const data = JSON.parse(e.target.result);
            if (data.world && data.width === WORLD_WIDTH && data.height === WORLD_HEIGHT) {
                state.world.set(new Uint8Array(data.world));
                if (data.player) state.player = data.player;
                if (data.mobs) state.mobs = data.mobs;
                if (data.time) state.time = data.time;
                if (data.day) state.day = data.day;
                showNotification("World loaded!");
            } else {
                showNotification("Invalid world file!");
            }
        } catch (err) {
            showNotification("Error loading file!");
        }
    };
    reader.readAsText(file);
}

document.getElementById('btn-save').addEventListener('click', saveWorld);
document.getElementById('btn-load').addEventListener('click', () => document.getElementById('load-input').click());
document.getElementById('load-input').addEventListener('change', e => {
    if (e.target.files[0]) loadWorld(e.target.files[0]);
    e.target.value = '';
});

// Mobile controls
document.getElementById('zoom-in').addEventListener('click', () => {
    state.camera.zoom = Math.min(5, state.camera.zoom * 1.2);
});
document.getElementById('zoom-out').addEventListener('click', () => {
    state.camera.zoom = Math.max(0.5, state.camera.zoom / 1.2);
});

const dpadKeys = {
    'dpad-up': 'ArrowUp',
    'dpad-down': 'ArrowDown',
    'dpad-left': 'ArrowLeft',
    'dpad-right': 'ArrowRight'
};

Object.entries(dpadKeys).forEach(([id, key]) => {
    const btn = document.getElementById(id);
    btn.addEventListener('touchstart', e => { e.preventDefault(); state.keys[key] = true; });
    btn.addEventListener('touchend', e => { e.preventDefault(); state.keys[key] = false; });
    btn.addEventListener('mousedown', e => { state.keys[key] = true; });
    btn.addEventListener('mouseup', e => { state.keys[key] = false; });
    btn.addEventListener('mouseleave', e => { state.keys[key] = false; });
});

// Notification
function showNotification(text) {
    const el = document.getElementById('notification');
    el.textContent = text;
    el.classList.add('show');
    setTimeout(() => el.classList.remove('show'), 2000);
}

// ==================== GAME LOOP ====================
let lastTime = 0;
function gameLoop(timestamp) {
    const dt = Math.min(timestamp - lastTime, 50);
    lastTime = timestamp;
    
    update(dt);
    render();
    
    requestAnimationFrame(gameLoop);
}

// Initialize
generateWorld();
requestAnimationFrame(gameLoop);

// Prevent context menu on canvas
canvas.addEventListener('contextmenu', e => e.preventDefault());
</script>
</body>
</html>'''
