# Tiny WebSocket Kit

A minimal client-server WebSocket utility with:
- Reconnect & Backoff
- Acknowledgement support
- One-time event listeners
- Connection status
- Optional debug logging

## Getting Started

📘 Tutorial: Building WebSocket Apps with websocket-utils-client-server
This tutorial walks you through building a real-time WebSocket-powered app using the websocket-utils-client-server package — a lightweight utility for seamless event-based communication.

✅ Prerequisites
Make sure you have the following installed:

Node.js (v14 or newer)

A web browser

A code editor (e.g., VS Code)

1. 📦 Install the Package
Initialize a new Node.js project:

bash
Copy
Edit
mkdir websocket-demo && cd websocket-demo
npm init -y
npm install websocket-utils-client-server
Create your folders:

bash
Copy
Edit
mkdir src examples
2. 🛠️ Setup Server
Create the file: examples/server.js

js
Copy
Edit
import { createServer } from 'websocket-utils-client-server/server.js';

const wss = createServer({ port: 8080 });

wss.onClient((client) => {
  console.log('✅ New client connected');

  client.onEvent('ping', (data) => {
    console.log('🔔 Ping received:', data);
    client.emit('pong', { msg: 'pong!', serverTime: Date.now() });
  });
});
Run the server:

bash
Copy
Edit
node examples/server.js
3. 🌐 Setup Client (Browser)
Create the file: examples/client.html

html
Copy
Edit
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <title>WebSocket Demo</title>
</head>
<body>
  <h1>🔗 WebSocket Client</h1>
  <button id="sendPing">Send Ping</button>
  <pre id="log"></pre>

  <script type="module">
    import { createClient } from '../src/client.js';

    const log = (msg) => {
      document.getElementById('log').textContent += msg + '\n';
    };

    const socket = createClient('ws://localhost:8080', {
      debug: true,
      reconnect: true,
      maxRetries: 5
    });

    socket.on('connect', () => {
      log('✅ Connected to server!');
    });

    socket.on('disconnect', () => {
      log('❌ Disconnected from server.');
    });

    socket.on('pong', (data) => {
      log('📥 Received pong: ' + JSON.stringify(data));
    });

    document.getElementById('sendPing').onclick = () => {
      socket.emit('ping', { clientTime: Date.now() });
      log('📤 Sent ping');
    };
  </script>
</body>
</html>
Open the file in your browser (file://...) or use Live Server extension in VS Code.

4. 🧪 Advanced Features
🔁 Emit with Acknowledgment
Client:

js
Copy
Edit
socket.emitWithAck('saveData', { name: 'Test' })
  .then(response => console.log('✅ Ack:', response))
  .catch(err => console.error('❌ Timeout or Error:', err));
Server:

js
Copy
Edit
client.onEvent('saveData', (data) => {
  console.log('Saving data:', data);
  client.emit(`saveData_ack_${data._ackId}`, { success: true });
});
🧠 One-time Listeners
js
Copy
Edit
socket.once('config', (cfg) => {
  console.log('Loaded config:', cfg);
});
💓 Ping/Pong (Auto)
Handled internally: the server sends pings and expects pong responses to keep the connection alive.

🛠️ Get Connection Status
js
Copy
Edit
console.log('Connection Status:', socket.getStatus()); // "connected" or "disconnected"
🧼 Clean Up Listeners
js
Copy
Edit
socket.off('eventName'); // remove all listeners for that event
🎯 Summary
The websocket-utils-client-server package helps you:

Build real-time web apps without boilerplate

Enable auto-reconnect, acknowledgment-based messaging, and event-driven communication

Enjoy a clean and modern API for WebSocket interactions

💬 Have Questions or Suggestions?
Create an issue or open a PR on GitHub. Contributions are welcome!

Open `client.html` in the browser to test.

## License
MIT
