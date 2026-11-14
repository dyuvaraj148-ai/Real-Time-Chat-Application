# 💬 Social Chat App

![Java](https://img.shields.io/badge/Java-ED8B00?style=flat&logo=openjdk&logoColor=white)
![JavaFX](https://img.shields.io/badge/JavaFX-007396?style=flat&logo=java&logoColor=white)
![MySQL](https://img.shields.io/badge/MySQL-4479A1?style=flat&logo=mysql&logoColor=white)
![BCrypt](https://img.shields.io/badge/BCrypt-6DB33F?style=flat&logo=springsecurity&logoColor=white)
![FastAPI](https://img.shields.io/badge/FastAPI-009688?style=flat&logo=fastapi&logoColor=white)
![TCP Sockets](https://img.shields.io/badge/TCP%20Sockets-FF6B35?style=flat&logo=cloudflare&logoColor=white)
![CSS3](https://img.shields.io/badge/CSS3-1572B6?style=flat&logo=css3&logoColor=white)
![FXML](https://img.shields.io/badge/FXML-007396?style=flat&logo=java&logoColor=white)

> A JavaFX desktop social chat application built on a custom TCP socket protocol, with real-time messaging, file transfer, group chat, and AI-powered smart reply suggestions.

📦 **Repository:** [github.com/dyuvaraj148-ai/Real-Time-Chat-Application](https://github.com/dyuvaraj148-ai/Real-Time-Chat-Application)

---

## 🌟 Overview

**Social Chat App** is a desktop messaging application that lets users sign up, log in, chat in real time, send files, and create group conversations. It is built as a JavaFX client connected to a Java TCP server, with MySQL used for storing user and chat data.

In simple terms, this project is a mini social messaging system similar to a basic chat app. The client handles the user interface, the server manages connections and message routing, and the shared `common` module defines the rules for communication between both sides.

The application supports direct chat, offline message delivery, file sharing, voice notes, and group chat. It also includes a smart-reply feature that calls a local FastAPI service for suggested responses.

---

## ✨ Highlights

- 🖥️ **JavaFX desktop UI** with multi-panel layout (sidebar, chat area, media/info panel)
- 🔌 **Custom frame-based TCP socket protocol** using `Frame` and `FrameIO`
- 🗄️ **MySQL-backed persistence** with BCrypt password hashing
- 📬 **Offline message delivery** — messages queued and delivered on reconnect
- 📁 **Chunked file transfer** — up to 25 MB, 64 KB chunks, with image/audio/video/document classification
- 🎙️ **Voice message recording** with preview before sending (max 30 seconds)
- 👥 **Group chat** with member management and owner reassignment
- 🤖 **Smart reply integration** via a local FastAPI endpoint

---
## ✅ Features

### 🔐 Authentication & User Management
- Register and log in with username/password
- Avatar selection during registration
- BCrypt password hashing
- Online/offline presence display with last seen timestamp
- User list loading in sidebar

### 💬 Messaging
- Real-time direct messaging over TCP
- Offline queued message delivery
- Conversation history loading
- Message reply, edit, and delete
- Message search

### 📁 File & Media Messaging
- Upload and download files (max **25 MB**, **64 KB** chunks)
- Image, audio, video, and document classification
- Voice message recording and sending (max **30 seconds**) with preview
- In-memory media playback via local HTTP streaming helper
- Media and document panel in the right sidebar
- Image, video, and document preview and download

### 👥 Group Chat
- Create groups, add and remove members
- Delete group
- List members
- Group conversation history loading
- Group messaging
- Owner reassignment when the owner leaves and members remain

### 🤖 Smart Reply
- Server requests smart reply suggestions from a local FastAPI endpoint at `http://localhost:8000/smart-reply`
- Suggestions are forwarded to the client on success

---

## 🛠️ Tech Stack

| Layer | Technology |
|-------|------------|
| Language | Java |
| UI Framework | JavaFX + FXML |
| UI Styling | CSS (light & dark theme support) |
| Networking | TCP Sockets — custom `Frame` / `FrameIO` protocol |
| Database | MySQL (`socialchatapp` database) |
| Security | BCrypt password hashing |
| Media Playback | JavaFX Media APIs + `InMemoryMediaServer` (local HTTP) |
| Smart Reply | Local FastAPI endpoint (`http://localhost:8000/smart-reply`) |

---

## 🏗️ Architecture

The repository is organized into three main layers:

```
┌─────────────────────────────────────────┐
│              JavaFX Client              │
│  (UI Controllers · ClientConnection)    │
└─────────────────┬───────────────────────┘
                  │ TCP · Custom Frame Protocol
                  │ Port 5000
                  ▼
┌─────────────────────────────────────────┐
│           Java Socket Server            │
│  (ClientHandler · DAOs)                 │
└──────────┬──────────────────────────────┘
           │                    │
           ▼                    ▼
        MySQL              FastAPI
    (socialchatapp)   (Smart Reply · :8000)
```

- **Client** — JavaFX application managing UI, user interaction, and socket communication
- **Server** — Multi-client socket server handling message routing and persistence
- **Common** — Shared protocol and model classes used by both sides (`Frame`, `FrameIO`, `MessageType`, `Message`, `User`, etc.)

---

## 📁 Project Structure

```
Social-Chat-App/
├── src/
│   ├── client/
│   │   ├── ClientMain.java
│   │   ├── ClientConnection.java
│   │   ├── controller/
│   │   │   ├── MainController.java
│   │   │   ├── HomeController.java
│   │   │   ├── LeftController.java
│   │   │   ├── MidController.java
│   │   │   ├── RightController.java
│   │   │   ├── InviteDialogSession.java
│   │   │   ├── mid/                     # File, media, message, session, and voice handlers
│   │   │   └── right/                   # Message search handler
│   │   ├── media/                       # In-memory HTTP media streaming helper
│   │   └── view/                        # FXML, CSS, and image resources
│   ├── server/
│   │   ├── ServerMain.java
│   │   ├── ClientHandler.java
│   │   ├── dao/                         # Database access classes
│   │   └── service/                     # MessageService
│   └── common/                          # Shared protocol and model classes
├── pom.xml                              # Maven build and dependencies
├── build.fxbuild
└── README.md
```

The server creates `uploads/` for transferred files; voice-message playback can
also create temporary files under `temp/`. Maven's compiled output is under
`target/`.

---

## ⚙️ How the System Works

### 🖥️ Client Side
The client connects to the server at `127.0.0.1:5000` via `ClientConnection`. After authentication, the `HomeController` initializes three sub-controllers: `LeftController` (sidebar and user list), `MidController` (chat area and message logic), and `RightController` (media/info panel). Specialized handlers under the controller layer manage file transfers, voice recording, media playback, search, and session management.

### 🖧 Server Side
`ServerMain` opens a TCP server socket on port `5000` and spawns a `ClientHandler` thread for each connected client. The server handles message routing, offline queuing, file storage/retrieval, and group management. DAO classes manage all MySQL interactions. `MessageService` handles deletion workflows.

### 🔗 Shared Protocol / Model Layer
Both sides share the `common` package. `Frame` is the base unit of communication, and `FrameIO` handles serialization and deserialization. `MessageType` defines all protocol operations. Shared model classes (`User`, `Message`, `GroupMessage`, `Group`, `FileResource`) ensure type consistency across the client-server boundary.

---

## 💬 Messaging Flow

```
Client A types message
    └─► MidController → MessageHandler
            └─► ClientConnection → Frame(SEND_MESSAGE) → Server
                    └─► ClientHandler → MessageDAO (persist)
                            └─► Frame(RECEIVE_MESSAGE) → Client B (if online)
                            └─► Queue (if Client B offline → deliver on reconnect)
```

- Offline messages are queued on the server and delivered when the recipient reconnects.
- Smart reply: after storing the message, the server calls `http://localhost:8000/smart-reply` and forwards suggestions to the recipient if the request succeeds.

---

## 📁 File & Media Flow

```
Client A selects file
    └─► FileHandler → FILE_META frame (filename, size, type)
            └─► FILE_CHUNK frames × N  (64 KB each, max 25 MB)
                    └─► Server stores file → notifies Client B
                            └─► Client B downloads on demand
```

- Files are classified as image, audio, video, or document.
- Voice messages are recorded locally (max 30 seconds), previewed, then sent as audio chunks.
- Media playback on the client uses JavaFX Media APIs. An `InMemoryMediaServer` exposes in-memory audio/video as local HTTP URLs to support JavaFX's media player.

---

## 👥 Group Chat Flow

- A user creates a group and adds members.
- Group messages follow the same `Frame`-based flow, routed to all active members by the server.
- Members can be added or removed by authorized users.
- If the group owner leaves and other members remain, ownership is reassigned automatically.
- The group can be deleted by the owner.

---

## 🗄️ Database Summary

- Database name: `socialchatapp`
- JDBC connection via MySQL driver
- **`users`** — username, BCrypt password hash, optional avatar, online status, and last seen
- **`messages`** — direct-message sender/recipient, body, reply reference, delivery status, and timestamps
- **`files`** — file metadata and storage path linked to a message
- **`groups` / `group_members`** — group name and owner, with membership stored separately
- **`group_messages`** — group, sender, body, optional reply reference, and timestamps

---

## 🎨 UI Overview

The main application window is divided into three panels:

| Panel | Responsibility |
|-------|---------------|
| **Left Sidebar** | User/group list, search, online presence, navigation |
| **Center Chat Area** | Message history, input bar, reply flow, voice record button |
| **Right Info/Media Panel** | Contact info, media tabs (Photos · Videos · Documents) |

Additional UI surfaces:
- **Welcome screen** (`Main.fxml`) — Login and Register entry point
- **Search dialog** — In-conversation message search
- **Voice record dialog** — Record, preview, and send voice messages
- Light and dark theme toggle in the sidebar settings menu

---

## 🚀 Getting Started

### ✅ Prerequisites

- **Java JDK 22+**
- **Apache Maven 3.8+**
- **MySQL 8+** server running locally
- **Python 3 + FastAPI** (required only for smart reply functionality)
- A Java IDE such as **Eclipse** or **IntelliJ IDEA**

### 📦 Java Dependencies

Maven resolves the application's Java libraries from `pom.xml`; no manually installed
JARs or JavaFX SDK configuration are needed.

| Dependency | Used for |
|---|---|
| JavaFX Controls, FXML, Media | Desktop UI, layouts, and media playback |
| jBCrypt | Password hashing |
| org.json | Smart reply response parsing |
| MySQL Connector/J | MySQL database connectivity |
| SLF4J Simple | Logging backend |

MySQL and the optional FastAPI smart-reply service are external services and are not
installed by Maven.

### 🗄️ Database Setup

1. Create a MySQL database named `socialchatapp`
2. Create the tables and columns expected by the DAOs. This repository does not include a database schema or migration script:
   - `users`: `id`, `username`, `password`, `avatar`, `avatar_mime`, `online`, `last_seen`, `avatar_updated_at`
   - `messages`: `id`, `sender`, `recipient`, `body`, `reply_to`, `status`, `created_at`, `delivered_at`, `updated_at`
   - `files`: `id`, `message_id`, `file_name`, `file_path`, `mime_type`, `file_size`, `uploaded_at`
   - `groups`: `id`, `name`, `owner`
   - `group_members`: `group_id`, `username`, `joined_at`
   - `group_messages`: `id`, `group_id`, `sender`, `body`, `reply_to`, `created_at`, `updated_at`
3. Configure the JDBC URL, username, and password in `src/server/dao/DBConnection.java` for your MySQL instance. These settings are currently hard-coded; do not commit real credentials.

### 🤖 Smart Reply Service (Optional)

Start the local FastAPI service on port `8000` before running the server if smart reply functionality is needed:

```bash
uvicorn main:app --port 8000
```

If the service is not running, the server will gracefully skip smart reply suggestions.

---

## ▶️ How to Run

### 1. Run the Server

1. Resolve dependencies and compile with `mvn clean compile`
2. Verify the MySQL connection settings in the server source
3. Run `mvn exec:java -Dexec.mainClass=server.ServerMain`

The server will start listening on **port 5000**.

### 2. Run the Client

1. Run `mvn javafx:run`
2. The client connects to `127.0.0.1:5000` by default
3. Register a new account or log in with existing credentials

Multiple client instances can be launched simultaneously to test real-time messaging.

---

## 📜 Available Concepts / Entry Points

| Component | Entry Point |
|-----------|-------------|
| Client application | `ClientMain.java` |
| Server application | `ServerMain.java` |
| Server socket port | `5000` |
| Default client target | `127.0.0.1:5000` |
| Smart reply endpoint | `http://localhost:8000/smart-reply` |
| File chunk size | 64 KB |
| Max file size | 25 MB |
| Max voice duration | 30 seconds |

---

## ⚠️ Limitations

- **Local server assumption** — The client is configured to connect to `127.0.0.1:5000`. Connecting over a network requires manual configuration.
- **Local database** — MySQL must be installed and configured locally with the correct schema before the server can start.
- **Database schema** — No schema or migration script is included; the required tables must be created separately.
- **Smart reply dependency** — The smart reply feature requires a separately running FastAPI service. It is not bundled with the application.
- **Java version** — The project targets Java 22 or newer.

---

## 🔮 Future Improvements

- 📦 Add automated packaging and release workflows
- 🌐 Support connections over a network beyond the local machine (configurable host/port)
- 🔒 Improve transport security (e.g. TLS over the socket connection)
- 🐳 Containerize the server and database for simpler deployment
- 🔄 Add refresh token / session expiry handling
- 📖 Improve inline documentation and provide a schema migration script
- 🎨 Refine the dark/light theme switcher with user preference persistence
---
