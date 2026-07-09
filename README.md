<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Pixel Sandbox Deluxe</title>
    <style>
        body { display: flex; justify-content: center; align-items: center; height: 100vh; background-color: #f0f0f0; }
        #canvas { border: 1px solid #000; }
        #tools { margin: 20px; }
    </style>
</head>
<body>
    <div id="tools">
        <button onclick="setMode('art')">Art Mode</button>
        <button onclick="setMode('world')">World Mode</button>
        <button onclick="setMode('sandbox')">Sandbox Mode</button>
        <button onclick="toggleCreatures()">Toggle Creatures</button>
        <button onclick="togglePlayer()">Toggle Player</button>
        <button onclick="freezeSimulation()">Freeze Simulation</button>
        <button onclick="saveWorld()">Save World</button>
    </div>
    <canvas id="canvas" width="512" height="512"></canvas>
    <script>
        function setMode(mode) {
            console.log('Set mode to: ' + mode);
        }
        function toggleCreatures() {
            console.log('Toggled creatures.');
        }
        function togglePlayer() {
            console.log('Toggled player.');
        }
        function freezeSimulation() {
            console.log('Simulation frozen.');
        }
        function saveWorld() {
            console.log('World saved.');
        }
    </script>
</body>
</html>
