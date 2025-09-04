# API 接口文档

## 🌐 概述

Mira Dashboard 通过 RESTful API 与后端服务进行通信，所有 API 请求都需要通过认证，并支持 WebSocket 实时通信。

## 🔐 认证方式

### Bearer Token 认证

所有 API 请求都需要在请求头中包含 JWT Token：

```http
Authorization: Bearer <jwt_token>
```

### 获取 Token

```http
POST /auth/login
Content-Type: application/json

{
  "username": "admin",
  "password": "password"
}
```

**响应：**
```json
{
  "success": true,
  "data": {
    "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
    "user": {
      "id": "1",
      "username": "admin",
      "role": "super",
      "email": "admin@example.com"
    }
  }
}
```

## 📊 系统概览 API

### 获取系统统计信息

```http
GET /api/stats/overview
```

**响应：**
```json
{
  "success": true,
  "data": {
    "libraries": 5,
    "plugins": 23,
    "admins": 3,
    "dbSize": "245.6 MB",
    "systemInfo": {
      "uptime": "3天 5小时",
      "version": "1.2.3",
      "nodeVersion": "18.17.0",
      "status": "running"
    }
  }
}
```

### 获取最近活动

```http
GET /api/activities/recent?limit=10
```

**响应：**
```json
{
  "success": true,
  "data": [
    {
      "id": "1",
      "type": "library_created",
      "message": "创建了新的资源库: MyLibrary",
      "user": "admin",
      "timestamp": "2024-01-15T10:30:00Z"
    }
  ]
}
```

## 📚 资源库管理 API

### 获取资源库列表

```http
GET /api/libraries?page=1&limit=20&search=&status=
```

**查询参数：**
- `page`: 页码（默认：1）
- `limit`: 每页数量（默认：20）
- `search`: 搜索关键词
- `status`: 状态筛选（active/inactive）

**响应：**
```json
{
  "success": true,
  "data": {
    "libraries": [
      {
        "id": "lib_001",
        "name": "默认资源库",
        "description": "系统默认资源库",
        "path": "/opt/mira/libraries/default",
        "type": "local",
        "status": "active",
        "fileCount": 156,
        "size": 12345678,
        "createdAt": "2024-01-01T00:00:00Z",
        "updatedAt": "2024-01-15T10:30:00Z"
      }
    ],
    "pagination": {
      "page": 1,
      "limit": 20,
      "total": 5,
      "totalPages": 1
    }
  }
}
```

### 创建资源库

```http
POST /api/libraries
Content-Type: application/json

{
  "name": "新资源库",
  "description": "资源库描述",
  "path": "/path/to/library",
  "type": "local",
  "icon": "folder",
  "config": {
    "pluginsDir": "plugins"
  }
}
```

**响应：**
```json
{
  "success": true,
  "data": {
    "id": "lib_002",
    "name": "新资源库",
    "description": "资源库描述",
    "path": "/path/to/library",
    "type": "local",
    "status": "active",
    "createdAt": "2024-01-15T10:30:00Z"
  }
}
```

### 更新资源库

```http
PUT /api/libraries/{libraryId}
Content-Type: application/json

{
  "name": "更新的资源库名称",
  "description": "更新的描述",
  "status": "active"
}
```

### 删除资源库

```http
DELETE /api/libraries/{libraryId}
```

### 批量删除资源库

```http
POST /api/libraries/batch-delete
Content-Type: application/json

{
  "libraryIds": ["lib_001", "lib_002"]
}
```

## 🔌 插件管理 API

### 获取插件列表

```http
GET /api/plugins?libraryId=&category=&status=&sort=name&order=asc
```

**查询参数：**
- `libraryId`: 资源库ID筛选
- `category`: 插件分类筛选
- `status`: 插件状态筛选
- `sort`: 排序字段（name/author/createdAt/category）
- `order`: 排序方向（asc/desc）

**响应：**
```json
{
  "success": true,
  "data": {
    "libraries": [
      {
        "id": "lib_001",
        "name": "默认资源库",
        "plugins": [
          {
            "id": "plugin_001",
            "name": "file-manager",
            "displayName": "文件管理器",
            "description": "强大的文件管理插件",
            "version": "1.0.0",
            "author": "Mira Team",
            "category": "utility",
            "status": "active",
            "configurable": true,
            "main": "index.js",
            "icon": "/icons/file-manager.png",
            "tags": ["file", "management"],
            "dependencies": ["fs-extra", "mime-types"],
            "createdAt": "2024-01-01T00:00:00Z",
            "updatedAt": "2024-01-15T10:30:00Z"
          }
        ]
      }
    ]
  }
}
```

### 切换插件状态

```http
PATCH /api/plugins/{pluginName}/status
Content-Type: application/json

{
  "status": "active"
}
```

### 获取插件配置

```http
GET /api/plugins/{pluginName}/config
```

**响应：**
```json
{
  "success": true,
  "data": {
    "config": {
      "enabled": true,
      "options": {
        "maxFileSize": "10MB",
        "allowedTypes": ["jpg", "png", "pdf"]
      }
    }
  }
}
```

