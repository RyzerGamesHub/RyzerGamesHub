<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Discord Layout</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            margin: 0;
            display: flex;
            height: 100vh;
        }
        .server-list {
            width: 60px;
            background-color: #2f3136;
            display: flex;
            flex-direction: column;
            align-items: center;
            padding-top: 10px;
        }
        .server-icon {
            width: 40px;
            height: 40px;
            border-radius: 50%;
            margin: 5px 0;
            background-color: #7289da;
            position: relative;
        }
        .channel-list {
            flex: 1;
            background-color: #36393f;
            padding: 10px;
            display: flex;
            flex-direction: column;
        }
        .channel-category {
            margin: 10px 0;
            color: #b9bbbe;
        }
        .channel {
            padding: 5px;
            color: #ffffff;
            cursor: pointer;
        }
        .main-panel {
            flex: 3;
            background-color: #2f3136;
            display: flex;
            flex-direction: column;
        }
        .message-log {
            flex: 1;
            overflow-y: auto;
            padding: 10px;
            color: #ffffff;
        }
        .message {
            margin: 5px 0;
        }
        .header {
            background-color: #202225;
            padding: 10px;
            color: #ffffff;
        }
        .message-composer {
            background-color: #40444b;
            padding: 10px;
            display: flex;
            align-items: center;
        }
        .member-list {
            width: 250px;
            background-color: #2f3136;
            padding: 10px;
            color: #ffffff;
            display: none; /* Optional */
        }
    </style>
</head>
<body>
    <div class="server-list">
        <div class="server-icon"></div>
        <div class="server-icon"></div>
        <div class="server-icon"></div>
    </div>
    <div class="channel-list">
        <div class="channel-category">Text Channels</div>
        <div class="channel">#general</div>
        <div class="channel">#random</div>
        <div class="channel-category">Voice Channels</div>
        <div class="channel">General</div>
    </div>
    <div class="main-panel">
        <div class="header">#general</div>
        <div class="message-log">
            <div class="message">User1: Hello!</div>
            <div class="message">User2: Hi there!</div>
        </div>
        <div class="message-composer">
            <input type="text" placeholder="Message #general" style="flex: 1; padding: 5px;">
        </div>
    </div>
    <div class="member-list">
        <div>Member1</div>
        <div>Member2</div>
    </div>
</body>
</html>
 
