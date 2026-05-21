# eclaw-server 📡

**eclaw-server** — 云端中转调度服务器，连接网页端（wclaw）和客户端（cclaw）的桥梁。

Express + WebSocket + MySQL 架构，支持用户管理、实时消息转发、文件上传、短信验证码等功能。

---

## 📦 系统架构关系

```
  网页端 (浏览器/手机)          eclaw-server (本仓库)           claw-client (执行端)
  ┌──────────────┐          ┌──────────────────────┐        ┌──────────────────┐
  │  🌐 wclaw  │  HTTPS   │  📡 中转调度服务器     │  WS   │  🤖 命令执行器   │
  │  聊天界面    │◄────────►│  Express + WebSocket │◄──────►│  node-pty 终端  │
  │  文件管理    │           │  MySQL 持久化         │        │   WebSocket 连接 │
  └──────────────┘          │  xCrab AI 对接        │        └──────────────────┘
                            └──────────┬───────────┘
                                       │ HTTP
                                       ▼
                            ┌──────────────────┐
                            │  🧠 xCrab-Agent  │
                            │  AI 对话引擎      │
                            └──────────────────┘
```

> - **eclaw-server** = 中转调度服务器（用户登录、消息转发、文件服务）← **本仓库**
> - **claw-client** = 连接 eclaw，在远程服务器上执行终端命令
> - **xCrab-Agent** = 提供 AI 对话能力（可选对接）

---

## 🚀 部署指南（Windows / Linux）

### 📋 环境要求

| 环境 | 要求 |
|------|------|
| **Node.js** | **v18.x 或更高**（推荐 v20 LTS） |
| **npm** | 随 Node.js 自带 |
| **MySQL** | v8.0+（支持远程连接 via SSH 隧道） |
| **系统** | Windows 10+ / Ubuntu 20.04+ |

---

## 🪟 Windows 环境部署

### 1️⃣ 安装 Node.js

