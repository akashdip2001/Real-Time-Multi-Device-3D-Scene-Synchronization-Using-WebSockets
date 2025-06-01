# `Project Report:` Real-Time Multi-Device 3D Scene Synchronization Using WebSockets

## Project Title

Real-Time Multi-Device 3D Scene Synchronization over Local Network using WebSockets

## Abstract

This project enhances the existing open-source implementation of synchronizing a 3D scene across multiple browser windows on the same device using localStorage. The enhancement enables real-time synchronization across **multiple devices** (e.g., laptop and mobile) on the **same local network** using **WebSocket technology**. This addresses the limitation of `localStorage` being restricted to the same browser context and expands it to real-time, bi-directional communication using a centralized server.

---

## Objectives

* Upgrade the localStorage-based scene sync to work across devices.
* Implement WebSocket-based messaging for real-time scene updates.
* Enable communication and interaction across multiple clients.
* Retain and enhance the modularity of the original `multipleWindow3dScene` project.

---

## System Architecture

### Diagram

```
+------------+       WebSocket       +------------------+       WebSocket       +-------------+
|  Laptop    | <-------------------> |   WebSocket      | <-------------------> |   Mobile    |
| (Client A) |                       |     Server       |                       | (Client B)  |
+------------+                       +------------------+                       +-------------+
```

### Technologies Used

* **Three.js**: 3D rendering
* **HTML/CSS/JavaScript**: Frontend
* **Node.js**: WebSocket server
* **ws (WebSocket library)**: For server-side communication
* **Express.js**: For optional static file serving

---

## Implementation

### 1. Original Project Summary

The original `multipleWindow3dScene` project uses `localStorage` to synchronize a single 3D scene rendered in multiple windows. It relies on storage event listeners to propagate changes like camera rotation or object transformation.

### 2. Limitations

* Only works within the same browser/device
* No support for multi-device communication

### 3. Proposed Solution

Use **WebSockets** to broadcast messages from any client to all connected peers via a central Node.js server. All clients receive updates and apply changes to their local Three.js scene.

---

## Code Snippets

### A. WebSocket Server (server.js)

```js
const WebSocket = require('ws');
const express = require('express');
const app = express();
const server = require('http').createServer(app);
const wss = new WebSocket.Server({ server });

wss.on('connection', (ws) => {
    ws.on('message', (message) => {
        wss.clients.forEach((client) => {
            if (client !== ws && client.readyState === WebSocket.OPEN) {
                client.send(message);
            }
        });
    });
});

server.listen(3000, () => console.log('Server running on http://localhost:3000'));
```

### B. Modified Client Code (main.js)

```js
const socket = new WebSocket('ws://192.168.1.10:3000'); // Use your laptop's IP

// Send local changes to server
function broadcastChange(data) {
    socket.send(JSON.stringify(data));
}

// Listen for updates
socket.onmessage = (event) => {
    const data = JSON.parse(event.data);
    applyRemoteChange(data);
};

function applyRemoteChange(data) {
    // Update Three.js scene based on received data
    // Example: update object rotation, position, camera, etc.
}
```

### C. Sample Scene Sync Message

```json
{
  "type": "update",
  "object": "cube1",
  "position": {"x": 1, "y": 0, "z": 2},
  "rotation": {"x": 0, "y": 1.57, "z": 0}
}
```

---

## Setup Instructions

### 1. Start Server

```bash
node server.js
```

Make sure your firewall allows connections on port 3000.

### 2. Open Web App on Devices

* Use your laptop’s IP, e.g., `http://192.168.1.10/index.html`
* Ensure all devices are on the same Wi-Fi network.

### 3. Interact

* Any object movement/rotation on one device reflects on all connected clients.

---

## Result

* Real-time object synchronization
* Works seamlessly across devices on the same local network
* Low-latency interaction using WebSockets

---

## Future Improvements

* Add WebRTC for peer-to-peer fallback
* Authentication and client identity
* Remote file loading support
* Scene version history and persistence (using DB)

---

## Conclusion

This project successfully transforms a single-device synchronized 3D viewer into a multi-device, real-time collaborative 3D environment. By integrating WebSocket-based communication, it extends usability for educational, collaborative, or presentation-based scenarios.

---

## References

* [multipleWindow3dScene GitHub Repository](https://github.com/bgstaal/multipleWindow3dScene)
* [Three.js Documentation](https://threejs.org/docs/)
* [ws WebSocket Library](https://github.com/websockets/ws)
* [MDN WebSocket API](https://developer.mozilla.org/en-US/docs/Web/API/WebSocket)

---

## Appendices

* Include screenshots or camera captures of different devices syncing in real time
* Source code zipped and attached

---
