<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no">
    <title>Pixel Sandbox Deluxe</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            -webkit-user-select: none;
            user-select: none;
            -webkit-tap-highlight-color: transparent;
        }

body {
            background: #1a1a2e;
            overflow: hidden;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            width: 100vw;
            height: 100vh;
            display: flex;
            justify-content: center;
            align-items: center;
        }

#gameContainer {
            position: relative;
            width: 100%;
            height: 100%;
            display: flex;
            justify-content: center;
            align-items: center;
        }

#gameCanvas {
            display: block;
            image-rendering: pixelated;
            image-rendering: crisp-edges;
        }

#minimapContainer {
            position: fixed;
            top: 55px;
            right: 10px;
            width: 150px;
            height: 150px;
            background: rgba(0, 0, 0, 0.8);
            border: 2px solid #4a4a6a;
            border-radius: 8px;
            z-index: 100;
            overflow: hidden;
        }

#minimapCanvas {
            width: 100%;
            height: 100%;
            image-rendering: pixelated;
        }

#mainMenu {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: linear-gradient(135deg, #1a1a2e 0%, #16213e 50%, #0f3460 100%);
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            z-index: 1000;
            gap: 15px;
        }

#mainMenu h1 {
            font-size: 48px;
            color: #e94560;
            text-shadow: 3px 3px 0 #533483;
            margin-bottom: 20px;
            letter-spacing: 2px;
        }

.menu-btn {
            padding: 15px 40px;
            font-size: 20px;
            border: none;
            border-radius: 10px;
            cursor: pointer;
            transition: all 0.2s;
            font-weight: bold;
            min-width: 280px;
        }

.menu-btn:hover {
            transform: scale(1.05);
        }

.btn-primary {
            background: #e94560;
            color: white;
        }

.btn-primary:hover {
            background: #ff6b6b;
        }

.btn-secondary {
            background: #533483;
            color: white;
        }

.btn-secondary:hover {
            background: #7b68ee;
        }

.btn-tertiary {
            background: #0f3460;
            color: #a0d2eb;
            border: 2px solid #533483;
        }

.btn-tertiary:hover {
            background: #1a4a7a;
        }

#hud {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            pointer-events: none;
            z-index: 50;
        }

#hud > * {
            pointer-events: auto;
        }

#topBar {
            position: fixed;
            top: 0;
            left: 0;
            right: 0;
            height: 50px;
            background: rgba(0, 0, 0, 0.7);
            display: flex;
            align-items: center;
            justify-content: space-between;
            padding: 0 15px;
            z-index: 100;
        }

#topBarLeft {
            display: flex;
            gap: 10px;
            align-items: center;
        }

        .hud-btn {
            padding: 8px 16px;
            background: #533483;
            color: white;
            border: none;
            border-radius: 6px;
            cursor: pointer;
            font-size: 14px;
            font-weight: bold;
        }

        .hud-btn:hover {
            background: #7b68ee;
        }

        .hud-btn.active {
            background: #e94560;
        }

        #topBarRight {
            display: flex;
            gap: 10px;
            align-items: center;
        }

        #inventoryBar {
            position: fixed;
            bottom: 10px;
            left: 50%;
            transform: translateX(-50%);
            display: flex;
            gap: 5px;
            background: rgba(0, 0, 0, 0.8);
            padding: 8px;
            border-radius: 10px;
            z-index: 100;
        }

        .inv-slot {
            width: 55px;
            height: 55px;
            background: rgba(255, 255, 255, 0.1);
            border: 2px solid #4a4a6a;
            border-radius: 6px;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            cursor: pointer;
            position: relative;
        }

        .inv-slot.selected {
            border-color: #e94560;
            box-shadow: 0 0 10px rgba(233, 69, 96, 0.5);
        }

        .inv-slot .tile-preview {
            width: 30px;
            height: 30px;
            image-rendering: pixelated;
            border-radius: 3px;
        }

        .inv-slot .slot-count {
            position: absolute;
            bottom: 2px;
            right: 4px;
            font-size: 11px;
            color: white;
            font-weight: bold;
            text-shadow: 1px 1px 0 black;
        }

        .inv-slot .slot-key {
            position: absolute;
            top: 2px;
            left: 4px;
            font-size: 10px;
            color: #aaa;
        }

        #colorMenu {
            position: fixed;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            width: 90%;
            max-width: 720px;
            max-height: 85vh;
            background: rgba(20, 20, 40, 0.97);
            border: 3px solid #533483;
            border-radius: 15px;
            z-index: 200;
            display: none;
            flex-direction: column;
            padding: 20px;
        }

        #colorMenuHeader {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 15px;
        }

        #colorMenuHeader h2 {
            color: #e94560;
            font-size: 24px;
        }

        .close-btn {
            background: #e94560;
            color: white;
            border: none;
            width: 35px;
            height: 35px;
            border-radius: 50%;
            cursor: pointer;
            font-size: 18px;
            font-weight: bold;
        }

        #colorCategories {
            display: flex;
            gap: 8px;
            margin-bottom: 15px;
            flex-wrap: wrap;
        }

        .color-cat-btn {
            padding: 8px 16px;
            background: #533483;
            color: white;
            border: none;
            border-radius: 20px;
            cursor: pointer;
            font-size: 13px;
        }

        .color-cat-btn.active {
            background: #e94560;
        }

        #colorGrid {
            display: grid;
            grid-template-columns: repeat(auto-fill, minmax(38px, 1fr));
            gap: 5px;
            overflow-y: auto;
            max-height: 55vh;
            padding: 10px;
            background: rgba(0, 0, 0, 0.3);
            border-radius: 10px;
        }

        .color-swatch {
            width: 38px;
            height: 38px;
            border-radius: 6px;
            cursor: pointer;
            border: 2px solid transparent;
            transition: transform 0.1s;
        }

        .color-swatch:hover {
            transform: scale(1.2);
            border-color: white;
            z-index: 10;
        }

        .color-swatch.selected {
            border-color: #e94560;
            box-shadow: 0 0 8px #e94560;
        }

        #settingsMenu {
            position: fixed;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            width: 90%;
            max-width: 500px;
            background: rgba(20, 20, 40, 0.97);
            border: 3px solid #533483;
            border-radius: 15px;
            z-index: 200;
            display: none;
            flex-direction: column;
            padding: 20px;
        }

        #settingsMenu h2 {
            color: #e94560;
            font-size: 24px;
            margin-bottom: 20px;
            text-align: center;
        }

        .setting-row {
            display: flex;
            justify-content: space-between;
            align-items: center;
            padding: 12px 0;
            border-bottom: 1px solid rgba(255, 255, 255, 0.1);
        }

        .setting-row label {
            color: #a0d2eb;
            font-size: 16px;
        }

        .toggle-switch {
            width: 50px;
            height: 26px;
            background: #4a4a6a;
            border-radius: 13px;
            position: relative;
            cursor: pointer;
            transition: background 0.3s;
        }

        .toggle-switch.active {
            background: #e94560;
        }

        .toggle-switch::after {
            content: '';
            position: absolute;
            width: 22px;
            height: 22px;
            background: white;
            border-radius: 50%;
            top: 2px;
            left: 2px;
            transition: left 0.3s;
        }

        .toggle-switch.active::after {
            left: 26px;
        }

        #mobileControls {
            position: fixed;
            bottom: 80px;
            left: 20px;
            z-index: 100;
            display: none;
        }

        .d-pad {
            position: relative;
            width: 150px;
            height: 150px;
        }

        .d-btn {
            position: absolute;
            width: 50px;
            height: 50px;
            background: rgba(255, 255, 255, 0.2);
            border: 2px solid rgba(255, 255, 255, 0.4);
            border-radius: 10px;
            display: flex;
            align-items: center;
            justify-content: center;
            color: white;
            font-size: 24px;
            font-weight: bold;
        }

        .d-btn:active {
            background: rgba(233, 69, 96, 0.5);
        }

        .d-up { top: 0; left: 50px; }
        .d-down { bottom: 0; left: 50px; }
        .d-left { top: 50px; left: 0; }
        .d-right { top: 50px; right: 0; }

        #mobileActions {
            position: fixed;
            bottom: 80px;
            right: 20px;
            z-index: 100;
            display: none;
            flex-direction: column;
            gap: 10px;
        }

        .mobile-btn {
            width: 60px;
            height: 60px;
            background: rgba(255, 255, 255, 0.2);
            border: 2px solid rgba(255, 255, 255, 0.4);
            border-radius: 50%;
            display: flex;
            align-items: center;
            justify-content: center;
            color: white;
            font-size: 14px;
            font-weight: bold;
        }

        .mobile-btn:active {
            background: rgba(233, 69, 96, 0.5);
        }

        #notification {
            position: fixed;
            top: 60px;
            left: 50%;
            transform: translateX(-50%);
            background: rgba(0, 0, 0, 0.8);
            color: white;
            padding: 10px 25px;
            border-radius: 20px;
            font-size: 14px;
            z-index: 300;
            display: none;
            border: 1px solid #533483;
        }

        #fileInput {
            display: none;
        }

        @media (max-width: 768px) {
            #mobileControls, #mobileActions {
                display: flex;
            }
            
            #inventoryBar {
                bottom: 5px;
                padding: 5px;
            }
            
            .inv-slot {
                width: 42px;
                height: 42px;
            }
            
            .inv-slot .tile-preview {
                width: 24px;
                height: 24px;
            }
            
            #minimapContainer {
                width: 100px;
                height: 100px;
                top: 55px;
            }
            
            .hud-btn {
                padding: 6px 10px;
                font-size: 12px;
            }
        }

        .tooltip {
            position: fixed;
            background: rgba(0, 0, 0, 0.9);
            color: white;
            padding: 5px 10px;
            border-radius: 5px;
            font-size: 12px;
            pointer-events: none;
            z-index: 500;
            display: none;
            border: 1px solid #533483;
        }

        #brushSizeIndicator {
            position: fixed;
            bottom: 75px;
            left: 50%;
            transform: translateX(-50%);
            background: rgba(0, 0, 0, 0.7);
            color: white;
            padding: 5px 15px;
            border-radius: 15px;
            font-size: 14px;
            z-index: 100;
            display: none;
        }

        #caveOverlay {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: rgba(0, 0, 0, 0);
            pointer-events: none;
            z-index: 40;
            transition: background 0.5s;
        }
    </style>
