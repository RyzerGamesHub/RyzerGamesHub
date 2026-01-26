<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Fluffy Chat</title>
<style>
  /* Full-screen background */
  body {
    font-family: Arial, sans-serif;
    background: linear-gradient(135deg, #c3f0f7, #e0c3fc);
    display: flex;
    justify-content: center;
    align-items: center;
    min-height: 100vh;
    margin: 0;
  }

  /* Chat container adapts to desktop or mobile */
  #chat-container {
    width: 90%;
    max-width: 800px;
    height: 80vh;
    background: rgba(255, 255, 255, 0.95);
    border-radius: 12px;
    box-shadow: 0 6px 25px rgba(0,0,0,0.25);
    display: flex;
    flex-direction: column;
    overflow: hidden;
  }

  #messages {
    flex: 1;
    padding: 16px;
    overflow-y: auto;
  }

  .message {
    margin-bottom: 12px;
    padding: 10px 14px;
    border-radius: 12px;
    max-width: 70%;
    word-wrap: break-word;
    animation: fadeIn 0.2s ease-out;
  }

  .user {
    background: #d1e7ff;
    align-self: flex-end;
  }

  .bot {
    background: #f0e2ff;
    align-self: flex-start;
  }

  #input-area {
    display: flex;
    padding: 12px;
    background: #f7f7f7;
    border-top: 1px solid #ccc;
  }

  #input-area input {
    flex: 1;
    padding: 10px;
    border-radius: 12px;
    border: 1px solid #ccc;
  }

  #input-area button {
    margin-left: 8px;
    padding: 10px 16px;
    border: none;
    border-radius: 12px;
    background: #8a63d2;
    color: white;
    cursor: pointer;
    transition: background 0.3s;
  }

  #input-area button:hover {
    background: #693bb5;
  }

  @keyframes fadeIn {
    from {opacity: 0;}
    to {opacity: 1;}
  }

  /* Optional: make chat look nice on small screens */
  @media (max-width: 600px) {
    #chat-container {
      width: 100%;
      height: 95vh;
      border-radius: 0;
    }
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
  
  chatHistory.push({ text, type: 'user' });
  localStorage.setItem('chatHistory', JSON.stringify(chatHistory));
  
  setTimeout(() => {
    const reply = generateReply(text);
    addMessage(reply, 'bot');
    chatHistory.push({ text: reply, type: 'bot' });
    localStorage.setItem('chatHistory', JSON.stringify(chatHistory));
  }, 500);
}

function generateReply(userMessage) {
  const responses = [
    "Interesting, tell me more!",
    "I see... what else?",
    "That sounds fun!",
    "Hmm, I'm not sure about that.",
    "Can you explain a bit more?",
    "Wow, really?",
    "Haha, that's cool!",
    "I understand."
  ];

  // Keyword-based replies
  const lower = userMessage.toLowerCase();
  if (lower.includes('hello') || lower.includes('hi')) return "Hi there!";
  if (lower.includes('brawl')) return "Brawl Stars sounds fun!";
  if (lower.includes('fluffy')) return "Hey! You just mentioned me 😄";

  // Random fallback
  return responses[Math.floor(Math.random() * responses.length)];
}

chatInput.addEventListener('keypress', function(e) {
  if (e.key === 'Enter') sendMessage();
});
</script>

</body>
</html>
