<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Fluffy Chat</title>
<style>
  body {
    font-family: Arial, sans-serif;
    background: #f0f0f5;
    display: flex;
    justify-content: center;
    align-items: center;
    height: 100vh;
    margin: 0;
  }
  #chat-container {
    width: 100%;
    max-width: 500px;
    height: 600px;
    background: white;
    border-radius: 12px;
    box-shadow: 0 4px 20px rgba(0,0,0,0.2);
    display: flex;
    flex-direction: column;
    overflow: hidden;
  }
  #messages {
    flex: 1;
    padding: 16px;
    overflow-y: auto;
    border-bottom: 1px solid #ccc;
  }
  .message {
    margin-bottom: 12px;
    padding: 8px 12px;
    border-radius: 8px;
    max-width: 80%;
    word-wrap: break-word;
  }
  .user {
    background: #d1e7ff;
    align-self: flex-end;
  }
  .bot {
    background: #e2e2e2;
    align-self: flex-start;
  }
  #input-area {
    display: flex;
    padding: 12px;
    background: #f7f7f7;
  }
  #input-area input {
    flex: 1;
    padding: 10px;
    border-radius: 8px;
    border: 1px solid #ccc;
  }
  #input-area button {
    margin-left: 8px;
    padding: 10px 16px;
    border: none;
    border-radius: 8px;
    background: #4a90e2;
    color: white;
    cursor: pointer;
  }
</style>
</head>
<body>
<div id="chat-container">
  <div id="messages"></div>
  <div id="input-area">
    <input type="text" id="chat-input" placeholder="Type a message...">
    <button onclick="sendMessage()">Send</button>
  </div>
</div>

<script>
  const messagesDiv = document.getElementById('messages');
  const chatInput = document.getElementById('chat-input');

  // Load previous chat from localStorage
  const chatHistory = JSON.parse(localStorage.getItem('chatHistory') || '[]');
  chatHistory.forEach(msg => addMessage(msg.text, msg.type));

  function addMessage(text, type) {
    const div = document.createElement('div');
    div.className = 'message ' + type;
    div.innerText = text;
    messagesDiv.appendChild(div);
    messagesDiv.scrollTop = messagesDiv.scrollHeight;
  }

  function sendMessage() {
    const text = chatInput.value.trim();
    if (!text) return;
    
    addMessage(text, 'user');
    chatInput.value = '';
    
    // Save to localStorage
    chatHistory.push({ text, type: 'user' });
    localStorage.setItem('chatHistory', JSON.stringify(chatHistory));
    
    // Simple bot reply
    setTimeout(() => {
      const reply = generateReply(text);
      addMessage(reply, 'bot');
      chatHistory.push({ text: reply, type: 'bot' });
      localStorage.setItem('chatHistory', JSON.stringify(chatHistory));
    }, 500);
  }

  function generateReply(userMessage) {
    // Very basic simulated reply
    if (userMessage.toLowerCase().includes('hello')) return 'Hi there!';
    if (userMessage.toLowerCase().includes('brawl')) return 'Brawl Stars sounds fun!';
    return 'I see... tell me more.';
  }

  chatInput.addEventListener('keypress', function(e) {
    if (e.key === 'Enter') sendMessage();
  });
</script>
</body>
</html>

