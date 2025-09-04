# 部署与配置指南

## 🎁 快速下载

### 解压即可使用
[下载 Mira Dashboard 最新版本](https://github.com/hunmer/mira-dashboard/blob/b3bc686915116c84bd71ffe686d2dac188daf59b/dist.zip)

## 🚀 本地部署

### 环境要求

| 依赖 | 最低版本 | 推荐版本 | 说明 |
|------|----------|----------|------|
| Node.js | 16.14.0 | 18.17.0+ | JavaScript 运行环境 |
| pnpm | 7.0.0 | 8.6.0+ | 包管理器 |
| Git | 2.20.0 | 最新版本 | 版本控制 |

### 克隆项目

```bash
# 克隆主项目
git clone https://github.com/hunmer/mira_typescript.git
cd mira_typescript

# 进入 Dashboard 目录
cd mira-dashboard
```

### 安装依赖

```bash
# 使用 pnpm 安装依赖
pnpm install

# 或者使用 npm
npm install
```

### 环境配置

创建环境变量文件：

```bash
# 复制环境变量模板
cp .env.example .env.local

# 编辑配置文件
nano .env.local
```

### 启动开发服务器

```bash
# 启动开发服务器
pnpm dev

# 项目将在 http://localhost:3999 启动
```

### 构建生产版本

```bash
# 构建生产版本
pnpm build

# 预览构建结果
pnpm preview
```

## ⚙️ 环境配置

### 环境变量说明

#### 基础配置

```bash
# 应用基础配置
VITE_APP_TITLE="Mira Dashboard"
VITE_APP_DESC="Mira 管理面板"
VITE_BASE="/"

# API 服务配置
VITE_API_URL="http://localhost:8081"
VITE_WS_URL="ws://localhost:8081"

# 路由配置
VITE_ROUTER_HISTORY="hash"  # hash | history

# 开发配置
VITE_DEV_TOOL="true"        # 是否启用开发工具
VITE_MOCK="false"           # 是否启用 Mock 数据
```

#### 环境特定配置

**开发环境 (.env.development)**
```bash
VITE_API_URL=http://localhost:8081
VITE_WS_URL=ws://localhost:8081
VITE_DEV_TOOL=true
VITE_MOCK=false
```

**生产环境 (.env.production)**
```bash
VITE_API_URL=https://api.yourdomain.com
VITE_WS_URL=wss://api.yourdomain.com
VITE_DEV_TOOL=false
VITE_MOCK=false
```

**测试环境 (.env.test)**
```bash
VITE_API_URL=https://test-api.yourdomain.com
VITE_WS_URL=wss://test-api.yourdomain.com
VITE_DEV_TOOL=true
VITE_MOCK=false
```