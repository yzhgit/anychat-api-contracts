# AnyChat API Contracts

[English](#english) | [中文](#中文)

---

## English

API contract definitions for the AnyChat IM backend system, including HTTP REST, WebSocket, and gRPC interfaces.

### Overview

AnyChat is a microservices-based IM backend composed of 12 independent services communicating via gRPC and HTTP, with NATS handling async messaging. This repository contains all external interface specifications and is automatically synced by the backend CI.

### Repository Structure

```
├── http/
│   └── openapi.json       # HTTP REST API (OpenAPI 3.0)
├── websocket/
│   └── asyncapi.yaml      # WebSocket / Event-driven API (AsyncAPI 2.6.0)
├── proto/
│   ├── common/            # Shared types
│   ├── auth/              # Authentication service
│   ├── user/              # User profile & search
│   ├── message/           # Message send, fetch, recall
│   ├── session/           # Session list, read status
│   ├── friend/            # Friend relations & requests
│   ├── group/             # Group management & members
│   ├── file/              # File upload & download
│   ├── push/              # Offline push notifications
│   ├── rtc/               # Audio/video calls
│   └── sync/              # Multi-device sync
└── CLAUDE.md              # AI assistant context
```

### Integration

#### HTTP REST API

Clients connect through the `gateway-service` HTTP interface.

**Base URL:** `http://<host>:8080/api/v1`

**Authentication:** All protected endpoints require `Authorization: Bearer <access_token>`

See [`http/openapi.json`](http/openapi.json) for the full OpenAPI 3.0 specification.

#### WebSocket

Real-time messaging is delivered over a persistent WebSocket connection.

**Endpoint:** `ws://<host>:8080/api/v1/ws?token=<access_token>`

> Since WebSocket does not support custom headers, the JWT token is passed as a URL query parameter.

See [`websocket/asyncapi.yaml`](websocket/asyncapi.yaml) for the AsyncAPI 2.6.0 specification.

#### gRPC (Internal)

gRPC is used for inter-service communication and is not intended for direct client use. The proto definitions are provided as a reference for data structures.

| Service | Proto File | Description |
|---------|-----------|-------------|
| auth | `proto/auth/auth.proto` | Login, register, token refresh |
| user | `proto/user/user.proto` | User profile, search |
| message | `proto/message/message.proto` | Send, fetch, recall messages |
| session | `proto/session/session.proto` | Session list, read status |
| friend | `proto/friend/friend.proto` | Friend relations, requests |
| group | `proto/group/group.proto` | Group management, members |
| file | `proto/file/file.proto` | File upload, download |
| push | `proto/push/push.proto` | Offline push notifications |
| rtc | `proto/rtc/rtc.proto` | Audio/video calls |
| sync | `proto/sync/sync.proto` | Multi-device sync |

### API Conventions

#### HTTP Response Format

```json
{
  "code": 0,
  "message": "success",
  "data": {}
}
```

- `code = 0`: Success
- `code != 0`: Failure, `message` contains the error description
- Error code ranges: auth=10xxx, user=20xxx, friend=30xxx, group=40xxx, message=50xxx

#### Pagination

```json
{
  "page": 1,
  "pageSize": 20,
  "total": 100,
  "list": []
}
```

#### Timestamps

All time fields use Unix timestamps in **milliseconds** (`int64`).

#### Message Types

| Value | Description |
|-------|-------------|
| 1 | Text message |
| 2 | Image message |
| 3 | Audio message |
| 4 | Video message |
| 5 | File message |
| 6 | Location message |
| 100 | System message |

### Version Info

- Backend source version: see [`.backend-version`](.backend-version)
- Last synced: see [`.last-synced`](.last-synced)

---

## 中文

AnyChat IM 后端系统的 API 契约仓库，包含 HTTP REST、WebSocket 及 gRPC 接口定义。

### 项目背景

AnyChat 是一个微服务 IM 后端系统，由 12 个独立服务组成，通过 gRPC 和 HTTP 通信，NATS 处理异步消息。本仓库是所有对外接口的规格定义，由后端 CI 自动同步。

### 目录结构

```
├── http/
│   └── openapi.json       # HTTP REST API（OpenAPI 3.0）
├── websocket/
│   └── asyncapi.yaml      # WebSocket / 事件驱动 API（AsyncAPI 2.6.0）
├── proto/
│   ├── common/            # 公共类型
│   ├── auth/              # 认证服务
│   ├── user/              # 用户资料、搜索
│   ├── message/           # 消息发送、拉取、撤回
│   ├── session/           # 会话列表、已读状态
│   ├── friend/            # 好友关系、申请
│   ├── group/             # 群组管理、成员
│   ├── file/              # 文件上传、下载
│   ├── push/              # 离线推送
│   ├── rtc/               # 音视频通话
│   └── sync/              # 多设备同步
└── CLAUDE.md              # AI 助手上下文
```

### 接入方式

#### HTTP REST API

客户端统一通过 `gateway-service` 的 HTTP 接口接入。

**Base URL：** `http://<host>:8080/api/v1`

**认证：** 所有受保护接口需携带 `Authorization: Bearer <access_token>`

完整规格见 [`http/openapi.json`](http/openapi.json)（OpenAPI 3.0）。

#### WebSocket

实时消息通过 WebSocket 长连接推送。

**连接地址：** `ws://<host>:8080/api/v1/ws?token=<access_token>`

> 由于 WebSocket 协议不支持自定义 Header，JWT Token 通过 URL query 参数传递。

完整规格见 [`websocket/asyncapi.yaml`](websocket/asyncapi.yaml)（AsyncAPI 2.6.0）。

#### gRPC（内部服务）

gRPC 用于服务间内部通信，客户端通常不直接使用。Proto 定义供参考数据结构。

| 服务 | Proto 文件 | 说明 |
|------|-----------|------|
| auth | `proto/auth/auth.proto` | 登录、注册、Token 刷新 |
| user | `proto/user/user.proto` | 用户资料、搜索 |
| message | `proto/message/message.proto` | 消息发送、拉取、撤回 |
| session | `proto/session/session.proto` | 会话列表、已读状态 |
| friend | `proto/friend/friend.proto` | 好友关系、申请 |
| group | `proto/group/group.proto` | 群组管理、成员 |
| file | `proto/file/file.proto` | 文件上传、下载 |
| push | `proto/push/push.proto` | 离线推送 |
| rtc | `proto/rtc/rtc.proto` | 音视频通话 |
| sync | `proto/sync/sync.proto` | 多设备同步 |

### 接口约定

#### HTTP 响应格式

```json
{
  "code": 0,
  "message": "success",
  "data": {}
}
```

- `code = 0`：成功
- `code != 0`：失败，`message` 为错误描述
- 错误码段：auth=10xxx, user=20xxx, friend=30xxx, group=40xxx, message=50xxx

#### 分页约定

```json
{
  "page": 1,
  "pageSize": 20,
  "total": 100,
  "list": []
}
```

#### 时间格式

所有时间字段使用 Unix 时间戳（**毫秒**），类型为 `int64`。

#### 消息类型枚举

| 值 | 说明 |
|----|------|
| 1 | 文本消息 |
| 2 | 图片消息 |
| 3 | 语音消息 |
| 4 | 视频消息 |
| 5 | 文件消息 |
| 6 | 位置消息 |
| 100 | 系统消息 |

### 版本信息

- 后端来源版本：见 [`.backend-version`](.backend-version)
- 最后同步时间：见 [`.last-synced`](.last-synced)

---

## License

[MIT](LICENSE)
