# eClaw Server

> 🌐 eClaw Web Server — Node.js + Express + WebSocket, providing user authentication, file upload, and integration with xCrab Gateway

[![GPL-3.0 License](https://img.shields.io/badge/License-GPL--3.0-blue.svg)](LICENSE)
[![Node.js v18+](https://img.shields.io/badge/Node.js-v18+-green.svg)](https://nodejs.org/)
[![Stars](https://img.shields.io/github/stars/yzp100911/eclaw-server?style=social)](https://github.com/yzp100911/eclaw-server)
[![Forks](https://img.shields.io/github/forks/yzp100911/eclaw-server?style=social)](https://github.com/yzp100911/eclaw-server)

## Features

- **User Authentication System** — JWT token authentication, supporting registration and login
- **File Upload** — Support for file upload and management
- **WebSocket Real-time Communication** — Maintains long connection with clients
- **xCrab Gateway Integration** — Seamlessly integrates with xCrab Agent multi-model gateway
- **MySQL Data Storage** — Persistent user data and configuration

## Requirements

- Node.js v18+
- MySQL 5.7+
- npm or pnpm

## Installation

```bash
git clone https://github.com/yzp100911/eclaw-server.git
cd eclaw-server
npm install
```

## Configuration

Create a `.env` file:

```env
PORT=3001
JWT_SECRET=your-super-secret-key

# MySQL Configuration (connected via SSH tunnel)
MYSQL_HOST=127.0.0.1
MYSQL_PORT=3306
MYSQL_USER=your_user
MYSQL_PASSWORD=your_password
MYSQL_DATABASE=wclaw_db

# xCrab Gateway Configuration
XCRAB_API_URL=http://localhost:3000
XCRAB_TOKEN=your_token

# SSH Tunnel (for cloud database connection)
SSH_HOST=your_server
SSH_PORT=22
SSH_USER=ubuntu
SSH_KEY_PATH=~/.ssh/id_rsa
```

## Running

```bash
npm start
```

## Project Structure

```
eclaw-server/
├── server.js          # Main entry point
├── wclaw/             # Web frontend static files
├── cloud-sync.js      # Cloud sync module
├── package.json
└── LICENSE
```

## Related Projects

- [xCrab-Agent](https://github.com/yzp100911/xCrab-Agent) — Multi-model AI Gateway
- [eClaw Server](https://github.com/yzp100911/eclaw-server) — Web Server
- [Claw Client](https://github.com/yzp100911/claw-client) — Execution Client

## License

This project is licensed under [GPL-3.0](LICENSE).