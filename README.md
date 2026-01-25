// ======================= package.json =======================
{
  "name": "minicord",
  "version": "1.0.0",
  "main": "server.js",
  "scripts": {
    "start": "node server.js"
  },
  "dependencies": {
    "express": "^4.18.2",
    "socket.io": "^4.7.5"
  }
}

// ======================= server.js =======================
const express = require('express')
const app = express()
const http = require('http').createServer(app)
const io = require('socket.io')(http)

const users = {}

app.use(express.static(__dirname))

io.on('connection', socket => {
  let user

  socket.on('login', data => {
    if (users[data.u] && users[data.u] !== data.p) {
      socket.disconnect()
      return
    }
    users[data.u] = data.p
    user = data.u
  })

  socket.on('msg', text => {
    if (!user) return
    io.emit('msg', { u: user, t: text })
  })
})

const PORT = process.env.PORT || 3000
http.listen(PORT)

// ======================= index.html =======================
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<title>MiniCord</title>
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<style>
*{box-sizing:border-box;font-family:Arial}
body{margin:0;background:#2b2d31;color:#fff}
.hidden{display:none}
.login{height:100vh;display:flex;align-items:center;justify-content:center}
.login-box{background:#1e1f22;padding:30px;border-radius:8px}
.login-box input{display:block;margin:10px 0;padding:10px;width:220px}
.app{display:flex;height:100vh}
.servers{width:70px;background:#1e1f22}
.channels{width:220px;background:#2b2d31;padding:10px}
.channel{padding:6px;border-radius:4px}
.chat{flex:1;display:flex;flex-direction:column;background:#313338}
.messages{flex:1;overflow:auto;padding:10px}
.msg{margin-bottom:6px}
.input{display:flex;padding:10px;background:#383a40}
input{flex:1;padding:10px;border:none;border-radius:4px}
button{margin-left:8px;padding:10px;background:#5865f2;border:0;color:#fff;border-radius:4px}
</style>
</head>
<body>

<div id="login" class="login">
  <div class="login-box">
    <input id="user" placeholder="username">
    <input id="pass" type="password" placeholder="password">
    <button onclick="login()">login / register</button>
  </div>
</div>

<div id="app" class="app hidden">
  <div class="servers"></div>
  <div class="channels">
    <div class="channel"># general</div>
  </div>
  <div class="chat">
    <div id="messages" class="messages"></div>
    <div class="input">
      <input id="msg" placeholder="message #general">
      <button onclick="send()">send</button>
    </div>
  </div>
</div>

<script src="/socket.io/socket.io.js"></script>
<script>
let socket, username
const loginDiv=document.getElementById('login')
const appDiv=document.getElementById('app')
const messages=document.getElementById('messages')

function login(){
  username=user.value.trim()
  if(!username||!pass.value)return
  loginDiv.classList.add('hidden')
  appDiv.classList.remove('hidden')
  socket=io()
  socket.emit('login',{u:username,p:pass.value})
  socket.on('msg',m=>{
    const d=document.createElement('div')
    d.className='msg'
    d.textContent=m.u+': '+m.t
    messages.appendChild(d)
    messages.scrollTop=messages.scrollHeight
  })
}

function send(){
  if(!msg.value)return
  socket.emit('msg',msg.value)
  msg.value=''
}
</script>
</body>
</html>
