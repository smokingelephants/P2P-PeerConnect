```markdown
# Multichat: Peer-to-Peer Minimal Chat Application

This project is a minimal, browser-based peer-to-peer chat application built using the [PeerJS library](https://peerjs.com/). It supports multiple users connecting and chatting in real-time, with a basic acknowledgment mechanism for message delivery.

---

## Features

- **Peer-to-Peer Connections:** Users connect directly using PeerJS.
- **Dynamic Chat Rooms:** Automatically connects new users to all existing participants.
- **Message Acknowledgment:** Ensures reliable message delivery using acknowledgments and retries.
- **Browser-Only Application:** No server backend required, runs entirely in the browser.
- **User-Friendly Interface:** Easy login, connection, and messaging.

---

## Usage

### 1. Clone or Download the Project
Download the HTML file or clone the repository if hosted on a version control platform.

### 2. Prerequisites
- A modern browser with JavaScript enabled.
- Access to the internet for loading the PeerJS library.

### 3. Run the Application
Open the HTML file in any browser to launch the app.

---

## How It Works

### **Login**
1. Enter your username in the `Login` section.
2. Click **Login** to join the chat.
3. Your username is saved in the browser for the session.

---

### **Connecting to Other Users**
1. Enter another user's username in the **Connect to User** input box.
2. Click **Connect** to establish a peer-to-peer connection.

The system automatically:
- Maps usernames to unique PeerJS IDs.
- Maintains a list of connected users.
- Broadcasts new connections to all existing peers, ensuring everyone is in the same chat room.

---

### **Messaging**
1. Type a message in the input box under the **Chat** section.
2. Click **Send** to broadcast your message to all connected users.

---

### **Message Acknowledgment and Reliability**
- Each message has a unique SHA-256 hash.
- Peers send acknowledgment messages (ACK) for received messages.
- Unacknowledged messages are retried up to 10 times, with a 5-second interval between retries.
- If retries fail, the connection is re-established, and the message is re-sent.

---

## Code Highlights

### **Peer Management**
- **Peer ID Conversion:**
  ```javascript
  const appPrefix = "p2p-multichat-test-";
  function getPeerId(username) {
      return appPrefix + username;
  }
  function getUsername(peerId) {
      return peerId ? peerId.slice(appPrefix.length) : "";
  }
  ```
- **Connection Management:**
  - Adding/Removing connections:
    ```javascript
    function addConnection(conn) {
        connections[conn.peer] = conn;
        updatePeerIds();
    }
    function removeConnection(conn) {
        delete connections[conn.peer];
        updatePeerIds();
    }
    ```

### **Message Handling**
- **Hash Generation for Messages:**
  ```javascript
  async function sha256(message) {
      const msgBuffer = new TextEncoder().encode(message);
      const hashBuffer = await crypto.subtle.digest('SHA-256', msgBuffer);
      return Array.from(new Uint8Array(hashBuffer))
                  .map(b => b.toString(16).padStart(2, '0'))
                  .join('');
  }
  ```

- **Sending Messages with Retries:**
  ```javascript
  const ACK_TIMEOUT = 5000;
  const MAX_RETRIES = 10;

  const sendWithRetry = (conn, retriesLeft) => {
      conn.send({ type: "chat", chat });
      pendingAcks[messageHash] = { /* Retry Logic */ };
  };
  ```

- **Receiving Messages:**
  ```javascript
  function receiveChat(chat) {
      chats[chat.hash] = chat;
      document.getElementById('chatbox').innerHTML = Object.keys(chats)
          .map(key => `<p>${chat.sender} : ${chat.message}</p>`)
          .join('');
  }
  ```

---

## Limitations
- Requires manual user connection.
- No encryption beyond SHA-256 for message acknowledgment.
- Not suitable for highly scalable use cases.

---

## Future Enhancements
- Automatic user discovery using signaling servers.
- Enhanced security with end-to-end encryption.
- Persistent storage for chat history.
- Improved UI/UX for better user interaction.

---

## Acknowledgments
This project uses the [PeerJS](https://peerjs.com/) library for seamless WebRTC integration.
```
