<!DOCTYPE html>
<!--
MINICORD – HTML-ONLY VERSION (WORKS ON GITHUB PAGES / RENDER STATIC)
Uses Firebase Auth + Realtime Database
NO SERVER, NO NODE
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
    <input id="email" placeholder="email">
    <input id="password" type="password" placeholder="password">
    <button onclick="login()">login / register</button>
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

<!-- Firebase -->
<script src="https://www.gstatic.com/firebasejs/9.23.0/firebase-app-compat.js"></script>
<script src="https://www.gstatic.com/firebasejs/9.23.0/firebase-auth-compat.js"></script>
<script src="https://www.gstatic.com/firebasejs/9.23.0/firebase-database-compat.js"></script>

<script>
// 🔴 REPLACE WITH YOUR FIREBASE CONFIG
const firebaseConfig = {
  apiKey: "YOUR_API_KEY",
  authDomain: "YOUR_PROJECT.firebaseapp.com",
  databaseURL: "https://YOUR_PROJECT.firebaseio.com",
  projectId: "YOUR_PROJECT",
};

firebase.initializeApp(firebaseConfig)
const auth = firebase.auth()
const db = firebase.database()

const loginDiv = document.getElementById('login')
const appDiv = document.getElementById('app')
const messages = document.getElementById('messages')

function login(){
  const e = email.value
  const p = password.value
  auth.signInWithEmailAndPassword(e,p).catch(()=>{
    return auth.createUserWithEmailAndPassword(e,p)
  })
}

auth.onAuthStateChanged(user=>{
  if(!user) return
  loginDiv.classList.add('hidden')
  appDiv.classList.remove('hidden')

  db.ref('messages').limitToLast(100).on('child_added',snap=>{
    const m=snap.val()
    const d=document.createElement('div')
    d.className='msg'
    d.textContent=m.user+': '+m.text
    messages.appendChild(d)
    messages.scrollTop=messages.scrollHeight
  })
})

function send(){
  if(!msg.value)return
  db.ref('messages').push({
    user: auth.currentUser.email,
    text: msg.value
  })
  msg.value=''
}
</script>
</body>
</html>
