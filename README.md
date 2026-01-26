// server.js
// PROVEN MINIMAL WORKING SOCKET.IO CHAT
// This is the official Socket.IO example adapted to one file
// It WORKS if Node >=16

const express = require('express');
const http = require('http');
const { Server } = require('socket.io');

const app = express();
const server = http.createServer(app);
const io = new Server(server);

app.get('/', (req, res) => {
  res.send(`<!DOCTYPE html>
<html>
<head>
  <title>Chat</title>
  <style>
    body { font-family: Arial; background:#111; color:white; margin:0; }
    #messages { list-style:none; padding:10px; height:90vh; overflow-y:auto; }
    #messages li { padding:4px 0; }
    form { display:flex; position:fixed; bottom:0; width:100%; }
    input { flex:1; padding:10px; font-size:16px; }
    button { padding:10px; font-size:16px; }
  </style>
</head>
<body>
<ul id="messages"></ul>
<form id="form">
  <input id="input" autocomplete="off" />
  <button>Send</button>
</form>

<script src="/socket.io/socket.io.js"></script>
<script>
  const socket = io();
  const form = document.getElementById('form');
  const input = document.getElementById('input');
  const messages = document.getElementById('messages');

  form.addEventListener('submit', function(e) {
    e.preventDefault();
    if (input.value) {
      socket.emit('chat message', input.value);
      input.value = '';
    }
  });

  socket.on('chat message', function(msg) {
    const item = document.createElement('li');
    item.textContent = msg;
    messages.appendChild(item);
    messages.scrollTop = messages.scrollHeight;
  });
</script>
</body>
</html>`);
});

io.on('connection', (socket) => {
  socket.on('chat message', (msg) => {
    io.emit('chat message', msg);
  });
});

server.listen(3000, () => {
  console.log('http://localhost:3000');
});
