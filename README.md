# eClaw Server

> 🌐 eClaw 网页服务端 — Node.js + Express + WebSocket，提供用户认证、文件上传、与 xCrab Gateway 集成的网页平台

[![GPL-3.0 License](https://img.shields.io/badge/License-GPL--3.0-blue.svg)](LICENSE)
[![Node.js v18+](https://img.shields.io/badge/Node.js-v18+-green.svg)](https://nodejs.org/)

## 功能特性

- **用户认证系统** — JWT 令牌认证，支持注册、登录
- **文件上传** — 支持文件上传和管理
- **WebSocket 实时通信** — 与客户端保持长连接
- **xCrab Gateway 集成** — 无缝对接 xCrab Agent 多模型网关
- **MySQL 数据存储** — 持久化用户数据和配置

## 环境要求

- Node.js v18+
- MySQL 5.7+
- npm 或 pnpm

## 安装

```bash
git clone https://github.com/yzp100911/eclaw-server.git
cd eclaw-server
npm install
```

## 配置

创建 `.env` 文件：

```env
PORT=3001
JWT_SECRET=your-super-secret-key

# MySQL 配置（通过 SSH 隧道连接）
MYSQL_HOST=127.0.0.1
MYSQL_PORT=3306
MYSQL_USER=your_user
MYSQL_PASSWORD=your_password
MYSQL_DATABASE=wclaw_db

# xCrab Gateway 配置
XCRAB_API_URL=http://localhost:3000
XCRAB_TOKEN=your_token

# SSH 隧道（连接云数据库）
SSH_HOST=your_server
SSH_PORT=22
SSH_USER=ubuntu
SSH_KEY_PATH=~/.ssh/id_rsa
```

## 运行

```bash
npm start
```

## 项目结构

```
eclaw-server/
├── server.js          # 主入口
├── wclaw/             # 网页前端静态文件
├── cloud-sync.js      # 云端同步模块
├── package.json
└── LICENSE
```

## 开源协议

本项目采用 [GPL-3.0](LICENSE) 开源协议。