### 更新插件配置

```http
PUT /api/plugins/{pluginName}/config
Content-Type: application/json

{
  "config": {
    "enabled": true,
    "options": {
      "maxFileSize": "20MB",
      "allowedTypes": ["jpg", "png", "pdf", "docx"]
    }
  }
}
```

### 安装插件（本地）

```http
POST /api/plugins/install/local
Content-Type: multipart/form-data

file: <plugin_package.zip>
libraryId: lib_001
```

### 安装插件（仓库）

```http
POST /api/plugins/install/repository
Content-Type: application/json

{
  "name": "plugin-name",
  "version": "latest",
  "repository": "npm",
  "libraryId": "lib_001"
}
```

### 卸载插件

```http
DELETE /api/plugins/{pluginName}?libraryId={libraryId}
```

## 🌐 插件路由管理 API

### 获取支持插件路由的资源库

```http
GET /api/plugin-routes/libraries
```

**响应：**
```json
{
  "success": true,
  "data": {
    "libraries": [
      {
        "id": "lib_001",
        "name": "默认资源库",
        "routeCount": 5
      }
    ]
  }
}
```

### 获取插件路由列表

```http
GET /api/plugin-routes/{libraryId}
```

**响应：**
```json
{
  "success": true,
  "data": [
    {
      "name": "PluginDashboard",
      "path": "/plugin/dashboard",
      "component": "PluginDashboard.vue",
      "meta": {
        "title": "插件仪表板",
        "icon": "dashboard",
        "roles": ["admin", "super"],
        "order": 1
      }
    }
  ]
}
```

## 📱 设备管理 API

### 获取设备列表

```http
GET /api/devices?status=&type=
```

**响应：**
```json
{
  "success": true,
  "data": {
    "devices": [
      {
        "id": "device_001",
        "name": "iPhone 14",
        "type": "ios",
        "status": "online",
        "ipAddress": "192.168.1.100",
        "lastConnected": "2024-01-15T10:30:00Z",
        "battery": 85,
        "version": "16.5",
        "apps": ["com.example.app1"]
      }
    ],
    "stats": {
      "total": 10,
      "online": 3,
      "offline": 6,
      "connecting": 1
    }
  }
}
```

### 连接设备

```http
POST /api/devices/{deviceId}/connect
```

### 断开设备

```http
POST /api/devices/{deviceId}/disconnect
```

### 发送消息到设备

```http
POST /api/devices/{deviceId}/message
Content-Type: application/json

{
  "content": "Hello Device!",
  "type": "user_message"
}
```

### 获取设备消息历史

```http
GET /api/devices/{deviceId}/messages?limit=50
```

**响应：**
```json
{
  "success": true,
  "data": [
    {
      "id": "msg_001",
      "type": "user_message",
      "content": "Hello Device!",
      "timestamp": "2024-01-15T10:30:00Z",
      "direction": "outbound"
    }
  ]
}
```

## 👥 管理员管理 API

### 获取管理员列表

```http
GET /api/admins?page=1&limit=20&role=&status=
```

**响应：**
```json
{
  "success": true,
  "data": {
    "admins": [
      {
        "id": "admin_001",
        "username": "admin",
        "email": "admin@example.com",
        "role": "super",
        "status": "active",
        "lastLogin": "2024-01-15T10:30:00Z",
        "createdAt": "2024-01-01T00:00:00Z"
      }
    ],
    "pagination": {
      "page": 1,
      "limit": 20,
      "total": 3,
      "totalPages": 1
    }
  }
}
```

### 创建管理员

```http
POST /api/admins
Content-Type: application/json

{
  "username": "newadmin",
  "password": "password123",
  "email": "newadmin@example.com",
  "role": "admin",
  "status": "active"
}
```

### 更新管理员

```http
PUT /api/admins/{adminId}
Content-Type: application/json

{
  "email": "updated@example.com",
  "role": "admin",
  "status": "active"
}
```

### 重置管理员密码

```http
POST /api/admins/{adminId}/reset-password
```

**响应：**
```json
{
  "success": true,
  "data": {
    "newPassword": "temp_password_123"
  }
}
```

### 删除管理员

```http
DELETE /api/admins/{adminId}
```

## 💾 数据库预览 API

### 获取数据库信息

```http
GET /api/database/info
```

**响应：**
```json
{
  "success": true,
  "data": {
    "size": "245.6 MB",
    "tables": 15,
    "records": 12450,
    "lastBackup": "2024-01-15T10:30:00Z"
  }
}
```

### 获取表列表

```http
GET /api/database/tables
```

**响应：**
```json
{
  "success": true,
  "data": [
    {
      "name": "libraries",
      "records": 5,
      "size": "2.3 MB",
      "lastModified": "2024-01-15T10:30:00Z"
    }
  ]
}
```

### 获取表数据

```http
GET /api/database/tables/{tableName}/data?page=1&limit=20
```

## ⚡ WebSocket 实时通信

### 连接端点

```
ws://localhost:8081/ws
```

### 认证

