# telecloud

> A modern desktop media cloud powered by Telegram.

TeleCloud transforms your personal Telegram account into a high-performance cloud media library with a native desktop experience inspired by Google Photos.

Browse, stream, upload, search, and manage your entire media archive — all stored inside your own Telegram channels.

---

## Overview

Telegram provides effectively unlimited cloud storage for personal media.
TeleCloud builds a complete desktop media platform on top of it.

Instead of manually scrolling through chats and channels, TeleCloud gives you:

* instant media browsing
* thumbnail generation
* video streaming
* drag & drop uploads
* local caching
* search
* multi-channel organization
* full desktop UX

All while using your own Telegram account and infrastructure.

---

## Preview

* Infinite-scrolling media grid
* Google Photos–style lightbox viewer
* Built-in video player with local cache pipeline
* Multi-channel media organization
* Desktop-native upload/download dialogs
* Real-time cache management

---

## Core Features

### Smart Media Browser

Browse thousands of files using a responsive infinite-scroll grid system optimized for large media collections.

Supported media types:

* Photos
* Videos
* Audio
* Documents

---

### Telegram-Powered Cloud Storage

Use:

* Saved Messages
* Private channels
* Dedicated storage channels

Create and manage storage channels directly inside the app.

---

### High-Performance Thumbnail Pipeline

TeleCloud includes:

* concurrent thumbnail downloading
* local thumbnail caching
* rate-limit protection
* async media loading

The backend uses controlled concurrency with async semaphores to prevent Telegram flood waits.

```python
_thumb_sem = asyncio.Semaphore(5)
```

---

### Built-In Video Streaming

Videos are:

1. downloaded into local cache
2. indexed
3. streamed directly through the desktop player

This avoids:

* repeated Telegram downloads
* playback lag
* excessive RAM usage

---

### Chunked Upload System

Large files are uploaded using chunked streaming instead of loading the full file into memory.

Benefits:

* lower RAM usage
* better upload stability
* smoother desktop experience

---

### Secure Session Storage

Telegram sessions are encrypted locally using Fernet encryption before being written to SQLite.

```python
from cryptography.fernet import Fernet
```

Security features include:

* encrypted session persistence
* isolated Electron preload bridge
* disabled Node integration
* context isolation enabled

```js
nodeIntegration: false,
contextIsolation: true,
```

---

# Architecture

```text
┌─────────────────────────────────────────────┐
│                   Electron                  │
│                                             │
│  ┌──────────────────────────────────────┐   │
│  │       React + Vite Frontend          │   │
│  │                                      │   │
│  │  MediaGrid · Viewer · Lightbox       │   │
│  │  Sidebar · Toolbar · VideoPlayer     │   │
│  └──────────────────────────────────────┘   │
│                    │                        │
│             HTTP localhost:8765            │
│                    │                        │
│  ┌──────────────────────────────────────┐   │
│  │       FastAPI + Telethon Backend     │   │
│  │                                      │   │
│  │  Auth · Cache · Streaming · Upload   │   │
│  │  Thumbnail Pipeline · Search API     │   │
│  └──────────────────────────────────────┘   │
└──────────────────┬──────────────────────────┘
                   │
                MTProto
                   │
           ┌───────▼───────┐
           │   Telegram    │
           └───────────────┘
```

---

# Tech Stack

| Layer           | Technology            |
| --------------- | --------------------- |
| Desktop Runtime | Electron 28           |
| Frontend        | React 18 + Vite       |
| Backend         | FastAPI               |
| Telegram API    | Telethon              |
| Database        | SQLite + aiosqlite    |
| ORM             | SQLAlchemy Async      |
| Authentication  | JWT                   |
| Encryption      | cryptography / Fernet |
| IPC Bridge      | Electron preload      |
| Media Streaming | Local cached pipeline |

---

# Performance Design

TeleCloud was designed around large-scale media browsing.

Key optimizations include:

* Infinite scrolling
* Lazy thumbnail loading
* Async concurrent downloads
* Cached media pipeline
* Chunked uploads
* Local filesystem caching
* Stream-based video playback
* Controlled Telegram API concurrency

---

# Security Model

TeleCloud runs entirely on the local machine.

No third-party servers are used.

Sensitive data:

* Telegram sessions
* auth tokens
* cache metadata

remain stored locally.

The Electron app uses:

* `contextIsolation`
* `preload.js`
* disabled Node integration

to reduce renderer attack surface.

---

# API Overview

The backend exposes a local REST API served by FastAPI.

Example endpoints:

| Method | Endpoint                     | Description                 |
| ------ | ---------------------------- | --------------------------- |
| `POST` | `/auth/send_code`            | Request Telegram login code |
| `POST` | `/auth/verify`               | Authenticate user           |
| `GET`  | `/channels`                  | List Telegram channels      |
| `GET`  | `/media/channel/{id}`        | Paginated media listing     |
| `GET`  | `/stream/{channel}/{msg}`    | Media streaming             |
| `POST` | `/upload`                    | Upload file                 |
| `GET`  | `/thumbnail/{channel}/{msg}` | Cached thumbnails           |
| `POST` | `/video/cache/{id}`          | Cache video locally         |

---

# Project Structure

```text
telecloud/
├── backend/
│   ├── app.py
│   ├── setup_env.py
│   └── data/
│
├── electron/
│   ├── main.js
│   └── preload.js
│
├── frontend/
│   ├── src/
│   │   ├── api/
│   │   ├── components/
│   │   ├── hooks/
│   │   └── styles/
│   │
│   └── dist/
│
├── setup.bat
├── start.bat
└── package.json
```

---

# Why TeleCloud?

Most Telegram media tools focus only on:

* uploading files
* downloading files
* acting as basic clients

TeleCloud focuses on transforming Telegram into a complete desktop media platform.

The goal is not just storage.

The goal is:

* organization
* browsing
* streaming
* accessibility
* desktop-native UX

---

# Current Limitations

* Windows-focused environment
* No installer/package release yet
* Telegram API flood limits still apply
* Single-user architecture
* Extremely large libraries may require virtualization improvements

---

# Future Plans

* Virtualized media rendering
* Background indexing workers
* AI-powered search
* EXIF metadata indexing
* Smart albums
* Multi-account support
* Electron auto-updater
* Native installers
* Cross-platform support
* Offline-first mode

---

# Development

## Setup

```bash
git clone https://github.com/yourname/telecloud.git
cd telecloud
setup.bat
```

## Start

```bash
start.bat
```

---

# Disclaimer

TeleCloud is an independent open-source project and is not affiliated with Telegram.

Use of Telegram as a storage backend is subject to Telegram Terms of Service.

---

# License

MIT License.
