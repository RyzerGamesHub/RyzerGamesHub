// server.js
// FIXED Discord-style chat app
// Run:
// npm init -y
// npm install express socket.io bcrypt jsonwebtoken
// node server.js

const express = require("express");
const http = require("http");
const { Server } = require("socket.io");
const bcrypt = require("bcrypt");
const jwt = require("jsonwebtoken");

const app = express();
const server = http.createServer(app);
const io = new Server(server);

app.use(express.json());

const SECRET = "change_this_secret";
const users = {}; // { username: passwordHash }
const messages = [];

app.post("/register", async (req, res) => {
  const { username, password } = req.body;
  if (!username || !password) return res.sendStatus(400);
  if (users[username]) return res.sendStatus(409);
  users[username] = await bcrypt.hash(password, 10);
  res.sendStatus(200);
});

app.post("/login", async (req, res) => {
  const { username, password } = req.body;
  const hash = users[username];
  if (!hash) return res.sendStatus(401);
  const ok = await bcrypt.compare(password, hash);
  if (!ok) return res.sendStatus(401);
  const token = jwt.sign({ username }, SECRET);
  res.json({ token });
});

app.get("/", (req, res) => {
  res.redirect("/login");
});

app.get("/login", (req, res) => {
  res.send(`<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8" />
<title>Login</title>
<style>
body{margin:0;font-family:Arial;background:#2b2d31;color:#fff;display:flex;align-items:center;justify-content:center;height:100vh}
form{background:#1e1f22;padding:24px;border-radius:8px;width:260px}
input,button{width:100%;padding:10px;margin-top:10px;font-size:16px}
button{background:#5865f2;border:none;color:#fff;cursor:pointer}
</style>
</head>
<body>
<form id="loginForm">
  <input id="user" placeholder="username" required />
  <input id="pass" type="password" placeholder="password" required />
  <button type="submit">Login / Register</button>
</form>
<script>
document.getElementById("loginForm").onsubmit = async e => {
  e.preventDefault();
  const username = user.value.trim();
  const password = pass.value.trim();

  await fetch("/register",{
    method:"POST",
    headers:{"Content-Type":"application/json"},
    body:JSON.stringify({username,password})
  }).catch(()=>{});

  const res = await fetch("/login",{
    method:"POST",
    headers:{"Content-Type":"application/json"},
    body:JSON.stringify({username,password})
  });

  if(!res.ok) return alert("Login failed");
  const data = await res.json();
  localStorage.setItem("token", data.token);
  location.href = "/chat";
};
</script>
</body>
</html>`);
});

app.get("/chat", (req, res) => {
  res.send(`<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8" />
<title>Chat</title>
<script src="/socket.io/socket.io.js"></script>
<style>
body{margin:0;font-family:Arial;background:#2b2d31;color:#fff;height:100vh;display:flex}
#servers{width:70px;background:#1e1f22}
#channels{width:200px;background:#232428}
#main{flex:1;display:flex;flex-direction:column}
#messages{flex:1;padding:10px;overflow-y:auto}
#input{display:flex}
#input input{flex:1;padding:10px;font-size:16px}
button{background:#5865f2;border:none;color:#fff;padding:10px;cursor:pointer}
</style>
</head>
<body>
<div id="servers"></div>
<div id="channels"></div>
<div id="main">
  <div id="messages"></div>
  <div id="input">
    <input id="msg" placeholder="Message" />
    <button id="sendBtn">Send</button>
  </div>
</div>
<script>
const token = localStorage.getItem("token");
if(!token) location.href = "/login";

const socket = io();
const messagesDiv = document.getElementById("messages");
const msgInput = document.getElementById("msg");

socket.emit("auth", token);

document.getElementById("sendBtn").onclick = () => {
  if(!msgInput.value) return;
  socket.emit("message", { token, text: msgInput.value });
  msgInput.value = "";
};

msgInput.addEventListener("keydown", e => {
  if(e.key === "Enter") document.getElementById("sendBtn").click();
});

socket.on("message", m => {
  const div = document.createElement("div");
  div.textContent = m.user + ": " + m.text;
  messagesDiv.appendChild(div);
  messagesDiv.scrollTop = messagesDiv.scrollHeight;
});
</script>
</body>
</html>`);
});
});

io.on("connection", socket => {
  socket.on("auth", token => {
    try{
      jwt.verify(token, SECRET);
      messages.forEach(m => socket.emit("message", m));
    }catch{}
  });

  socket.on("message", ({ token, text }) => {
    try{
      const data = jwt.verify(token, SECRET);
      const msg = { user: data.username, text };
      messages.push(msg);
      io.emit("message", msg);
    }catch{}
  });
});

server.listen(3000, () => console.log("Server running on http://localhost:3000"));