连接时需要提供 token：

```javascript
const ws = new WebSocket('ws://localhost:8081/ws?token=' + jwt_token);
```

### 消息格式

所有 WebSocket 消息都使用以下格式：

```json
{
  "type": "message_type",
  "data": {},
  "timestamp": "2024-01-15T10:30:00Z"
}
```

### 支持的消息类型

#### 设备状态变更
```json
{
  "type": "device_status_changed",
  "data": {
    "deviceId": "device_001",
    "status": "online",
    "timestamp": "2024-01-15T10:30:00Z"
  }
}
```

#### 插件状态变更
```json
{
  "type": "plugin_status_changed",
  "data": {
    "pluginName": "file-manager",
    "libraryId": "lib_001",
    "status": "active"
  }
}
```

#### 系统通知
```json
{
  "type": "system_notification",
  "data": {
    "level": "info",
    "title": "系统更新",
    "message": "系统已更新到版本 1.2.3",
    "action": {
      "type": "reload",
      "url": "/mira/overview"
    }
  }
}
```

## 🚨 错误处理

### 错误响应格式

```json
{
  "success": false,
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "请求参数验证失败",
    "details": [
      {
        "field": "name",
        "message": "名称不能为空"
      }
    ]
  }
}
```

### 常见错误代码

| 错误代码 | HTTP状态码 | 说明 |
|----------|------------|------|
| UNAUTHORIZED | 401 | 未授权访问 |
| FORBIDDEN | 403 | 权限不足 |
| NOT_FOUND | 404 | 资源不存在 |
| VALIDATION_ERROR | 400 | 请求参数验证失败 |
| INTERNAL_ERROR | 500 | 服务器内部错误 |
| PLUGIN_ERROR | 422 | 插件操作失败 |
| DEVICE_ERROR | 422 | 设备操作失败 |

### 客户端错误处理

```typescript
// API 客户端自动错误处理
miraApiClient.addResponseInterceptor({
  rejected: (error) => {
    if (error.response?.status === 401) {
      // 跳转到登录页
      router.push('/login');
      message.error('登录已过期，请重新登录');
    } else if (error.response?.status >= 500) {
      message.error('服务器错误，请稍后重试');
    } else if (error.response?.status >= 400) {
      const errorMessage = error.response?.data?.error?.message || '请求失败';
      message.error(errorMessage);
    }
    return Promise.reject(error);
  },
});
```

## 📋 API 调用示例

### JavaScript/TypeScript

```typescript
import { miraApiClient } from '@/api/mira/client';

// 获取资源库列表
const getLibraries = async () => {
  try {
    const response = await miraApiClient.get('/libraries');
    return response.data;
  } catch (error) {
    console.error('获取资源库失败:', error);
    throw error;
  }
};

// 创建资源库
const createLibrary = async (libraryData: CreateLibraryRequest) => {
  try {
    const response = await miraApiClient.post('/libraries', libraryData);
    return response.data;
  } catch (error) {
    console.error('创建资源库失败:', error);
    throw error;
  }
};

// 上传插件
const uploadPlugin = async (file: File, libraryId: string) => {
  const formData = new FormData();
  formData.append('file', file);
  formData.append('libraryId', libraryId);

  try {
    const response = await miraApiClient.post('/plugins/install/local', formData, {
      headers: {
        'Content-Type': 'multipart/form-data',
      },
    });
    return response.data;
  } catch (error) {
    console.error('插件上传失败:', error);
    throw error;
  }
};
```

### cURL 示例

```bash
# 登录获取 token
curl -X POST http://localhost:8081/auth/login \
  -H "Content-Type: application/json" \
  -d '{"username":"admin","password":"password"}'

# 获取资源库列表
curl -X GET http://localhost:8081/api/libraries \
  -H "Authorization: Bearer <jwt_token>"

# 创建资源库
curl -X POST http://localhost:8081/api/libraries \
  -H "Authorization: Bearer <jwt_token>" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "新资源库",
    "description": "测试资源库",
    "path": "/path/to/library",
    "type": "local"
  }'
```

## 🔄 API 版本控制

当前 API 版本：`v1`

所有 API 端点都包含版本前缀：`/api/v1/`

### 版本升级策略

- **向后兼容**：新版本 API 保持向后兼容
- **废弃通知**：废弃的 API 会提前通知
- **多版本支持**：同时支持多个 API 版本

### 版本头信息

```http
API-Version: v1
Accept: application/json
```

## 📊 API 性能监控

### 请求耗时统计

所有 API 请求都会记录耗时，可通过响应头查看：

```http
X-Response-Time: 45ms
X-Request-ID: req_123456789
```

### 速率限制

API 请求受到速率限制保护：

```http
X-RateLimit-Limit: 1000
X-RateLimit-Remaining: 999
X-RateLimit-Reset: 1642234567
```

超出限制时返回 429 状态码：

```json
{
  "success": false,
  "error": {
    "code": "RATE_LIMIT_EXCEEDED",
    "message": "API 请求过于频繁，请稍后重试"
  }
}
```
