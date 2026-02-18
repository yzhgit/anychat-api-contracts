# AnyChat API Contracts

> 此文件是 `anychat-api-contracts` 仓库的 CLAUDE.md 模板。
> SDK/客户端项目通过 git submodule 引入本仓库后，在各自的 CLAUDE.md 中 @import 本文件。

## 项目背景

AnyChat 是一个微服务 IM 后端系统，由 12 个独立服务组成，通过 gRPC 和 HTTP 通信，NATS 处理异步消息。
本仓库是 AnyChat 后端的 **API 契约仓库**，包含所有对外接口的规格定义，由后端 CI 自动同步。

## 接入方式

- **HTTP REST API**：客户端统一通过 `gateway-service` 的 HTTP 接口接入
- **WebSocket**：实时消息通过 WebSocket 长连接，连接地址 `ws://<host>:8080/ws`
- **认证**：所有受保护接口需携带 `Authorization: Bearer <access_token>`

## HTTP API 规格 (OpenAPI 3.0)

@./http/openapi.json

## WebSocket / 事件驱动 API 规格 (AsyncAPI 2.6.0)

@./websocket/asyncapi.yaml

## gRPC 接口定义 (Proto3)

> SDK 通常不直接使用 gRPC（gRPC 为服务间内部通信），以下供参考数据结构。

### 核心服务

| 服务 | Proto 文件 | 说明 |
|------|-----------|------|
| auth | @./proto/auth/auth.proto | 登录、注册、Token 刷新 |
| user | @./proto/user/user.proto | 用户资料、搜索 |
| message | @./proto/message/message.proto | 消息发送、拉取、撤回 |
| session | @./proto/session/session.proto | 会话列表、已读状态 |
| friend | @./proto/friend/friend.proto | 好友关系、申请 |
| group | @./proto/group/group.proto | 群组管理、成员 |
| file | @./proto/file/file.proto | 文件上传、下载 |
| push | @./proto/push/push.proto | 离线推送 |
| rtc | @./proto/rtc/rtc.proto | 音视频通话 |
| sync | @./proto/sync/sync.proto | 多设备同步 |

### 公共类型

@./proto/common/common.proto

## 接口约定

### HTTP 响应格式

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

### 分页约定

```json
{
  "page": 1,
  "pageSize": 20,
  "total": 100,
  "list": []
}
```

### 时间格式

所有时间字段使用 Unix 时间戳（毫秒），类型为 `int64`。

### 消息类型枚举

| 值 | 说明 |
|----|------|
| 1 | 文本消息 |
| 2 | 图片消息 |
| 3 | 语音消息 |
| 4 | 视频消息 |
| 5 | 文件消息 |
| 6 | 位置消息 |
| 100 | 系统消息 |

## 版本信息

后端来源版本见 `.backend-version` 文件，最后同步时间见 `.last-synced` 文件。