- 访问 [https://nodejs.org](https://nodejs.org) 下载 **v20.x LTS** 版本
- 安装完成后打开 **命令提示符 (cmd)** 或 **PowerShell**，验证安装：

```bash
node -v    # 应显示 v20.x.x
npm -v     # 应显示 10.x.x
```

### 2️⃣ 克隆仓库

```bash
# 先安装 Git（https://git-scm.com/downloads/win）
git clone https://github.com/yzp100911/eclaw-server.git
cd eclaw-server
```

### 3️⃣ 安装依赖

```bash
npm install
```

### 4️⃣ 配置环境变量

```bash
# 复制环境变量模板
copy .env.example .env
```

编辑 `.env` 文件：

```env
# xCrab 服务端对接配置（如不对接可保留默认）
XCRAB_TOKEN=100911yzpYZP@
XCRAB_API_URL=http://localhost:3000
```

### 5️⃣ 配置 MySQL 数据库连接

打开 `cloud-sync.js`，修改以下配置：

```javascript
// ====== 修改为你自己的云服务器信息 ======
const SSH_HOST = 'ubuntu@你的云服务器IP';     // 你的云服务器 SSH 地址
const DB_USER = 'wclaw_db';                    // MySQL 用户名
const DB_PASS = '你的MySQL密码';               // MySQL 密码
const DB_NAME = 'wclaw_db';                    // 数据库名
```

> 💡 **提示**：如果不想使用 SSH 隧道（比如 MySQL 在本地），可以修改 `server.js` 中的 `getCloudDbConfig()` 调用为本地 MySQL 连接。

### 6️⃣ 准备 SSH 密钥

将你的 SSH 私钥放在以下位置：

- **Windows**: `C:\Users\你的用户名\.ssh\cloud_server_ed25519`
- **Linux**: `~/.ssh/cloud_server_ed25519`

> 或者修改 `cloud-sync.js` 中的 `SSH_KEY` 路径为你实际密钥位置。

### 7️⃣ 启动服务器

```bash
node server.js
```

看到以下输出即启动成功：

```
[启动] 正在建立 SSH 隧道到云服务器 MySQL...
[Cloud Tunnel] SSH 隧道已建立 (127.0.0.1:3307 → 云 MySQL:3306)
MySQL 数据库和表初始化成功！
[启动] 云数据库初始化完成
```

服务器默认运行在 **端口 10090**（查看 `server.js` 底部确认）。

---

## 🐧 Linux 环境部署（Ubuntu）

### 1️⃣ 安装 Node.js

```bash
# 安装 Node.js 20.x
curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash -
sudo apt-get install -y nodejs

# 验证安装
node -v
npm -v
```

### 2️⃣ 克隆仓库

```bash
sudo apt-get install -y git
git clone https://github.com/yzp100911/eclaw-server.git
cd eclaw-server
```

### 3️⃣ 安装依赖

```bash
npm install
```

### 4️⃣ 配置环境变量

```bash
cp .env.example .env
nano .env    # 编辑配置文件
```

### 5️⃣ 配置 MySQL 连接

```bash
nano cloud-sync.js   # 修改 SSH_HOST / DB_USER / DB_PASS 等配置
```

### 6️⃣ 放置 SSH 密钥

```bash
# 确保密钥文件存在且权限正确
chmod 600 ~/.ssh/cloud_server_ed25519
```

### 7️⃣ 启动服务器

```bash
node server.js
```

### 8️⃣ ★ 设置开机自启（systemd 服务）

```bash
# 创建 systemd 服务文件
sudo tee /etc/systemd/system/eclaw.service << 'EOF'
[Unit]
Description=eclaw-server - 云端中转调度服务器
After=network.target network-online.target
Wants=network-online.target

[Service]
Type=simple
User=root
WorkingDirectory=/path/to/eclaw-server   # 改为你的实际路径
ExecStart=/usr/bin/node /path/to/eclaw-server/server.js
Restart=always
RestartSec=10
StandardOutput=journal
StandardError=journal
Environment=NODE_ENV=production

[Install]
WantedBy=multi-user.target
EOF

# 重新加载 systemd
sudo systemctl daemon-reload

# 启用并启动服务
sudo systemctl enable eclaw
sudo systemctl start eclaw

# 查看状态和日志
sudo systemctl status eclaw
sudo journalctl -u eclaw -f
```

---

## 🌐 访问网页端（wclaw）

启动成功后，在浏览器访问：

```
http://你的服务器IP:10090
```

即可看到 wclaw 聊天管理界面。

---

## 🔌 对接 xCrab-Agent（AI 对话能力）

eclaw-server 默认会尝试连接本地的 xCrab-Agent（localhost:3000）。

### 配置步骤：

1. **启动 xCrab-Agent**（确保已启动并开启了 Gateway）
2. **编辑 `.env`** 确保配置正确：

```env
XCRAB_TOKEN=你的token       # 与 xCrab-Agent 的 GATEWAY_TOKEN 一致
XCRAB_API_URL=http://localhost:3000   # xCrab-Agent 的 Gateway 地址
```

---

## 🔗 对接 claw-client（命令执行终端）

eclaw-server 默认在 `ws://0.0.0.0:10090/ws` 监听 WebSocket 连接。

**claw-client** 连接时需要配置：

```bash
# 在 claw-client 端设置环境变量
export ECLAW_API_URL=http://你的eclaw服务器IP:10090
export ECLAW_WS_URL=ws://你的eclaw服务器IP:10090/ws
```

---

## 📁 项目结构

```
eclaw-server/
├── server.js              # 入口文件（启动服务）
├── package.json           # 依赖配置
├── .env                   # 环境变量配置
├── .env.example           # 环境变量模板
├── cloud-sync.js          # SSH 隧道 + MySQL 数据同步
├── users.json             # 用户数据（本地存储）
├── history.json           # 聊天记录缓存
│
├── wclaw/                 # 🌐 网页前端（静态文件）
│   ├── index.html         # 主页面
│   ├── styles.css         # 样式表
│   ├── app.js             # 主应用入口
│   ├── app-base.js        # 基础模块
│   ├── app-auth.js        # 登录/注册模块
│   ├── app-main.js        # 聊天主界面模块
│   ├── app-anim.js        # 动画效果
│   ├── edge-tts-config.html  # 语音合成配置页
│   └── icon/              # 图标资源
│
└── uploads/               # 上传文件存储目录
```

---

## 🔧 故障排除

| 问题 | 解决方案 |
|------|----------|
| **SSH 隧道连接失败** | 检查 `cloud-sync.js` 中的服务器地址、用户名、密钥路径是否正确 |
| **MySQL 连接失败** | 确认云服务器 MySQL 已启动并允许远程连接，检查用户名密码 |
| **端口 10090 被占用** | 修改 `server.js` 中 `server.listen(...)` 的端口号 |
| **WebSocket 连接不上** | 检查防火墙是否放行了端口，确认 `ECLAW_WS_URL` 路径为 `/ws` |
| **前端页面空白** | 检查浏览器控制台是否有报错，确认 `wclaw/` 目录文件完整 |

---

## 📝 许可证

MIT