</head>
<body>
    <div id="gameContainer">
        <canvas id="gameCanvas"></canvas>
    </div>

    <div id="minimapContainer">
        <canvas id="minimapCanvas"></canvas>
    </div>

    <div id="mainMenu">
        <h1>Pixel Sandbox Deluxe</h1>
        <button class="menu-btn btn-primary" onclick="startGame('world')">Explore World</button>
        <button class="menu-btn btn-secondary" onclick="startGame('canvas')">Blank Canvas</button>
        <button class="menu-btn btn-tertiary" onclick="loadWorld()">Load World</button>
        <input type="file" id="fileInput" accept=".json" onchange="handleFileLoad(event)">
    </div>

    <div id="hud" style="display: none;">
        <div id="topBar">
            <div id="topBarLeft">
                <button class="hud-btn" onclick="toggleMenu()">Menu</button>
                <button class="hud-btn" id="playerToggle" onclick="togglePlayer()">Player: ON</button>
                <button class="hud-btn" id="mobsToggle" onclick="toggleMobs()">Mobs: ON</button>
            </div>
            <div id="topBarRight">
                <button class="hud-btn" onclick="toggleColorMenu()">Colors</button>
                <button class="hud-btn" onclick="toggleSettings()">Settings</button>
                <button class="hud-btn" onclick="saveWorld()">Save</button>
            </div>
        </div>
    </div>

    <div id="inventoryBar" style="display: none;"></div>

    <div id="colorMenu">
        <div id="colorMenuHeader">
            <h2>Color Palette</h2>
            <button class="close-btn" onclick="toggleColorMenu()">X</button>
        </div>
        <div id="colorCategories"></div>
        <div id="colorGrid"></div>
    </div>

    <div id="settingsMenu">
        <h2>Settings</h2>
        <div class="setting-row">
            <label>Day/Night Cycle</label>
            <div class="toggle-switch active" id="dayNightToggle" onclick="toggleSetting('dayNight')"></div>
        </div>
        <div class="setting-row">
            <label>Weather Effects</label>
            <div class="toggle-switch" id="weatherToggle" onclick="toggleSetting('weather')"></div>
        </div>
        <div class="setting-row">
            <label>Show Minimap</label>
            <div class="toggle-switch active" id="minimapToggle" onclick="toggleSetting('minimap')"></div>
        </div>
        <div class="setting-row">
            <label>Sound Effects</label>
            <div class="toggle-switch active" id="soundToggle" onclick="toggleSetting('sound')"></div>
        </div>
        <div style="text-align: center; margin-top: 20px;">
            <button class="menu-btn btn-secondary" onclick="toggleSettings()">Close</button>
        </div>
    </div>

    <div id="mobileControls">
        <div class="d-pad">
            <div class="d-btn d-up" ontouchstart="mobileMove('up')" ontouchend="mobileStop()">W</div>
            <div class="d-btn d-down" ontouchstart="mobileMove('down')" ontouchend="mobileStop()">S</div>
            <div class="d-btn d-left" ontouchstart="mobileMove('left')" ontouchend="mobileStop()">A</div>
            <div class="d-btn d-right" ontouchstart="mobileMove('right')" ontouchend="mobileStop()">D</div>
        </div>
    </div>

    <div id="mobileActions">
        <div class="mobile-btn" ontouchstart="mobileAction('place')">Place</div>
        <div class="mobile-btn" ontouchstart="mobileAction('break')">Break</div>
        <div class="mobile-btn" ontouchstart="mobileAction('interact')">Use</div>
    </div>

    <div id="notification"></div>
    <div id="brushSizeIndicator">Brush: 1x1</div>
    <div class="tooltip" id="tooltip"></div>
    <div id="caveOverlay"></div>

    <script>

        const TILE_SIZE = 32;
        const WORLD_WIDTH = 200;
        const WORLD_HEIGHT = 150;
        const CANVAS_WIDTH = 120;
        const CANVAS_HEIGHT = 90;
        const PLAYER_SPEED = 120;
        const MOB_SPEED = 30;
        const GRAVITY = 400;
        const JUMP_FORCE = 200;
        
        // Tile IDs
        const TILES = {
            AIR: 0,
            GRASS: 1,
            DIRT: 2,
            STONE: 3,
            WATER: 4,
            SAND: 5,
            WOOD: 6,
            LEAVES: 7,
            TALL_GRASS: 8,
            FLOWER_RED: 9,
            FLOWER_YELLOW: 10,
            ROCK: 11,
            CAVE_ENTRANCE: 12,
            CAVE_WALL: 13,
            CAVE_FLOOR: 14,
            CAVE_CRYSTAL: 15,
            SNOW: 16,
            ICE: 17,
            LAVA: 18,
            BRICK: 19,
            GLASS: 20,
            METAL: 21,
            GOLD: 22,
            DIAMOND: 23,
            PLANKS: 24,
            WOOL_WHITE: 25,
            WOOL_RED: 26,
            WOOL_BLUE: 27,
            WOOL_GREEN: 28,
            WOOL_YELLOW: 29,
            WOOL_PURPLE: 30,
            WOOL_ORANGE: 31,
            WOOL_PINK: 32,
            WOOL_BLACK: 33,
            WOOL_GRAY: 34,
            WOOL_BROWN: 35,
            WOOL_CYAN: 36,
            WOOL_LIME: 37,
            WOOL_MAGENTA: 38,
            WOOL_LIGHT_BLUE: 39,
            WOOL_LIGHT_GRAY: 40,
            CONCRETE: 41,
            TERRACOTTA: 42,
            OBSIDIAN: 43,
            MOSSY_STONE: 44,
            MOSSY_BRICK: 45,
            SANDSTONE: 46,
            RED_SAND: 47,
            RED_SANDSTONE: 48,
            PODZOL: 49,
            MYCELIUM: 50,
            GRAVEL: 51,
            CLAY: 52,
            HONEYCOMB: 53,
            HONEY_BLOCK: 54,
            SLIME_BLOCK: 55,
            MAGMA_BLOCK: 56,
            SEA_LANTERN: 57,
            GLOWSTONE: 58,
            TORCH: 59,
            LANTERN: 60,
            CAMPFIRE: 61,
            BOOKSHELF: 62,
            CHEST: 63,
            CRAFTING_TABLE: 64,
            FURNACE: 65,
            ANVIL: 66,
            BED_RED: 67,
            BED_BLUE: 68,
            DOOR_WOOD: 69,
            DOOR_IRON: 70,
            TRAPDOOR: 71,
            FENCE: 72,
            FENCE_GATE: 73,
            LADDER: 74,
            VINES: 75,
            LILY_PAD: 76,
            CACTUS: 77,
            SUGAR_CANE: 78,
            BAMBOO: 79,
            KELP: 80,
            CORAL: 81,
            CORAL_FAN: 82,
            SEA_PICKLE: 83,
            SPONGE: 84,
            WET_SPONGE: 85,
            TNT: 86,
            PISTON: 87,
            STICKY_PISTON: 88,
            REDSTONE_BLOCK: 89,
            REDSTONE_TORCH: 90,
            LEVER: 91,
            BUTTON: 92,
            PRESSURE_PLATE: 93,
            RAIL: 94,
            POWERED_RAIL: 95,
            DETECTOR_RAIL: 96,
            ACTIVATOR_RAIL: 97,
            MINECART: 98,
            BOAT: 99,
            SIGN: 100,
            ITEM_FRAME: 101,
            PAINTING: 102,
            FLOWER_POT: 103,
            SAPLING_OAK: 104,
            SAPLING_BIRCH: 105,
            SAPLING_SPRUCE: 106,
            SAPLING_JUNGLE: 107,
            SAPLING_ACACIA: 108,
            SAPLING_DARK_OAK: 109,
            DEAD_BUSH: 110,
            FERN: 111,
            LARGE_FERN: 112,
            GRASS_PATH: 113,
            FARMLAND: 114,
            WHEAT: 115,
            CARROTS: 116,
            POTATOES: 117,
            BEETROOTS: 118,
            MELON: 119,
            PUMPKIN: 120,
            CARVED_PUMPKIN: 121,
            JACK_O_LANTERN: 122,
            HAY_BALE: 123,
            COMPOSTER: 124,
            CAULDRON: 125,
            BREWING_STAND: 126,
            ENCHANTING_TABLE: 127,
            END_PORTAL_FRAME: 128,
            END_STONE: 129,
            END_STONE_BRICK: 130,
            PURPUR_BLOCK: 131,
            PURPUR_PILLAR: 132,
            CHORUS_PLANT: 133,
            CHORUS_FLOWER: 134,
            DRAGON_EGG: 135,
            SHULKER_BOX: 136,
            COMMAND_BLOCK: 137,
            STRUCTURE_BLOCK: 138,
            BARRIER: 139,
            JIGSAW: 140,
            LIGHT_BLOCK: 141,
            RAINBOW: 142,
            TRANSPARENT: 143,
            ERASER: 144,
            CUSTOM_1: 145, CUSTOM_2: 146, CUSTOM_3: 147, CUSTOM_4: 148,
            CUSTOM_5: 149, CUSTOM_6: 150, CUSTOM_7: 151, CUSTOM_8: 152,
            CUSTOM_9: 153, CUSTOM_10: 154, CUSTOM_11: 155, CUSTOM_12: 156,
            CUSTOM_13: 157, CUSTOM_14: 158, CUSTOM_15: 159, CUSTOM_16: 160
        };

        const TILE_COLORS = {
            [TILES.AIR]: null,
            [TILES.GRASS]: '#4a8c3f',
            [TILES.DIRT]: '#8B6914',
            [TILES.STONE]: '#808080',
            [TILES.WATER]: '#3b7bbf',
            [TILES.SAND]: '#e6c288',
            [TILES.WOOD]: '#6b4226',
            [TILES.LEAVES]: '#2d5a1e',
            [TILES.TALL_GRASS]: '#5a9e4a',
            [TILES.FLOWER_RED]: '#e63946',
            [TILES.FLOWER_YELLOW]: '#f4d03f',
            [TILES.ROCK]: '#696969',
            [TILES.CAVE_ENTRANCE]: '#3d3d3d',
            [TILES.CAVE_WALL]: '#4a4a4a',
            [TILES.CAVE_FLOOR]: '#5c5c5c',
            [TILES.CAVE_CRYSTAL]: '#9b59b6',
            [TILES.SNOW]: '#f0f8ff',
            [TILES.ICE]: '#a8d8ea',
            [TILES.LAVA]: '#e74c3c',
            [TILES.BRICK]: '#a0522d',
            [TILES.GLASS]: 'rgba(200, 220, 255, 0.6)',
            [TILES.METAL]: '#708090',
            [TILES.GOLD]: '#ffd700',
            [TILES.DIAMOND]: '#00ced1',
            [TILES.PLANKS]: '#8b7355',
            [TILES.WOOL_WHITE]: '#f5f5f5',
            [TILES.WOOL_RED]: '#dc143c',
            [TILES.WOOL_BLUE]: '#1e90ff',
            [TILES.WOOL_GREEN]: '#228b22',
            [TILES.WOOL_YELLOW]: '#ffd700',
            [TILES.WOOL_PURPLE]: '#9370db',
            [TILES.WOOL_ORANGE]: '#ff8c00',
            [TILES.WOOL_PINK]: '#ff69b4',
            [TILES.WOOL_BLACK]: '#1a1a1a',
            [TILES.WOOL_GRAY]: '#808080',
            [TILES.WOOL_BROWN]: '#8b4513',
            [TILES.WOOL_CYAN]: '#00ced1',
            [TILES.WOOL_LIME]: '#32cd32',
            [TILES.WOOL_MAGENTA]: '#ff00ff',
            [TILES.WOOL_LIGHT_BLUE]: '#87ceeb',
            [TILES.WOOL_LIGHT_GRAY]: '#d3d3d3',
            [TILES.CONCRETE]: '#a9a9a9',
            [TILES.TERRACOTTA]: '#cd5c5c',
            [TILES.OBSIDIAN]: '#1a0a2e',
            [TILES.MOSSY_STONE]: '#6b8e6b',
            [TILES.MOSSY_BRICK]: '#7a9a7a',
            [TILES.SANDSTONE]: '#e6d5a7',
            [TILES.RED_SAND]: '#c2a060',
            [TILES.RED_SANDSTONE]: '#c2a060',
            [TILES.PODZOL]: '#5d4037',
            [TILES.MYCELIUM]: '#8b7d6b',
            [TILES.GRAVEL]: '#a0a0a0',
            [TILES.CLAY]: '#b0c4de',
            [TILES.HONEYCOMB]: '#e6a817',
            [TILES.HONEY_BLOCK]: '#ffb347',
            [TILES.SLIME_BLOCK]: '#7fff00',
            [TILES.MAGMA_BLOCK]: '#8b0000',
            [TILES.SEA_LANTERN]: '#e0ffff',
            [TILES.GLOWSTONE]: '#f0e68c',
            [TILES.TORCH]: '#ffa500',
            [TILES.LANTERN]: '#ffd700',
            [TILES.CAMPFIRE]: '#ff6347',
            [TILES.BOOKSHELF]: '#8b6914',
            [TILES.CHEST]: '#d2691e',
            [TILES.CRAFTING_TABLE]: '#8b6914',
            [TILES.FURNACE]: '#696969',
            [TILES.ANVIL]: '#a9a9a9',
            [TILES.BED_RED]: '#dc143c',
            [TILES.BED_BLUE]: '#1e90ff',
            [TILES.DOOR_WOOD]: '#8b4513',
            [TILES.DOOR_IRON]: '#708090',
            [TILES.TRAPDOOR]: '#8b6914',
            [TILES.FENCE]: '#8b6914',
            [TILES.FENCE_GATE]: '#8b6914',
            [TILES.LADDER]: '#8b6914',
            [TILES.VINES]: '#228b22',
            [TILES.LILY_PAD]: '#228b22',
            [TILES.CACTUS]: '#2e8b57',
            [TILES.SUGAR_CANE]: '#90ee90',
            [TILES.BAMBOO]: '#228b22',
            [TILES.KELP]: '#2e8b57',
            [TILES.CORAL]: '#ff7f50',
            [TILES.CORAL_FAN]: '#ff69b4',
            [TILES.SEA_PICKLE]: '#9acd32',
            [TILES.SPONGE]: '#f0e68c',
            [TILES.WET_SPONGE]: '#bdb76b',
            [TILES.TNT]: '#ff4500',
            [TILES.PISTON]: '#a9a9a9',
            [TILES.STICKY_PISTON]: '#8fbc8f',
            [TILES.REDSTONE_BLOCK]: '#ff0000',
            [TILES.REDSTONE_TORCH]: '#ff4500',
            [TILES.LEVER]: '#8b6914',
            [TILES.BUTTON]: '#8b6914',
            [TILES.PRESSURE_PLATE]: '#8b6914',
            [TILES.RAIL]: '#a9a9a9',
            [TILES.POWERED_RAIL]: '#ff4500',
            [TILES.DETECTOR_RAIL]: '#ff6347',
            [TILES.ACTIVATOR_RAIL]: '#ff4500',
            [TILES.MINECART]: '#708090',
            [TILES.BOAT]: '#8b4513',
            [TILES.SIGN]: '#8b6914',
            [TILES.ITEM_FRAME]: '#8b6914',
            [TILES.PAINTING]: '#deb887',
            [TILES.FLOWER_POT]: '#cd853f',
            [TILES.SAPLING_OAK]: '#228b22',
            [TILES.SAPLING_BIRCH]: '#90ee90',
            [TILES.SAPLING_SPRUCE]: '#006400',
            [TILES.SAPLING_JUNGLE]: '#228b22',
            [TILES.SAPLING_ACACIA]: '#daa520',
            [TILES.SAPLING_DARK_OAK]: '#556b2f',
            [TILES.DEAD_BUSH]: '#8b7355',
            [TILES.FERN]: '#228b22',
            [TILES.LARGE_FERN]: '#228b22',
            [TILES.GRASS_PATH]: '#9acd32',
            [TILES.FARMLAND]: '#8b4513',
            [TILES.WHEAT]: '#ffd700',
            [TILES.CARROTS]: '#ff8c00',
            [TILES.POTATOES]: '#daa520',
            [TILES.BEETROOTS]: '#dc143c',
            [TILES.MELON]: '#228b22',
            [TILES.PUMPKIN]: '#ff8c00',
            [TILES.CARVED_PUMPKIN]: '#ff8c00',
            [TILES.JACK_O_LANTERN]: '#ff8c00',
            [TILES.HAY_BALE]: '#ffd700',
            [TILES.COMPOSTER]: '#8b6914',
            [TILES.CAULDRON]: '#708090',
            [TILES.BREWING_STAND]: '#8b6914',
            [TILES.ENCHANTING_TABLE]: '#4b0082',
            [TILES.END_PORTAL_FRAME]: '#2f4f4f',
            [TILES.END_STONE]: '#f5f5dc',
            [TILES.END_STONE_BRICK]: '#f5f5dc',
            [TILES.PURPUR_BLOCK]: '#dda0dd',
            [TILES.PURPUR_PILLAR]: '#dda0dd',
            [TILES.CHORUS_PLANT]: '#800080',
            [TILES.CHORUS_FLOWER]: '#dda0dd',
            [TILES.DRAGON_EGG]: '#1a0a2e',
            [TILES.SHULKER_BOX]: '#9370db',
            [TILES.COMMAND_BLOCK]: '#ffa500',
            [TILES.STRUCTURE_BLOCK]: '#808080',
            [TILES.BARRIER]: '#ff0000',
            [TILES.JIGSAW]: '#a9a9a9',
            [TILES.LIGHT_BLOCK]: '#ffffe0',
            [TILES.RAINBOW]: null,
            [TILES.TRANSPARENT]: 'rgba(255, 255, 255, 0.15)',
            [TILES.ERASER]: null,
            [TILES.CUSTOM_1]: '#ff6b6b',
            [TILES.CUSTOM_2]: '#4ecdc4',
            [TILES.CUSTOM_3]: '#45b7d1',
            [TILES.CUSTOM_4]: '#96ceb4',
            [TILES.CUSTOM_5]: '#ffeaa7',
            [TILES.CUSTOM_6]: '#dfe6e9',
            [TILES.CUSTOM_7]: '#fd79a8',
            [TILES.CUSTOM_8]: '#a29bfe',
            [TILES.CUSTOM_9]: '#00b894',
            [TILES.CUSTOM_10]: '#e17055',
            [TILES.CUSTOM_11]: '#74b9ff',
            [TILES.CUSTOM_12]: '#55efc4',
            [TILES.CUSTOM_13]: '#ff7675',
            [TILES.CUSTOM_14]: '#636e72',
            [TILES.CUSTOM_15]: '#b2bec3',
            [TILES.CUSTOM_16]: '#fab1a0'
        };

        const TILE_NAMES = {};
        for (const [key, val] of Object.entries(TILES)) {
            TILE_NAMES[val] = key.replace(/_/g, ' ').replace(/\\b\\w/g, c => c.toUpperCase());
        }
        TILE_NAMES[TILES.AIR] = 'Air';
        TILE_NAMES[TILES.RAINBOW] = 'Rainbow';
        TILE_NAMES[TILES.TRANSPARENT] = 'Transparent';
        TILE_NAMES[TILES.ERASER] = 'Eraser';

        const COLOR_CATEGORIES = {
            'Basic': ['#ff0000','#00ff00','#0000ff','#ffff00','#ff00ff','#00ffff','#ffffff','#000000','#808080','#c0c0c0'],
            'Reds': ['#ff0000','#ff3333','#ff6666','#ff9999','#ffcccc','#cc0000','#990000','#660000','#330000','#ff1493','#ff69b4','#ffb6c1','#ffc0cb','#dc143c','#b22222','#8b0000','#800000'],
            'Oranges': ['#ff4500','#ff6347','#ff7f50','#ff8c00','#ffa500','#ffb347','#ffcc99','#ffdab9','#ffe4b5','#e25822','#d2691e','#cd853f','#a0522d'],
            'Yellows': ['#ffff00','#ffff33','#ffff66','#ffff99','#ffffcc','#cccc00','#999900','#666600','#333300','#ffd700','#ffdf00','#f0e68c','#fffacd','#f4d03f','#daa520','#b8860b','#8b6914'],
            'Greens': ['#00ff00','#33ff33','#66ff66','#99ff99','#ccffcc','#00cc00','#009900','#006600','#003300','#228b22','#32cd32','#7fff00','#adff2f','#006400','#2e8b57','#3cb371','#2f4f4f'],
            'Cyans': ['#00ffff','#33ffff','#66ffff','#99ffff','#ccffff','#00cccc','#009999','#006666','#003333','#00ced1','#20b2aa','#48d1cc','#40e0d0','#008b8b','#5f9ea0','#7fffd4','#afeeee'],
            'Blues': ['#0000ff','#3333ff','#6666ff','#9999ff','#ccccff','#0000cc','#000099','#000066','#000033','#1e90ff','#4169e1','#6495ed','#87ceeb','#00008b','#191970','#483d8b','#6a5acd'],
            'Purples': ['#800080','#9932cc','#9400d3','#8b008b','#4b0082','#9370db','#ba55d3','#da70d6','#dda0dd','#ee82ee','#ff00ff','#ff69b4','#ff1493','#663399','#7b68ee','#8a2be2','#9b59b6'],
            'Pinks': ['#ff69b4','#ff1493','#ff6b9d','#ff8fab','#ffb3c6','#ffc8dd','#ffafcc','#bde0fe','#cdb4db','#f4a261','#e76f51','#e9c46a','#2a9d8f'],
            'Browns': ['#8b4513','#a0522d','#cd853f','#d2691e','#deb887','#f4a460','#daa520','#b8860b','#8b6914','#6b4226','#5d4037','#4e342e','#3e2723','#795548','#8d6e63','#a1887f','#bcaaa4'],
            'Grays': ['#000000','#1a1a1a','#333333','#4d4d4d','#666666','#808080','#999999','#b3b3b3','#cccccc','#e6e6e6','#f5f5f5','#ffffff','#2f4f4f','#696969','#708090','#778899','#b0c4de','#c0c4c8'],
            'Pastels': ['#ffb3ba','#ffdfba','#ffffba','#baffc9','#bae1ff','#eecbff','#ffd1dc','#ffdead','#f0f8ff','#f5fffa','#fff0f5','#f0fff0','#fff8dc','#faf0e6','#faebd7','#ffe4e1','#fff5ee'],
            'Earth': ['#5d4037','#795548','#8d6e63','#a1887f','#bcaaa4','#d7ccc8','#4caf50','#66bb6a','#81c784','#a5d6a7','#c8e6c9','#388e3c','#2e7d32','#1b5e20','#33691e','#558b2f','#689f38','#7cb342'],
            'Neon': ['#ff006e','#fb5607','#ffbe0b','#8338ec','#3a86ff','#06ffa5','#ff4365','#00d9ff','#ff00ff','#39ff14','#ff073a','#bc13fe','#ff6b35'],
            'Special': ['rainbow','transparent','#ffffff','#000000']
        };

        // ==================== GAME STATE ====================
        let canvas, ctx, minimapCanvas, minimapCtx;
        let gameState = 'menu';
        let worldType = 'world';
        let world = [], caveWorld = [];
        let isCave = false;
        let camera = { x: 0, y: 0 };
        let player = { x: 0, y: 0, vx: 0, vy: 0, facing: 1, onGround: false };
        let mobs = [];
        let particles = [];
        let inventory = [];
        let selectedSlot = 0;
        let selectedTile = TILES.GRASS;
        let brushSize = 1;
        let keys = {};
        let mouse = { x: 0, y: 0, down: false, rightDown: false };
        let mobileDir = null;
        let mobileActionType = null;
        let settings = { dayNight: true, weather: false, minimap: true, sound: true };
        let timeOfDay = 0;
        let weatherTimer = 0;
        let isRaining = false, isSnowing = false;
        let rainbowOffset = 0;
        let lastTime = 0;
        let worldWidth = WORLD_WIDTH, worldHeight = WORLD_HEIGHT;
        let playerEnabled = true, mobsEnabled = true;
        let tooltipEl = document.getElementById('tooltip');
        let touchStartX = 0, touchStartY = 0, touchStartCameraX = 0, touchStartCameraY = 0;
        let isPanning = false;

        // ==================== INITIALIZATION ====================
        function init() {
            canvas = document.getElementById('gameCanvas');
            ctx = canvas.getContext('2d');
            minimapCanvas = document.getElementById('minimapCanvas');
            minimapCtx = minimapCanvas.getContext('2d');
            resizeCanvas();
            window.addEventListener('resize', resizeCanvas);
            document.addEventListener('keydown', handleKeyDown);
            document.addEventListener('keyup', handleKeyUp);
            canvas.addEventListener('mousedown', handleMouseDown);
            canvas.addEventListener('mouseup', handleMouseUp);
            canvas.addEventListener('mousemove', handleMouseMove);
            canvas.addEventListener('contextmenu', e => e.preventDefault());
            canvas.addEventListener('wheel', handleWheel);
            canvas.addEventListener('touchstart', handleTouchStart, { passive: false });
            canvas.addEventListener('touchend', handleTouchEnd);
            canvas.addEventListener('touchmove', handleTouchMove, { passive: false });
            buildColorMenu();
            requestAnimationFrame(gameLoop);
        }

        function resizeCanvas() {
            const container = document.getElementById('gameContainer');
            canvas.width = container.clientWidth;
            canvas.height = container.clientHeight;
            minimapCanvas.width = 150;
            minimapCanvas.height = 150;
        }

        function generateWorld(type) {
            worldType = type;
            isCave = false;
            document.getElementById('caveOverlay').style.background = 'rgba(0,0,0,0)';
            
            if (type === 'canvas') {
                worldWidth = CANVAS_WIDTH;
                worldHeight = CANVAS_HEIGHT;
                world = [];
                for (let y = 0; y < worldHeight; y++) {
                    world[y] = [];
                    for (let x = 0; x < worldWidth; x++) {
                        world[y][x] = TILES.AIR;
                    }
                }
            } else {
                worldWidth = WORLD_WIDTH;
                worldHeight = WORLD_HEIGHT;
                world = [];
                for (let y = 0; y < worldHeight; y++) {
                    world[y] = [];
                    for (let x = 0; x < worldWidth; x++) {
                        world[y][x] = TILES.AIR;
                    }
                }
                
                const groundLevel = Math.floor(worldHeight * 0.4);
                
                for (let x = 0; x < worldWidth; x++) {
                    const heightVar = Math.sin(x * 0.05) * 3 + Math.sin(x * 0.1) * 2 + Math.sin(x * 0.02) * 5;
                    const surfaceY = Math.floor(groundLevel + heightVar);
                    
                    for (let y = surfaceY; y < worldHeight; y++) {
                        if (y === surfaceY) {
                            world[y][x] = TILES.GRASS;
                        } else if (y < surfaceY + 3) {
                            world[y][x] = TILES.DIRT;
                        } else if (y < surfaceY + 8) {
                            world[y][x] = Math.random() < 0.3 ? TILES.STONE : TILES.DIRT;
                        } else {
                            world[y][x] = TILES.STONE;
                        }
                    }
                    
                    if (Math.random() < 0.08) {
                        for (let dy = -1; dy <= 2; dy++) {
                            if (surfaceY + dy >= 0 && surfaceY + dy < worldHeight) {
                                world[surfaceY + dy][x] = TILES.SAND;
                            }
                        }
                    }
                }
                
                for (let i = 0; i < 6; i++) {
                    const wx = Math.floor(Math.random() * (worldWidth - 10)) + 5;
                    const wy = Math.floor(groundLevel + Math.random() * 5);
                    const wsize = Math.floor(Math.random() * 8) + 4;
                    for (let dy = 0; dy < wsize; dy++) {
                        for (let dx = 0; dx < wsize; dx++) {
                            const nx = wx + dx, ny = wy + dy;
                            if (nx >= 0 && nx < worldWidth && ny >= 0 && ny < worldHeight) {
                                if (Math.random() < 0.8) world[ny][nx] = TILES.WATER;
                            }
                        }
                    }
                }
                
                for (let i = 0; i < 50; i++) {
                    const tx = Math.floor(Math.random() * (worldWidth - 4)) + 2;
                    const ty = findSurface(tx);
                    if (ty > 0) {
                        const height = Math.floor(Math.random() * 4) + 3;
                        for (let h = 0; h < height; h++) {
                            if (ty - 1 - h >= 0) world[ty - 1 - h][tx] = TILES.WOOD;
                        }
                        for (let ly = -height - 1; ly <= -height + 1; ly++) {
                            for (let lx = -2; lx <= 2; lx++) {
                                const nx = tx + lx, ny = ty + ly;
                                if (nx >= 0 && nx < worldWidth && ny >= 0 && ny < worldHeight) {
                                    if (Math.random() < 0.7 && world[ny][nx] === TILES.AIR) {
                                        world[ny][nx] = TILES.LEAVES;
                                    }
                                }
                            }
                        }
                    }
                }
                
                for (let i = 0; i < 35; i++) {
                    const rx = Math.floor(Math.random() * worldWidth);
                    const ry = findSurface(rx);
                    if (ry > 0 && ry - 1 >= 0) world[ry - 1][rx] = TILES.ROCK;
                }
                
                for (let x = 0; x < worldWidth; x++) {
                    const sy = findSurface(x);
                    if (sy > 0 && sy - 1 >= 0 && world[sy - 1][x] === TILES.AIR) {
                        const r = Math.random();
                        if (r < 0.15) world[sy - 1][x] = TILES.TALL_GRASS;
                        else if (r < 0.18) world[sy - 1][x] = TILES.FLOWER_RED;
                        else if (r < 0.21) world[sy - 1][x] = TILES.FLOWER_YELLOW;
                        else if (r < 0.23) world[sy - 1][x] = TILES.FERN;
                    }
                }
                
                for (let i = 0; i < 8; i++) {
                    const cx = Math.floor(Math.random() * (worldWidth - 10)) + 5;
                    const cy = findSurface(cx);
                    if (cy > 0) {
                        for (let dy = 0; dy < 3; dy++) {
                            for (let dx = -1; dx <= 1; dx++) {
                                const nx = cx + dx, ny = cy + dy;
                                if (nx >= 0 && nx < worldWidth && ny >= 0 && ny < worldHeight) {
                                    world[ny][nx] = TILES.CAVE_ENTRANCE;
                                }
                            }
                        }
                    }
                }
                
                generateCaveWorld();
            }
            
            const spawnX = Math.floor(worldWidth / 2);
            const spawnY = type === 'canvas' ? Math.floor(worldHeight / 2) : findSurface(spawnX) - 2;
            player.x = spawnX * TILE_SIZE;
            player.y = spawnY * TILE_SIZE;
            player.vx = 0;
            player.vy = 0;
            camera.x = player.x - canvas.width / 2;
            camera.y = player.y - canvas.height / 2;
            
            spawnMobs();
            setupInventory();
        }

        function generateCaveWorld() {
            caveWorld = [];
            const caveWidth = 100, caveHeight = 80;
            
            for (let y = 0; y < caveHeight; y++) {
                caveWorld[y] = [];
                for (let x = 0; x < caveWidth; x++) {
                    caveWorld[y][x] = TILES.CAVE_WALL;
                }
            }
            
            const centerX = Math.floor(caveWidth / 2);
            const centerY = Math.floor(caveHeight / 2);
            
            for (let y = 0; y < caveHeight; y++) {
                for (let x = 0; x < caveWidth; x++) {
                    const dist = Math.sqrt((x - centerX) ** 2 + (y - centerY) ** 2);
                    const noise = Math.sin(x * 0.2) * Math.cos(y * 0.2) * 5 + Math.sin(x * 0.5 + y * 0.3) * 3;
                    if (dist < 20 + noise) {
                        caveWorld[y][x] = TILES.CAVE_FLOOR;
                    }
                }
            }
            
            for (let i = 0; i < 10; i++) {
                let tx = centerX, ty = centerY;
                const angle = (Math.PI * 2 * i) / 10;
                const length = 15 + Math.random() * 20;
                for (let step = 0; step < length; step++) {
                    tx += Math.cos(angle) * 0.8 + (Math.random() - 0.5);
                    ty += Math.sin(angle) * 0.8 + (Math.random() - 0.5);
                    const ix = Math.floor(tx), iy = Math.floor(ty);
                    for (let dy = -1; dy <= 1; dy++) {
                        for (let dx = -1; dx <= 1; dx++) {
                            const nx = ix + dx, ny = iy + dy;
                            if (nx >= 0 && nx < caveWidth && ny >= 0 && ny < caveHeight) {
                                caveWorld[ny][nx] = TILES.CAVE_FLOOR;
                            }
                        }
                    }
                }
            }
            
            for (let i = 0; i < 25; i++) {
                const cx = Math.floor(Math.random() * caveWidth);
                const cy = Math.floor(Math.random() * caveHeight);
                if (caveWorld[cy][cx] === TILES.CAVE_WALL && 
                    ((cy > 0 && caveWorld[cy-1][cx] === TILES.CAVE_FLOOR) ||
                     (cy < caveHeight-1 && caveWorld[cy+1][cx] === TILES.CAVE_FLOOR))) {
                    caveWorld[cy][cx] = TILES.CAVE_CRYSTAL;
                }
            }
            
            for (let i = 0; i < 4; i++) {
                const lx = Math.floor(Math.random() * caveWidth);
                const ly = Math.floor(Math.random() * caveHeight);
                for (let dy = 0; dy < 4; dy++) {
                    for (let dx = 0; dx < 4; dx++) {
                        const nx = lx + dx, ny = ly + dy;
                        if (nx >= 0 && nx < caveWidth && ny >= 0 && ny < caveHeight) {
                            if (caveWorld[ny][nx] === TILES.CAVE_FLOOR && Math.random() < 0.6) {
                                caveWorld[ny][nx] = TILES.LAVA;
                            }
                        }
                    }
                }
            }
        }

        function findSurface(x) {
            for (let y = 0; y < worldHeight; y++) {
                if (world[y] && world[y][x] !== TILES.AIR) return y;
            }
            return worldHeight - 1;
        }

        function spawnMobs() {
            mobs = [];
            if (!mobsEnabled || worldType === 'canvas') return;
            const mobCount = 20;
            for (let i = 0; i < mobCount; i++) {
                const mx = Math.floor(Math.random() * worldWidth);
                const my = findSurface(mx);
                if (my > 0) {
                    mobs.push({
                        x: mx * TILE_SIZE, y: (my - 1) * TILE_SIZE,
                        vx: (Math.random() - 0.5) * MOB_SPEED, vy: 0,
                        type: Math.floor(Math.random() * 5),
                        dir: Math.random() < 0.5 ? -1 : 1,
                        timer: Math.random() * 3,
                        animFrame: 0
                    });
                }
            }
        }

        function setupInventory() {
            inventory = [];
            const defaultTiles = [
                TILES.GRASS, TILES.DIRT, TILES.STONE, TILES.WOOD, TILES.LEAVES,
                TILES.WATER, TILES.SAND, TILES.BRICK, TILES.GLASS, TILES.WOOL_WHITE
            ];
            for (let i = 0; i < 9; i++) {
                inventory.push({
                    tile: defaultTiles[i] || TILES.AIR,
                    count: 99
                });
            }
            updateInventoryUI();
        }

        function updateInventoryUI() {
            const bar = document.getElementById('inventoryBar');
            bar.innerHTML = '';
            for (let i = 0; i < 9; i++) {
                const slot = document.createElement('div');
                slot.className = 'inv-slot' + (i === selectedSlot ? ' selected' : '');
                slot.onclick = () => selectSlot(i);
                
                const keyLabel = document.createElement('span');
                keyLabel.className = 'slot-key';
                keyLabel.textContent = (i + 1).toString();
                slot.appendChild(keyLabel);
                
                if (inventory[i] && inventory[i].tile !== TILES.AIR) {
                    const preview = document.createElement('div');
                    preview.className = 'tile-preview';
                    const color = getTileColor(inventory[i].tile, 0, 0);
                    preview.style.background = color || '#333';
                    slot.appendChild(preview);
                    
                    const count = document.createElement('span');
                    count.className = 'slot-count';
                    count.textContent = inventory[i].count;
                    slot.appendChild(count);
                }
                
                bar.appendChild(slot);
            }
            selectedTile = inventory[selectedSlot] ? inventory[selectedSlot].tile : TILES.GRASS;
        }

        function selectSlot(i) {
            selectedSlot = i;
            updateInventoryUI();
        }

        function addToInventory(tile) {
            for (let i = 0; i < inventory.length; i++) {
                if (inventory[i].tile === tile && inventory[i].count < 99) {
                    inventory[i].count++;
                    updateInventoryUI();
                    return;
                }
            }
            for (let i = 0; i < inventory.length; i++) {
                if (inventory[i].tile === TILES.AIR) {
                    inventory[i] = { tile: tile, count: 1 };
                    updateInventoryUI();
                    return;
                }
            }
        }

        // ==================== INPUT HANDLERS ====================
        function handleKeyDown(e) {
            keys[e.key.toLowerCase()] = true;
            
            if (gameState !== 'playing') return;
            
            if (e.key >= '1' && e.key <= '9') {
                selectSlot(parseInt(e.key) - 1);
            }
            
            if (e.key === 'e' || e.key === 'E') {
                toggleColorMenu();
            }
            
            if (e.key === 'Escape') {
                toggleMenu();
            }
            
            if (e.key === 'b' || e.key === 'B') {
                brushSize = brushSize >= 5 ? 1 : brushSize + 1;
                showNotification('Brush: ' + brushSize + 'x' + brushSize);
            }
            
            if (e.key === 'c' || e.key === 'C') {
                clearCanvas();
            }
        }

        function handleKeyUp(e) {
            keys[e.key.toLowerCase()] = false;
        }

        function handleMouseDown(e) {
            if (gameState !== 'playing') return;
            if (document.getElementById('colorMenu').style.display === 'flex') return;
            if (document.getElementById('settingsMenu').style.display === 'flex') return;
            
            const rect = canvas.getBoundingClientRect();
            mouse.x = e.clientX - rect.left;
            mouse.y = e.clientY - rect.top;
            
            if (e.button === 0) {
                mouse.down = true;
                if (!playerEnabled) {
                    paintAtMouse();
                } else {
                    breakTileAtMouse();
                }
            } else if (e.button === 2) {
                mouse.rightDown = true;
                if (playerEnabled) {
                    placeTileAtMouse();
                }
            }
        }

        function handleMouseUp(e) {
            if (e.button === 0) mouse.down = false;
            if (e.button === 2) mouse.rightDown = false;
        }

        function handleMouseMove(e) {
            const rect = canvas.getBoundingClientRect();
            mouse.x = e.clientX - rect.left;
            mouse.y = e.clientY - rect.top;
            
            if (gameState === 'playing' && !playerEnabled && mouse.down) {
                paintAtMouse();
            }
        }

        function handleWheel(e) {
            if (gameState !== 'playing') return;
            e.preventDefault();
            if (e.deltaY < 0) {
                brushSize = Math.min(brushSize + 1, 5);
            } else {
                brushSize = Math.max(brushSize - 1, 1);
            }
            showNotification('Brush: ' + brushSize + 'x' + brushSize);
        }

        function handleTouchStart(e) {
            if (gameState !== 'playing') return;
            e.preventDefault();
            const touch = e.touches[0];
            const rect = canvas.getBoundingClientRect();
            mouse.x = touch.clientX - rect.left;
            mouse.y = touch.clientY - rect.top;
            
            if (!playerEnabled) {
                isPanning = true;
                touchStartX = touch.clientX;
                touchStartY = touch.clientY;
                touchStartCameraX = camera.x;
                touchStartCameraY = camera.y;
            }
        }

        function handleTouchMove(e) {
            if (gameState !== 'playing') return;
            e.preventDefault();
            const touch = e.touches[0];
            const rect = canvas.getBoundingClientRect();
            mouse.x = touch.clientX - rect.left;
            mouse.y = touch.clientY - rect.top;
            
            if (!playerEnabled && isPanning && e.touches.length === 1) {
                const dx = touchStartX - touch.clientX;
                const dy = touchStartY - touch.clientY;
                camera.x = touchStartCameraX + dx;
                camera.y = touchStartCameraY + dy;
            }
        }

        function handleTouchEnd(e) {
            isPanning = false;
        }

        function mobileMove(dir) {
            mobileDir = dir;
        }

        function mobileStop() {
            mobileDir = null;
        }

        function mobileAction(type) {
            mobileActionType = type;
            setTimeout(() => { mobileActionType = null; }, 200);
        }

        // ==================== PAINTING & TILE MANIPULATION ====================
        function getWorldAt(tx, ty) {
            const w = isCave ? caveWorld : world;
            const wh = isCave ? 80 : worldHeight;
            const ww = isCave ? 100 : worldWidth;
            if (tx < 0 || tx >= ww || ty < 0 || ty >= wh) return TILES.AIR;
            return w[ty] ? w[ty][tx] : TILES.AIR;
        }

        function setWorldAt(tx, ty, tile) {
            const w = isCave ? caveWorld : world;
            const wh = isCave ? 80 : worldHeight;
            const ww = isCave ? 100 : worldWidth;
            if (tx < 0 || tx >= ww || ty < 0 || ty >= wh) return;
            if (!w[ty]) return;
            w[ty][tx] = tile;
        }

        function paintAtMouse() {
            const tx = Math.floor((mouse.x + camera.x) / TILE_SIZE);
            const ty = Math.floor((mouse.y + camera.y) / TILE_SIZE);
            
            const half = Math.floor(brushSize / 2);
            for (let dy = -half; dy <= half; dy++) {
                for (let dx = -half; dx <= half; dx++) {
                    const nx = tx + dx, ny = ty + dy;
                    if (selectedTile === TILES.ERASER) {
                        setWorldAt(nx, ny, TILES.AIR);
                    } else {
                        setWorldAt(nx, ny, selectedTile);
                    }
                }
            }
        }

        function breakTileAtMouse() {
            const tx = Math.floor((mouse.x + camera.x) / TILE_SIZE);
            const ty = Math.floor((mouse.y + camera.y) / TILE_SIZE);
            const tile = getWorldAt(tx, ty);
            if (tile !== TILES.AIR) {
                addToInventory(tile);
                setWorldAt(tx, ty, TILES.AIR);
                spawnParticles(tx * TILE_SIZE + TILE_SIZE/2, ty * TILE_SIZE + TILE_SIZE/2, tile);
            }
        }

        function placeTileAtMouse() {
            const tx = Math.floor((mouse.x + camera.x) / TILE_SIZE);
            const ty = Math.floor((mouse.y + camera.y) / TILE_SIZE);
            if (getWorldAt(tx, ty) === TILES.AIR && inventory[selectedSlot] && inventory[selectedSlot].count > 0) {
                setWorldAt(tx, ty, inventory[selectedSlot].tile);
                inventory[selectedSlot].count--;
                if (inventory[selectedSlot].count <= 0) {
                    inventory[selectedSlot] = { tile: TILES.AIR, count: 0 };
                }
                updateInventoryUI();
            }
        }

        function clearCanvas() {
            const w = isCave ? caveWorld : world;
            const wh = isCave ? 80 : worldHeight;
            const ww = isCave ? 100 : worldWidth;
            for (let y = 0; y < wh; y++) {
                for (let x = 0; x < ww; x++) {
                    w[y][x] = TILES.AIR;
                }
            }
            showNotification('Canvas cleared!');
        }

        function spawnParticles(x, y, tile) {
            const color = getTileColor(tile, 0, 0);
            if (!color) return;
            for (let i = 0; i < 6; i++) {
                particles.push({
                    x: x, y: y,
                    vx: (Math.random() - 0.5) * 100,
                    vy: (Math.random() - 0.5) * 100 - 50,
                    life: 0.5 + Math.random() * 0.5,
                    maxLife: 1,
                    color: color,
                    size: 3 + Math.random() * 3
                });
            }
        }

        function buildColorMenu() {
            const catContainer = document.getElementById('colorCategories');
            const grid = document.getElementById('colorGrid');
            catContainer.innerHTML = '';
            
            let first = true;
            for (const [catName, colors] of Object.entries(COLOR_CATEGORIES)) {
                const btn = document.createElement('button');
                btn.className = 'color-cat-btn' + (first ? ' active' : '');
                btn.textContent = catName;
                btn.onclick = () => showColorCategory(catName);
                catContainer.appendChild(btn);
                first = false;
            }
            
            showColorCategory('Basic');
        }

        function showColorCategory(catName) {
            const grid = document.getElementById('colorGrid');
            grid.innerHTML = '';
            
            document.querySelectorAll('.color-cat-btn').forEach(btn => {
                btn.classList.toggle('active', btn.textContent === catName);
            });
            
            const colors = COLOR_CATEGORIES[catName];
            for (const color of colors) {
                const swatch = document.createElement('div');
                swatch.className = 'color-swatch';
                
                if (color === 'rainbow') {
                    swatch.style.background = 'linear-gradient(45deg, red, orange, yellow, green, blue, indigo, violet)';
                    swatch.title = 'Rainbow';
                    swatch.onclick = () => selectColorFromMenu(TILES.RAINBOW);
                } else if (color === 'transparent') {
                    swatch.style.background = 'repeating-conic-gradient(#444 0% 25%, #666 0% 50%)';
                    swatch.style.backgroundSize = '10px 10px';
                    swatch.title = 'Transparent';
                    swatch.onclick = () => selectColorFromMenu(TILES.TRANSPARENT);
                } else {
                    swatch.style.background = color;
                    swatch.title = color;
                    swatch.onclick = () => selectColorFromMenu(color);
                }
                
                grid.appendChild(swatch);
            }
        }

        function selectColorFromMenu(value) {
            if (typeof value === 'string' && value.startsWith('#')) {
                // Find or assign to a custom slot
                for (let i = TILES.CUSTOM_1; i <= TILES.CUSTOM_16; i++) {
                    if (TILE_COLORS[i] === value) {
                        selectedTile = i;
                        inventory[selectedSlot] = { tile: i, count: 99 };
                        updateInventoryUI();
                        showNotification('Selected: ' + value);
                        return;
                    }
                }
                // Assign to first available custom slot
                for (let i = TILES.CUSTOM_1; i <= TILES.CUSTOM_16; i++) {
                    if (!TILE_COLORS[i] || TILE_COLORS[i] === '#ff6b6b') {
                        TILE_COLORS[i] = value;
                        selectedTile = i;
                        inventory[selectedSlot] = { tile: i, count: 99 };
                        updateInventoryUI();
                        showNotification('Selected: ' + value);
                        return;
                    }
                }
            } else {
                selectedTile = value;
                inventory[selectedSlot] = { tile: value, count: 99 };
                updateInventoryUI();
                showNotification('Selected: ' + TILE_NAMES[value]);
            }
        }

        function toggleColorMenu() {
            const menu = document.getElementById('colorMenu');
            menu.style.display = menu.style.display === 'flex' ? 'none' : 'flex';
        }

        function toggleSettings() {
            const menu = document.getElementById('settingsMenu');
            menu.style.display = menu.style.display === 'flex' ? 'none' : 'flex';
        }

        function toggleSetting(name) {
            settings[name] = !settings[name];
            document.getElementById(name + 'Toggle').classList.toggle('active', settings[name]);
            
            if (name === 'minimap') {
                document.getElementById('minimapContainer').style.display = settings.minimap ? 'block' : 'none';
            }
        }

        // ==================== GAME MENU ====================
        function startGame(type) {
            gameState = 'playing';
            document.getElementById('mainMenu').style.display = 'none';
            document.getElementById('hud').style.display = 'block';
            document.getElementById('inventoryBar').style.display = 'flex';
            generateWorld(type);
            showNotification(type === 'canvas' ? 'Blank Canvas loaded!' : 'World generated!');
        }

        function toggleMenu() {
            if (gameState === 'playing') {
                gameState = 'menu';
                document.getElementById('mainMenu').style.display = 'flex';
                document.getElementById('hud').style.display = 'none';
                document.getElementById('inventoryBar').style.display = 'none';
                document.getElementById('colorMenu').style.display = 'none';
                document.getElementById('settingsMenu').style.display = 'none';
            }
        }

        function togglePlayer() {
            playerEnabled = !playerEnabled;
            document.getElementById('playerToggle').textContent = 'Player: ' + (playerEnabled ? 'ON' : 'OFF');
            document.getElementById('playerToggle').classList.toggle('active', playerEnabled);
            if (!playerEnabled) {
                showNotification('Art Mode: Free camera!');
            } else {
                camera.x = player.x - canvas.width / 2;
                camera.y = player.y - canvas.height / 2;
            }
        }

        function toggleMobs() {
            mobsEnabled = !mobsEnabled;
            document.getElementById('mobsToggle').textContent = 'Mobs: ' + (mobsEnabled ? 'ON' : 'OFF');
            document.getElementById('mobsToggle').classList.toggle('active', mobsEnabled);
            if (!mobsEnabled) {
                mobs = [];
            } else {
                spawnMobs();
            }
        }

        function showNotification(text) {
            const notif = document.getElementById('notification');
            notif.textContent = text;
            notif.style.display = 'block';
            setTimeout(() => { notif.style.display = 'none'; }, 2000);
        }

        // ==================== SAVE / LOAD ====================
        function saveWorld() {
            const data = {
                worldType: worldType,
                world: world,
                caveWorld: caveWorld,
                isCave: isCave,
                player: { x: player.x, y: player.y },
                inventory: inventory,
                timeOfDay: timeOfDay,
                settings: settings
            };
            const blob = new Blob([JSON.stringify(data)], { type: 'application/json' });
            const url = URL.createObjectURL(blob);
            const a = document.createElement('a');
            a.href = url;
            a.download = 'pixel_sandbox_world.json';
            a.click();
            URL.revokeObjectURL(url);
            showNotification('World saved!');
        }

        function loadWorld() {
            document.getElementById('fileInput').click();
        }

        function handleFileLoad(event) {
            const file = event.target.files[0];
            if (!file) return;
            const reader = new FileReader();
            reader.onload = (e) => {
                try {
                    const data = JSON.parse(e.target.result);
                    worldType = data.worldType || 'world';
                    world = data.world || [];
                    caveWorld = data.caveWorld || [];
                    isCave = data.isCave || false;
                    if (data.player) {
                        player.x = data.player.x;
                        player.y = data.player.y;
                    }
                    if (data.inventory) inventory = data.inventory;
                    if (data.timeOfDay !== undefined) timeOfDay = data.timeOfDay;
                    if (data.settings) settings = data.settings;
                    
                    worldWidth = world[0] ? world[0].length : WORLD_WIDTH;
                    worldHeight = world.length || WORLD_HEIGHT;
                    
                    gameState = 'playing';
                    document.getElementById('mainMenu').style.display = 'none';
                    document.getElementById('hud').style.display = 'block';
                    document.getElementById('inventoryBar').style.display = 'flex';
                    updateInventoryUI();
                    showNotification('World loaded!');
                } catch (err) {
                    showNotification('Failed to load world!');
                }
            };
            reader.readAsText(file);
        }

        // ==================== CAVE SYSTEM ====================
        function enterCave() {
            if (isCave) return;
            isCave = true;
            player.x = 50 * TILE_SIZE;
            player.y = 40 * TILE_SIZE;
            camera.x = player.x - canvas.width / 2;
            camera.y = player.y - canvas.height / 2;
            document.getElementById('caveOverlay').style.background = 'rgba(0,0,0,0.3)';
            showNotification('Entered cave!');
        }

        function exitCave() {
            if (!isCave) return;
            isCave = false;
            // Find a cave entrance to spawn near
            for (let y = 0; y < worldHeight; y++) {
                for (let x = 0; x < worldWidth; x++) {
                    if (world[y] && world[y][x] === TILES.CAVE_ENTRANCE) {
                        player.x = x * TILE_SIZE;
                        player.y = (y - 2) * TILE_SIZE;
                        camera.x = player.x - canvas.width / 2;
                        camera.y = player.y - canvas.height / 2;
                        document.getElementById('caveOverlay').style.background = 'rgba(0,0,0,0)';
                        showNotification('Exited cave!');
                        return;
                    }
                }
            }
        }

        // ==================== GAME LOOP ====================
        function gameLoop(timestamp) {
            const dt = Math.min((timestamp - lastTime) / 1000, 0.05);
            lastTime = timestamp;
            
            if (gameState === 'playing') {
                update(dt);
                render();
                if (settings.minimap) renderMinimap();
            }
            
            requestAnimationFrame(gameLoop);
        }

        function update(dt) {
            rainbowOffset += dt * 2;
            
            if (settings.dayNight) {
                timeOfDay += dt * 0.02;
                if (timeOfDay > 1) timeOfDay -= 1;
            }
            
            if (settings.weather) {
                weatherTimer += dt;
                if (weatherTimer > 20) {
                    weatherTimer = 0;
                    isRaining = !isRaining;
                    isSnowing = !isRaining && Math.random() < 0.3;
                }
            }
            
            updatePlayer(dt);
            updateMobs(dt);
            updateParticles(dt);
        }

        function updatePlayer(dt) {
            if (playerEnabled) {
                let moveX = 0, moveY = 0;
                
                if (keys['a'] || keys['arrowleft'] || mobileDir === 'left') moveX -= 1;
                if (keys['d'] || keys['arrowright'] || mobileDir === 'right') moveX += 1;
                if (keys['w'] || keys['arrowup'] || mobileDir === 'up') moveY -= 1;
                if (keys['s'] || keys['arrowdown'] || mobileDir === 'down') moveY += 1;
                
                if (moveX !== 0 || moveY !== 0) {
                    const speed = PLAYER_SPEED;
                    player.vx = moveX * speed;
                    player.vy = moveY * speed;
                    if (moveX !== 0) player.facing = moveX > 0 ? 1 : -1;
                } else {
                    player.vx *= 0.8;
                    player.vy *= 0.8;
                }
                
                const newX = player.x + player.vx * dt;
                const newY = player.y + player.vy * dt;
                
                if (!isSolidTileAt(newX, player.y)) player.x = newX;
                if (!isSolidTileAt(player.x, newY)) player.y = newY;
                
                // Check cave entrance
                const tx = Math.floor((player.x + TILE_SIZE/2) / TILE_SIZE);
                const ty = Math.floor((player.y + TILE_SIZE/2) / TILE_SIZE);
                const tile = getWorldAt(tx, ty);
                if (tile === TILES.CAVE_ENTRANCE && !isCave) {
                    enterCave();
                }
                
                // Camera follows player
                const targetCamX = player.x - canvas.width / 2;
                const targetCamY = player.y - canvas.height / 2;
                camera.x += (targetCamX - camera.x) * 0.1;
                camera.y += (targetCamY - camera.y) * 0.1;
            } else {
                // Art mode - free camera with WASD
                let camSpeed = 300;
                if (keys['a'] || keys['arrowleft'] || mobileDir === 'left') camera.x -= camSpeed * dt;
                if (keys['d'] || keys['arrowright'] || mobileDir === 'right') camera.x += camSpeed * dt;
                if (keys['w'] || keys['arrowup'] || mobileDir === 'up') camera.y -= camSpeed * dt;
                if (keys['s'] || keys['arrowdown'] || mobileDir === 'down') camera.y += camSpeed * dt;
            }
            
            // Clamp camera
            const maxCamX = (isCave ? 100 : worldWidth) * TILE_SIZE - canvas.width;
            const maxCamY = (isCave ? 80 : worldHeight) * TILE_SIZE - canvas.height;
            camera.x = Math.max(0, Math.min(camera.x, Math.max(0, maxCamX)));
            camera.y = Math.max(0, Math.min(camera.y, Math.max(0, maxCamY)));
        }

        function isSolidTileAt(x, y) {
            const tx = Math.floor(x / TILE_SIZE);
            const ty = Math.floor(y / TILE_SIZE);
            const tile = getWorldAt(tx, ty);
            return tile !== TILES.AIR && tile !== TILES.WATER && tile !== TILES.TALL_GRASS &&
                   tile !== TILES.FLOWER_RED && tile !== TILES.FLOWER_YELLOW && tile !== TILES.FERN;
        }

        function updateMobs(dt) {
            if (!mobsEnabled || isCave) return;
            
            for (const mob of mobs) {
                mob.timer += dt;
                mob.animFrame += dt * 4;
                
                if (mob.timer > 2 + Math.random() * 3) {
                    mob.timer = 0;
                    mob.dir = Math.random() < 0.5 ? -1 : 1;
                }
                
                mob.x += mob.dir * MOB_SPEED * dt;
                
                // Keep mobs in bounds
                if (mob.x < 0) { mob.x = 0; mob.dir = 1; }
                if (mob.x > worldWidth * TILE_SIZE) { mob.x = worldWidth * TILE_SIZE; mob.dir = -1; }
                
                // Simple ground following
                const tx = Math.floor(mob.x / TILE_SIZE);
                const ty = findSurface(tx);
                mob.y = (ty - 1) * TILE_SIZE;
            }
        }

        function updateParticles(dt) {
            for (let i = particles.length - 1; i >= 0; i--) {
                const p = particles[i];
                p.x += p.vx * dt;
                p.y += p.vy * dt;
                p.vy += 200 * dt;
                p.life -= dt;
                if (p.life <= 0) particles.splice(i, 1);
            }
        }

        function render() {
            ctx.clearRect(0, 0, canvas.width, canvas.height);
            
            // Sky / background
            if (!isCave) {
                const skyColor = getSkyColor();
                ctx.fillStyle = skyColor;
                ctx.fillRect(0, 0, canvas.width, canvas.height);
                
                // Sun / Moon
                if (settings.dayNight) {
                    const sunX = canvas.width * 0.1 + (timeOfDay * canvas.width * 0.8);
                    const sunY = canvas.height * 0.15 + Math.sin(timeOfDay * Math.PI) * -50;
                    if (timeOfDay < 0.75 && timeOfDay > 0.25) {
                        ctx.fillStyle = '#ffd700';
                        ctx.beginPath();
                        ctx.arc(sunX, sunY, 25, 0, Math.PI * 2);
                        ctx.fill();
                    } else {
                        ctx.fillStyle = '#f0f0f0';
                        ctx.beginPath();
                        ctx.arc(sunX, sunY, 20, 0, Math.PI * 2);
                        ctx.fill();
                    }
                }
            } else {
                ctx.fillStyle = '#1a0a1a';
                ctx.fillRect(0, 0, canvas.width, canvas.height);
            }
            
            const startX = Math.floor(camera.x / TILE_SIZE);
            const startY = Math.floor(camera.y / TILE_SIZE);
            const endX = startX + Math.ceil(canvas.width / TILE_SIZE) + 1;
            const endY = startY + Math.ceil(canvas.height / TILE_SIZE) + 1;
            
            const w = isCave ? caveWorld : world;
            const wh = isCave ? 80 : worldHeight;
            const ww = isCave ? 100 : worldWidth;
            
            // Draw tiles
            for (let y = startY; y <= endY; y++) {
                for (let x = startX; x <= endX; x++) {
                    if (y < 0 || y >= wh || x < 0 || x >= ww) continue;
                    const tile = w[y][x];
                    if (tile === TILES.AIR) continue;
                    
                    const screenX = x * TILE_SIZE - camera.x;
                    const screenY = y * TILE_SIZE - camera.y;
                    
                    const color = getTileColor(tile, x, y);
                    if (color) {
                        ctx.fillStyle = color;
                        ctx.fillRect(screenX, screenY, TILE_SIZE, TILE_SIZE);
                        
                        // Tile detail / border
                        ctx.strokeStyle = 'rgba(0,0,0,0.1)';
                        ctx.lineWidth = 1;
                        ctx.strokeRect(screenX, screenY, TILE_SIZE, TILE_SIZE);
                        
                        // Special tile details
                        drawTileDetail(tile, screenX, screenY);
                    }
                }
            }
            
            // Draw mobs
            if (mobsEnabled && !isCave) {
                for (const mob of mobs) {
                    const screenX = mob.x - camera.x;
                    const screenY = mob.y - camera.y;
                    if (screenX < -32 || screenX > canvas.width + 32 || screenY < -32 || screenY > canvas.height + 32) continue;
                    drawMob(mob, screenX, screenY);
                }
            }
            
            // Draw player
            if (playerEnabled) {
                const screenX = player.x - camera.x;
                const screenY = player.y - camera.y;
                drawPlayer(screenX, screenY);
            }
            
            // Draw particles
            for (const p of particles) {
                const screenX = p.x - camera.x;
                const screenY = p.y - camera.y;
                ctx.fillStyle = p.color;
                ctx.globalAlpha = p.life / p.maxLife;
                ctx.fillRect(screenX - p.size/2, screenY - p.size/2, p.size, p.size);
                ctx.globalAlpha = 1;
            }
            
            // Weather
            if (settings.weather && !isCave) {
                drawWeather();
            }
            
            // Day/night overlay
            if (settings.dayNight && !isCave) {
                drawDayNightOverlay();
            }
            
            // Brush preview
            if (!playerEnabled && gameState === 'playing') {
                drawBrushPreview();
            }
            
            // Crosshair when player is on
            if (playerEnabled && gameState === 'playing') {
                ctx.strokeStyle = 'rgba(255,255,255,0.5)';
                ctx.lineWidth = 1;
                const cx = mouse.x;
                const cy = mouse.y;
                ctx.beginPath();
                ctx.moveTo(cx - 8, cy);
                ctx.lineTo(cx + 8, cy);
                ctx.moveTo(cx, cy - 8);
                ctx.lineTo(cx, cy + 8);
                ctx.stroke();
            }
        }

        function getTileColor(tile, x, y) {
            if (tile === TILES.RAINBOW) {
                const hue = ((x + y + rainbowOffset * 50) % 360);
                return 'hsl(' + hue + ', 80%, 60%)';
            }
            if (tile === TILES.ERASER) return null;
            return TILE_COLORS[tile] || '#888';
        }

        function drawTileDetail(tile, x, y) {
            ctx.fillStyle = 'rgba(255,255,255,0.15)';
            
            switch(tile) {
                case TILES.GRASS:
                    // Grass blades
                    ctx.fillStyle = '#5aad4a';
                    ctx.fillRect(x + 4, y + 2, 3, 8);
                    ctx.fillRect(x + 12, y + 4, 3, 6);
                    ctx.fillRect(x + 20, y + 1, 3, 9);
                    ctx.fillRect(x + 26, y + 3, 3, 7);
                    break;
                case TILES.WATER:
                    // Water shimmer
                    ctx.fillStyle = 'rgba(255,255,255,0.2)';
                    const waveY = Math.sin(Date.now() * 0.003 + x * 0.1) * 3;
                    ctx.fillRect(x + 2, y + 8 + waveY, 8, 2);
                    ctx.fillRect(x + 18, y + 14 + waveY, 10, 2);
                    break;
                case TILES.WOOD:
                    // Wood grain
                    ctx.strokeStyle = 'rgba(0,0,0,0.2)';
                    ctx.lineWidth = 1;
                    ctx.beginPath();
                    ctx.moveTo(x + 4, y + 8); ctx.lineTo(x + 28, y + 8);
                    ctx.moveTo(x + 2, y + 20); ctx.lineTo(x + 30, y + 20);
                    ctx.stroke();
                    break;
                case TILES.LEAVES:
                    // Leaf texture
                    ctx.fillStyle = 'rgba(0,0,0,0.1)';
                    ctx.fillRect(x + 6, y + 6, 6, 6);
                    ctx.fillRect(x + 18, y + 14, 8, 8);
                    break;
                case TILES.STONE:
                    // Stone cracks
                    ctx.strokeStyle = 'rgba(0,0,0,0.15)';
                    ctx.lineWidth = 1;
                    ctx.beginPath();
                    ctx.moveTo(x + 5, y + 5); ctx.lineTo(x + 12, y + 10);
                    ctx.moveTo(x + 20, y + 8); ctx.lineTo(x + 25, y + 18);
                    ctx.stroke();
                    break;
                case TILES.CAVE_CRYSTAL:
                    // Crystal glow
                    ctx.fillStyle = 'rgba(155, 89, 182, 0.5)';
                    ctx.fillRect(x + 8, y + 4, 16, 24);
                    ctx.fillStyle = 'rgba(255,255,255,0.4)';
                    ctx.fillRect(x + 12, y + 8, 8, 16);
                    break;
                case TILES.LAVA:
                    // Lava glow
                    ctx.fillStyle = 'rgba(255, 100, 50, 0.3)';
                    const lavaY = Math.sin(Date.now() * 0.005 + x * 0.2) * 4;
                    ctx.fillRect(x + 2, y + 4 + lavaY, 28, 8);
                    break;
                case TILES.TALL_GRASS:
                    ctx.fillStyle = '#6bbd5a';
                    ctx.fillRect(x + 10, y + 4, 3, 20);
                    ctx.fillRect(x + 18, y + 6, 3, 18);
                    ctx.fillRect(x + 6, y + 8, 3, 16);
                    break;
                case TILES.FLOWER_RED:
                    ctx.fillStyle = '#ff3333';
                    ctx.beginPath(); ctx.arc(x + 16, y + 10, 5, 0, Math.PI * 2); ctx.fill();
                    ctx.fillStyle = '#ff6666';
                    ctx.beginPath(); ctx.arc(x + 16, y + 10, 3, 0, Math.PI * 2); ctx.fill();
                    ctx.fillStyle = '#ffff00';
                    ctx.beginPath(); ctx.arc(x + 16, y + 10, 2, 0, Math.PI * 2); ctx.fill();
                    ctx.fillStyle = '#228b22';
                    ctx.fillRect(x + 15, y + 15, 2, 17);
                    break;
                case TILES.FLOWER_YELLOW:
                    ctx.fillStyle = '#ffd700';
                    for (let i = 0; i < 6; i++) {
                        const angle = (i / 6) * Math.PI * 2;
                        const px = x + 16 + Math.cos(angle) * 5;
                        const py = y + 10 + Math.sin(angle) * 5;
                        ctx.beginPath(); ctx.arc(px, py, 3, 0, Math.PI * 2); ctx.fill();
                    }
                    ctx.fillStyle = '#ff8c00';
                    ctx.beginPath(); ctx.arc(x + 16, y + 10, 3, 0, Math.PI * 2); ctx.fill();
                    ctx.fillStyle = '#228b22';
                    ctx.fillRect(x + 15, y + 15, 2, 17);
                    break;
                case TILES.ROCK:
                    ctx.fillStyle = '#555';
                    ctx.beginPath(); ctx.arc(x + 12, y + 20, 6, 0, Math.PI * 2); ctx.fill();
                    ctx.beginPath(); ctx.arc(x + 20, y + 22, 5, 0, Math.PI * 2); ctx.fill();
                    ctx.beginPath(); ctx.arc(x + 16, y + 18, 4, 0, Math.PI * 2); ctx.fill();
                    break;
                case TILES.CAVE_ENTRANCE:
                    ctx.fillStyle = '#1a1a1a';
                    ctx.fillRect(x + 6, y + 4, 20, 24);
                    ctx.fillStyle = '#0a0a0a';
                    ctx.fillRect(x + 10, y + 8, 12, 18);
                    break;
            }
        }

        function drawMob(mob, x, y) {
            const bob = Math.sin(mob.animFrame) * 2;
            
            // Body
            ctx.fillStyle = mob.type === 0 ? '#ff8c00' :
                           mob.type === 1 ? '#87ceeb' :
                           mob.type === 2 ? '#98fb98' :
                           mob.type === 3 ? '#dda0dd' : '#f0e68c';
            ctx.fillRect(x + 4, y + 8 + bob, 24, 16);
            
            // Head
            ctx.fillStyle = mob.type === 0 ? '#ffa500' :
                           mob.type === 1 ? '#b0e0e6' :
                           mob.type === 2 ? '#90ee90' :
                           mob.type === 3 ? '#d8bfd8' : '#fffacd';
            ctx.fillRect(x + 8, y + bob, 16, 12);
            
            // Eyes
            ctx.fillStyle = '#000';
            const eyeDir = mob.dir > 0 ? 1 : -1;
            ctx.fillRect(x + 12 + eyeDir * 2, y + 4 + bob, 3, 3);
            ctx.fillRect(x + 18 + eyeDir * 2, y + 4 + bob, 3, 3);
            
            // Legs
            ctx.fillStyle = ctx.fillStyle;
            const legAnim = Math.sin(mob.animFrame * 2) * 3;
            ctx.fillRect(x + 6, y + 24 + bob, 6, 6 + legAnim);
            ctx.fillRect(x + 20, y + 24 + bob, 6, 6 - legAnim);
        }

        function drawPlayer(x, y) {
            const bob = Math.sin(Date.now() * 0.01) * 1;
            
            // Body
            ctx.fillStyle = '#4169e1';
            ctx.fillRect(x + 8, y + 12, 16, 14);
            
            // Head
            ctx.fillStyle = '#ffdbac';
            ctx.fillRect(x + 10, y + 2, 12, 10);
            
            // Eyes
            ctx.fillStyle = '#000';
            const eyeOffset = player.facing > 0 ? 2 : -2;
            ctx.fillRect(x + 12 + eyeOffset, y + 6, 2, 2);
            ctx.fillRect(x + 18 + eyeOffset, y + 6, 2, 2);
            
            // Legs
            ctx.fillStyle = '#2f4f4f';
            const legAnim = Math.sin(Date.now() * 0.015) * 2;
            ctx.fillRect(x + 9, y + 26 + bob, 5, 6 + legAnim);
            ctx.fillRect(x + 18, y + 26 + bob, 5, 6 - legAnim);
            
            // Arms
            ctx.fillStyle = '#ffdbac';
            ctx.fillRect(x + 4, y + 14, 4, 10);
            ctx.fillRect(x + 24, y + 14, 4, 10);
        }

        function drawBrushPreview() {
            const tx = Math.floor((mouse.x + camera.x) / TILE_SIZE);
            const ty = Math.floor((mouse.y + camera.y) / TILE_SIZE);
            const half = Math.floor(brushSize / 2);
            
            ctx.strokeStyle = 'rgba(255, 255, 255, 0.6)';
            ctx.lineWidth = 2;
            ctx.setLineDash([4, 4]);
            
            const startX = (tx - half) * TILE_SIZE - camera.x;
            const startY = (ty - half) * TILE_SIZE - camera.y;
            const size = brushSize * TILE_SIZE;
            ctx.strokeRect(startX, startY, size, size);
            ctx.setLineDash([]);
        }

        function getSkyColor() {
            if (!settings.dayNight) return '#87ceeb';
            
            const t = timeOfDay;
            if (t < 0.2) return '#0a0a2e'; // Night
            if (t < 0.3) return '#ff6b6b'; // Dawn
            if (t < 0.7) return '#87ceeb'; // Day
            if (t < 0.8) return '#ff8c69'; // Dusk
            return '#0a0a2e'; // Night
        }

        function drawDayNightOverlay() {
            const t = timeOfDay;
            let alpha = 0;
            if (t < 0.2 || t > 0.8) alpha = 0.5;
            else if (t < 0.3) alpha = 0.5 * (0.3 - t) / 0.1;
            else if (t > 0.7) alpha = 0.5 * (t - 0.7) / 0.1;
            
            if (alpha > 0) {
                ctx.fillStyle = 'rgba(0, 0, 20, ' + alpha + ')';
                ctx.fillRect(0, 0, canvas.width, canvas.height);
            }
        }

        function drawWeather() {
            ctx.strokeStyle = isSnowing ? 'rgba(255,255,255,0.8)' : 'rgba(150,180,255,0.6)';
            ctx.lineWidth = isSnowing ? 2 : 1;
            
            const count = isSnowing ? 80 : 100;
            const time = Date.now() * 0.001;
            
            for (let i = 0; i < count; i++) {
                const x = ((i * 137.5 + time * 100) % canvas.width);
                const y = ((i * 73.3 + time * (isSnowing ? 30 : 200)) % canvas.height);
                const len = isSnowing ? 2 : 8 + Math.random() * 5;
                
                ctx.beginPath();
                if (isSnowing) {
                    ctx.arc(x, y, 2, 0, Math.PI * 2);
                    ctx.fillStyle = 'rgba(255,255,255,0.8)';
                    ctx.fill();
                } else {
                    ctx.moveTo(x, y);
                    ctx.lineTo(x - 2, y + len);
                    ctx.stroke();
                }
            }
        }

        function renderMinimap() {
            const mmw = minimapCanvas.width;
            const mmh = minimapCanvas.height;
            minimapCtx.fillStyle = '#000';
            minimapCtx.fillRect(0, 0, mmw, mmh);
            
            const w = isCave ? caveWorld : world;
            const wh = isCave ? 80 : worldHeight;
            const ww = isCave ? 100 : worldWidth;
            
            const scaleX = mmw / ww;
            const scaleY = mmh / wh;
            
            for (let y = 0; y < wh; y++) {
                for (let x = 0; x < ww; x++) {
                    const tile = w[y][x];
                    if (tile === TILES.AIR) continue;
                    const color = getTileColor(tile, x, y);
                    if (color) {
                        minimapCtx.fillStyle = color;
                        minimapCtx.fillRect(x * scaleX, y * scaleY, scaleX + 1, scaleY + 1);
                    }
                }
            }
            
            // Player dot
            if (playerEnabled) {
                minimapCtx.fillStyle = '#ff0000';
                minimapCtx.fillRect(
                    (player.x / TILE_SIZE) * scaleX - 2,
                    (player.y / TILE_SIZE) * scaleY - 2,
                    4, 4
                );
            }
            
            // Camera rect
            minimapCtx.strokeStyle = '#fff';
            minimapCtx.lineWidth = 1;
            minimapCtx.strokeRect(
                (camera.x / TILE_SIZE) * scaleX,
                (camera.y / TILE_SIZE) * scaleY,
                (canvas.width / TILE_SIZE) * scaleX,
                (canvas.height / TILE_SIZE) * scaleY
            );
        }

        // ==================== START ====================
        window.onload = init;
    </script>

</body>
</html>
'''

with open('/mnt/agents/output/pixel_sandbox_deluxe.html', 'a') as f:
    f.write(part5)

print("Part 5 written successfully - Game complete!")


