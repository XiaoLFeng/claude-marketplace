# Record Decision - 使用示例

## 示例 1：技术方案选型

### 场景

团队讨论认证方案，最终选择 JWT

### 对话过程

```
用户：我们需要选择认证方案，JWT 还是 Session？

助手：让我们对比一下两个方案...
[详细讨论过程]

助手：基于以上分析，建议选择 JWT + Refresh Token 方案。

用户：好的，就用这个方案。
```

### 自动记录

```javascript
// record-decision 识别到技术选型场景
await memory_create({
  code: "mem-jwt-vs-session",
  title: "JWT vs Session 认证方案选型",
  content: `
# JWT vs Session 认证方案选型

## 背景
需要为新的用户认证系统选择合适的认证方案。

## 候选方案

### 方案 1: JWT (JSON Web Token)
- **优点**：
  - 无状态，服务器无需存储会话
  - 易于水平扩展
  - 跨域友好
  - 移动端友好
- **缺点**：
  - 无法主动失效
  - Token 体积较大
  - 需要处理 Token 刷新

### 方案 2: Session
- **优点**：
  - 可主动失效
  - 安全性高
  - 实现简单
- **缺点**：
  - 需要服务器存储
  - 扩展困难
  - 跨域麻烦

## 最终决策
选择 JWT + Refresh Token 方案

## 理由
1. 系统需要支持水平扩展
2. 需要同时支持移动端和 Web 端
3. 使用 Refresh Token 机制解决无法主动失效的问题
4. 通过 Token 黑名单处理注销场景

## 影响
- 需要实现 Token 黑名单机制
- 需要设计 Refresh Token 存储方案（Redis）
- 前端需要处理 Token 刷新逻辑

## 参考资料
- [JWT 最佳实践](https://datatracker.ietf.org/doc/html/rfc7519)
- [OAuth 2.0 规范](https://oauth.net/2/)
  `,
  category: "架构决策",
  tags: ["auth", "jwt", "security", "architecture"],
  priority: 3,
  global: false
});
```

---

## 示例 2：复杂 Bug 排查

### 场景

生产环境内存泄漏，排查 2 小时后找到原因

### 对话过程

```
用户：生产环境内存一直在涨，已经崩溃了两次

助手：让我帮你分析一下...
[排查过程]

用户：终于找到了！是 WebSocket 断开重连时没有清理旧的监听器

助手：确认是这个原因。让我记录下这次排查过程。
```

### 自动记录

```javascript
// record-decision 识别到 Bug 排查场景
await memory_create({
  code: "mem-memory-leak-websocket",
  title: "WebSocket 内存泄漏问题排查与解决",
  content: `
# WebSocket 内存泄漏问题排查与解决

## 背景
生产环境出现内存持续增长问题，最终导致 OOM 崩溃。
- 首次发现时间：2024-12-01 10:00
- 影响范围：所有 WebSocket 连接的用户
- 崩溃次数：2 次

## 排查过程

### 1. 收集内存快照
使用 heapdump 在不同时间点获取内存快照

### 2. 分析内存增长
对比多个快照，发现 EventEmitter 监听器数量持续增长

### 3. 定位问题代码
追踪到 WebSocket 连接管理模块

### 4. 确认根因
WebSocket 断开重连时，旧的事件监听器没有被移除

## 根本原因
\`\`\`javascript
// 问题代码
socket.on('disconnect', () => {
  // 重连逻辑，但没有清理旧监听器
  socket.connect();
});
\`\`\`

每次重连都会添加新的监听器，导致内存泄漏。

## 解决方案
\`\`\`javascript
// 修复后
socket.on('disconnect', () => {
  socket.removeAllListeners();  // 先清理
  socket.connect();
});
\`\`\`

## 预防措施
1. 添加 EventEmitter 监听器数量监控告警
2. 代码 Review 检查点增加事件监听清理
3. 增加内存使用率监控和告警

## 相关代码
- 文件：src/services/websocket.js
- 修复 PR：#1234
  `,
  category: "问题排查",
  tags: ["bug", "memory-leak", "nodejs", "websocket", "production"],
  priority: 4,
  global: false
});
```

---

## 示例 3：规范制定

### 场景

团队统一 API 响应格式

### 对话过程

```
用户：我们需要统一 API 响应格式

助手：好的，让我们定义一个标准格式...

用户：就用 { code, data, message, timestamp } 这个结构
```

### 自动记录

```javascript
await memory_create({
  code: "mem-api-response-format",
  title: "API 响应格式规范",
  content: `
# API 响应格式规范

## 标准响应结构

\`\`\`typescript
interface ApiResponse<T> {
  code: number;      // 业务状态码
  data: T | null;    // 响应数据
  message: string;   // 提示信息
  timestamp: number; // 响应时间戳
}
\`\`\`

## 状态码定义

| 范围 | 说明 |
|------|------|
| 0 | 成功 |
| 1xxx | 客户端错误（参数、权限等）|
| 2xxx | 服务端错误（内部错误）|
| 3xxx | 第三方服务错误 |

## 示例

### 成功响应
\`\`\`json
{
  "code": 0,
  "data": { "id": 1, "name": "张三" },
  "message": "success",
  "timestamp": 1701408000000
}
\`\`\`

### 错误响应
\`\`\`json
{
  "code": 1001,
  "data": null,
  "message": "用户名已存在",
  "timestamp": 1701408000000
}
\`\`\`

## 适用范围
- 所有 REST API 接口
- 不包括文件下载等特殊接口
  `,
  category: "设计规范",
  tags: ["api", "convention", "backend"],
  priority: 3,
  global: true  // 全局可见
});
```

---

## 示例 4：手动触发

### 场景

用户明确要求记录决策

### 对话过程

```
用户：记录这个决策，我们决定使用 PostgreSQL 作为主数据库，因为需要复杂查询和事务支持
```

### 记录

```javascript
await memory_create({
  code: "mem-postgresql-selection",
  title: "PostgreSQL 数据库选型",
  content: `
# PostgreSQL 数据库选型

## 决策
选择 PostgreSQL 作为主数据库

## 理由
1. 需要支持复杂查询（JOIN、子查询等）
2. 需要完整的事务支持（ACID）
3. 需要支持 JSON 数据类型
4. 团队有 PostgreSQL 使用经验

## 备选方案
- MySQL：不选择，因为 JSON 支持不够好
- MongoDB：不选择，因为需要事务支持
  `,
  category: "架构决策",
  tags: ["database", "postgresql", "architecture"],
  priority: 3,
  global: false
});
```
