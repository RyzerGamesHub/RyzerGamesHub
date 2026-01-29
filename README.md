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
