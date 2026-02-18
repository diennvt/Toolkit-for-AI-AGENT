---
name: Real-time
description: Real-time - WebSocket, SSE, polling, pub/sub patterns, live notifications
---

# Real-time Features

## Tổng quan

Skill kit này hướng dẫn implement tính năng real-time: WebSocket, Server-Sent Events, polling, và pub/sub patterns.

---

## Checklist

- [ ] Chọn technology (WebSocket / SSE / Polling)
- [ ] Setup WebSocket server (Socket.IO / ws)
- [ ] Implement connection management
- [ ] Implement rooms/channels
- [ ] Handle reconnection
- [ ] Authentication cho WebSocket
- [ ] Implement heartbeat/ping-pong

---

## Hướng dẫn chi tiết

### Chọn Technology

| Technology | Khi nào dùng | Ví dụ |
|-----------|-------------|-------|
| **WebSocket** | Two-way, high frequency | Chat, gaming, collaboration |
| **SSE** | Server → Client only | Notifications, live feed |
| **Polling** | Simple, low frequency | Dashboard refresh, status check |
| **Long Polling** | Fallback cho WebSocket | Compatibility |

### Socket.IO Setup

```typescript
// Server
import { Server } from 'socket.io';

const io = new Server(httpServer, {
  cors: { origin: process.env.CLIENT_URL },
});

// Authentication middleware
io.use((socket, next) => {
  const token = socket.handshake.auth.token;
  try {
    const user = verifyToken(token);
    socket.data.user = user;
    next();
  } catch (err) {
    next(new Error('Authentication failed'));
  }
});

io.on('connection', (socket) => {
  console.log(`User connected: ${socket.data.user.id}`);

  // Join room
  socket.join(`user:${socket.data.user.id}`);

  // Listen for messages
  socket.on('chat:message', async (data) => {
    const message = await saveMessage(data);
    io.to(`room:${data.roomId}`).emit('chat:message', message);
  });

  // Handle disconnect
  socket.on('disconnect', () => {
    console.log(`User disconnected: ${socket.data.user.id}`);
  });
});

// Client
import { io } from 'socket.io-client';

const socket = io(API_URL, {
  auth: { token: getAccessToken() },
  reconnection: true,
  reconnectionDelay: 1000,
  reconnectionAttempts: 5,
});

socket.on('chat:message', (message) => {
  addMessageToUI(message);
});

socket.emit('chat:message', { roomId: '1', content: 'Hello!' });
```

### Server-Sent Events (SSE)

```typescript
// Server
app.get('/api/notifications/stream', authenticate, (req, res) => {
  res.setHeader('Content-Type', 'text/event-stream');
  res.setHeader('Cache-Control', 'no-cache');
  res.setHeader('Connection', 'keep-alive');

  const sendEvent = (data) => {
    res.write(`data: ${JSON.stringify(data)}\n\n`);
  };

  // Send notifications
  const listener = (notification) => sendEvent(notification);
  notificationEmitter.on(`user:${req.user.id}`, listener);

  req.on('close', () => {
    notificationEmitter.off(`user:${req.user.id}`, listener);
  });
});

// Client
const eventSource = new EventSource('/api/notifications/stream');

eventSource.onmessage = (event) => {
  const notification = JSON.parse(event.data);
  showNotification(notification);
};
```

---

## Common Pitfalls

| ❌ Sai | ✅ Đúng |
|--------|---------|
| WebSocket cho mọi thứ | SSE cho one-way, Polling cho simple cases |
| Không auth WebSocket connections | Verify token ở handshake |
| Không handle reconnection | Auto-reconnect với backoff |
| Gửi quá nhiều events | Throttle/debounce events |
| Không cleanup listeners | Remove listeners khi disconnect |

---

## Liên kết

- **Skill liên quan:** [backend-development](../backend-development/SKILL.md), [frontend-development](../frontend-development/SKILL.md)
