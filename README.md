const express = require("express");
const http = require("http");
const { Server } = require("socket.io");

const app = express();
const server = http.createServer(app);
const io = new Server(server);

app.use(express.urlencoded({ extended: true }));
app.use(express.json());

const users = new Set();
const messages = [];

app.get("/", (req, res) => res.redirect("/login"));

app.get("/login", (req, res) => {
  res.send(`<!DOCTYPE html>
<html>
<head>
<title>Login</title>
<style>
body{margin:0;background:#2b2d31;color:#fff;font-family:Arial;height:100vh;display:flex;align-items:center;justify-content:center}
form{background:#1e1f22;padding:24px;border-radius:8px;width:260px}
input,button{width:100%;padding:10px;margin-top:10px;font-size:16px}
button{background:#5865f2;border:none;color:white;cursor:pointer}
</style>
</head>
<body>
<form method="POST" action="/login">
  <input name="username" placeholder="username" required />
  <button type="submit">Enter Chat</button>
</form>
</body>
</html>`);
});

app.post("/login", (req, res) => {
  const { username } = req.body;
  if (!username) return res.redirect("/login");
  users.add(username);
  res.redirect(`/chat?u=${encodeURIComponent(username)}`);
});

app.get("/chat", (req, res) => {
  const user = req.query.u;
  if (!user) return res.redirect("/login");

  res.send(`<!DOCTYPE html>
<html>
<head>
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
button{background:#5865f2;border:none;color:white;padding:10px;cursor:pointer}
</style>
</head>
<body>
<div id="servers"></div>
<div id="channels"></div>
<div id="main">
  <div id="messages"></div>
  <div id="input">
    <input id="msg" placeholder="Message" />
    <button id="send">Send</button>
  </div>
</div>
<script>
const username = ${JSON.stringify(user)};
const socket = io();
const messagesDiv = document.getElementById("messages");
const msgInput = document.getElementById("msg");

socket.emit("join", username);

socket.on("message", m => {
  const d = document.createElement("div");
  d.textContent = m.user + ": " + m.text;
  messagesDiv.appendChild(d);
  messagesDiv.scrollTop = messagesDiv.scrollHeight;
});

function send(){
  if(!msgInput.value) return;
  socket.emit("message", { user: username, text: msgInput.value });
  msgInput.value = "";
}

document.getElementById("send").onclick = send;
msgInput.addEventListener("keydown", e => { if(e.key === "Enter") send(); });
</script>
</body>
</html>`);
});

io.on("connection", socket => {
  messages.forEach(m => socket.emit("message", m));

  socket.on("message", msg => {
    messages.push(msg);
    io.emit("message", msg);
  });
});

server.listen(3000, () => console.log("Running at http://localhost:3000"));
