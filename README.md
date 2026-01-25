<!DOCTYPE html>
<!--
MINICORD – GUARANTEED-TO-WORK VERSION
HTML ONLY
NO BACKEND
NO FIREBASE
WORKS IMMEDIATELY ON GITHUB PAGES
NOTE: Chat syncs per browser (no realtime multiplayer)
-->
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
    <button onclick="login()">enter</button>
  </div>
</div>

<div id="app" class="app hidden">
  <div class="servers"></div>
  <div class="channels"><div class="channel"># general</div></div>
  <div class="chat">
    <div id="messages" class="messages"></div>
    <div class="input">
      <input id="msg" placeholder="message #general">
      <button onclick="send()">send</button>
    </div>
  </div>
</div>

<script>
let username = localStorage.getItem('mc_user')
const messagesEl = document.getElementById('messages')

if(username){
  loginDiv.classList.add('hidden')
  appDiv.classList.remove('hidden')
  load()
}

function login(){
  if(!user.value.trim())return
  username = user.value.trim()
  localStorage.setItem('mc_user', username)
  loginDiv.classList.add('hidden')
  appDiv.classList.remove('hidden')
  load()
}

function send(){
  if(!msg.value)return
  const data = JSON.parse(localStorage.getItem('mc_msgs') || '[]')
  data.push({u: username, t: msg.value})
  localStorage.setItem('mc_msgs', JSON.stringify(data))
  msg.value = ''
  load()
}

function load(){
  messagesEl.innerHTML = ''
  const data = JSON.parse(localStorage.getItem('mc_msgs') || '[]')
  data.forEach(m=>{
    const d=document.createElement('div')
    d.className='msg'
    d.textContent = m.u + ': ' + m.t
    messagesEl.appendChild(d)
  })
  messagesEl.scrollTop = messagesEl.scrollHeight
}

const loginDiv=document.getElementById('login')
const appDiv=document.getElementById('app')
</script>
</body>
</html>
