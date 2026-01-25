// server.js
// Full working Discord-style chat with login using Node.js, Express, Socket.IO
// Run: npm init -y && npm install express socket.io bcrypt jsonwebtoken
// Then: node server.js

const express = require("express");
const http = require("http");
const { Server } = require("socket.io");
const bcrypt = require("bcrypt");
const jwt = require("jsonwebtoken");

const app = express();
const server = http.createServer(app);
const io = new Server(server);

app.use(express.json());

const SECRET = "super_secret_key_change_me";
const users = {};
const messages = [];

app.post("/register", async (req, res) => {
  const { username, password } = req.body;
  if (!username || !password) return res.status(400).end();
  if (users[username]) return res.status(409).end();
  users[username] = await bcrypt.hash(password, 10);
  res.status(200).end();
});

app.post("/login", async (req, res) => {
  const { username, password } = req.body;
  const hash = users[username];
  if (!hash) return res.status(401).end();
  const ok = await bcrypt.compare(password, hash);
  if (!ok) return res.status(401).end();
  const token = jwt.sign({ username }, SECRET);
  res.json({ token });
});

app.get("/", (req, res) => {
  res.send(`<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8" />
<title>Chat</title>
<script src="/socket.io/socket.io.js"></script>
<style>
body { margin:0; font-family:Arial; background:#2b2d31; color:white; }
#login,#chat { display:none; height:100vh; }
#login { display:flex; align-items:center; justify-content:center; }
#login form { background:#1e1f22; padding:20px; border-radius:8px; }
#chat { display:flex; }
#servers { width:70px; background:#1e1f22; }
#channels { width:200px; background:#232428; }
#main { flex:1; display:flex; flex-direction:column; }
#messages { flex:1; padding:10px; overflow-y:auto; }
#input { display:flex; }
#input input { flex:1; padding:10px; }
button { background:#5865f2; border:none; color:white; padding:10px; }
</style>
</head>
<body>
<div id="login">
  <form id="loginForm">
    <input id="user" placeholder="username" /><br/><br/>
    <input id="pass" type="password" placeholder="password" /><br/><br/>
    <button type="submit">Login / Register</button>
  </form>
</div>
<div id="chat">
  <div id="servers"></div>
  <div id="channels"></div>
  <div id="main">
    <div id="messages"></div>
    <div id="input">
      <input id="msg" placeholder="Message" />
      <button onclick="send()">Send</button>
    </div>
  </div>
</div>
<script>
let token = null;
const socket = io();
const login = document.getElementById("login");
const chat = document.getElementById("chat");

login.style.display = "flex";

document.getElementById("loginForm").onsubmit = async e => {
  e.preventDefault();
  const username = user.value;
  const password = pass.value;
  await fetch("/register", { method:"POST", headers:{"Content-Type":"application/json"}, body:JSON.stringify({username,password}) }).catch(()=>{});
  const res = await fetch("/login", { method:"POST", headers:{"Content-Type":"application/json"}, body:JSON.stringify({username,password}) });
  const data = await res.json();
  token = data.token;
  socket.emit("auth", token);
  login.style.display = "none";
  chat.style.display = "flex";
};

socket.on("message", m => {
  const div = document.createElement("div");
  div.textContent = m.user + ": " + m.text;
  messages.appendChild(div);
  messages.scrollTop = messages.scrollHeight;
});

function send() {
  const text = msg.value;
  msg.value = "";
  socket.emit("message", { token, text });
}
</script>
</body>
</html>`);
});

io.on("connection", socket => {
  let user = null;

  socket.on("auth", token => {
    try {
      const data = jwt.verify(token, SECRET);
      user = data.username;
      messages.forEach(m => socket.emit("message", m));
    } catch {}
  });

  socket.on("message", ({ token, text }) => {
    try {
      const data = jwt.verify(token, SECRET);
      const msg = { user: data.username, text };
      messages.push(msg);
      io.emit("message", msg);
    } catch {}
  });
});

server.listen(3000);

