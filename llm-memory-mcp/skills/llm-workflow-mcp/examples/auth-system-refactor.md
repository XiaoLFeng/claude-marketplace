# 示例 1: 用户认证系统重构 (MCP 版本)

这是一个复杂项目的完整工作流示例，展示如何使用 llm-memory MCP 工具管理大型重构任务。

## 场景描述

```
用户需求：
"我们需要将现有的 Session 认证系统重构为 JWT 认证机制。
这个改动涉及数据库、API、中间件等多个模块，预计需要一周时间。"
```

## 步骤 1：需求分析

分析这个需求的特点：

```
✅ 复杂度：高 - 涉及多个模块
✅ 时间跨度：1 周
✅ 涉及范围：数据库、API、中间件、测试
✅ 知识积累：需要记录技术决策
✅ 作用域：项目特定（personal）

结论：
- 需要创建 Plan（多步骤计划）
- 需要创建多个 Todo（具体任务）
- 需要创建 Memory（架构决策记录）
```

## 步骤 2：设计方案

向用户展示完整方案：

````markdown
📋 **工作流建议方案**

---

## Plan: 用户认证系统重构

**Code:** `plan-user-auth-refactor`
**Scope:** `personal`
**描述:** 将 Session 认证迁移到 JWT 机制
**详细内容:**
```markdown
# 用户认证系统重构计划

## 目标
将现有 Session 认证迁移到 JWT 机制，提高系统可扩展性

## 阶段划分

### 阶段 1：数据库设计 (Day 1-2)
- 设计 Token 存储表结构
- 添加 Refresh Token 机制
- 数据库迁移脚本

### 阶段 2：JWT 核心实现 (Day 3-4)
- 实现 Token 签发逻辑
- 实现 Token 验证逻辑
- 实现 Token 刷新机制

### 阶段 3：API 集成 (Day 5)
- 更新登录 API
- 更新注册 API
- 添加 Token 刷新 API

### 阶段 4：中间件更新 (Day 6)
- 更新认证中间件
- 添加权限验证
- 兼容旧 Session

### 阶段 5：测试与部署 (Day 7)
- 单元测试
- 集成测试
- 部署上线
```

---

## Todos: (共 5 个任务)

### 1️⃣ [Priority 3 🟠 高] 设计认证数据库架构
- **Code:** `todo-design-auth-db`
- **Scope:** `personal`
- **描述:** 设计 Token 存储表结构，包括刷新令牌机制

### 2️⃣ [Priority 4 🔴 紧急] 实现 JWT 令牌机制
- **Code:** `todo-impl-jwt-core`
- **Scope:** `personal`
- **描述:** 实现签发、验证、刷新逻辑（阻塞其他任务）

### 3️⃣ [Priority 3 🟠 高] 开发登录和注册 API
- **Code:** `todo-dev-auth-api`
- **Scope:** `personal`
- **描述:** 更新现有 API 支持 JWT

### 4️⃣ [Priority 2 🟡 中] 添加认证中间件
- **Code:** `todo-add-auth-middleware`
- **Scope:** `personal`
- **描述:** 实现新的认证中间件

### 5️⃣ [Priority 2 🟡 中] 编写认证单元测试
- **Code:** `todo-write-auth-tests`
- **Scope:** `personal`
- **描述:** 完整测试覆盖，目标 80%

---

## Memory: JWT 技术选型决策

**Code:** `mem-jwt-vs-session-decision`
**标题:** JWT vs Session 选型分析
**分类:** 架构决策
**Scope:** `personal`
**标签:** auth, jwt, decision, architecture
**内容:**
```markdown
# 技术选型分析

## 为什么选择 JWT

### 优点
1. **无状态**: 服务器无需存储会话信息，便于负载均衡
2. **可扩展**: 易于水平扩展，无需共享会话存储
3. **跨域友好**: 通过 Header 传递，支持多域名部署
4. **移动端友好**: 适合 RESTful API 和移动应用

### 缺点
1. **无法主动失效**: Token 在过期前无法主动撤销
2. **载荷较大**: 每次请求都需要携带完整 Token

## 解决方案

采用双 Token 机制：
- **Access Token**: 有效期 15 分钟，存储在内存中
- **Refresh Token**: 有效期 7 天，存储在 HttpOnly Cookie

## 安全措施
1. 强制 HTTPS 传输
2. Refresh Token 使用 HttpOnly + Secure + SameSite Cookie
3. Access Token 存储在 JavaScript 内存中（非 localStorage）
4. 实现 Token 黑名单机制处理注销场景

## 参考资料
- JWT Best Practices
- OWASP Authentication Cheat Sheet
```

---

**是否确认创建？**
如需调整（修改优先级、code、作用域、增删任务），请告诉我~ (｡･ω･｡)ﾉ゛
````

## 步骤 3：用户确认

```
用户回复：
"方案看起来不错，确认创建"
```

## 步骤 4：执行 MCP 工具

### 4.1 创建计划

```javascript
plan_create({
  code: "plan-user-auth-refactor",
  title: "用户认证系统重构",
  description: "将 Session 认证迁移到 JWT 机制，提高系统可扩展性",
  content: `# 用户认证系统重构计划

## 目标
将现有 Session 认证迁移到 JWT 机制，提高系统可扩展性

## 阶段划分

### 阶段 1：数据库设计 (Day 1-2)
- 设计 Token 存储表结构
- 添加 Refresh Token 机制
- 数据库迁移脚本

### 阶段 2：JWT 核心实现 (Day 3-4)
- 实现 Token 签发逻辑
- 实现 Token 验证逻辑
- 实现 Token 刷新机制

### 阶段 3：API 集成 (Day 5)
- 更新登录 API
- 更新注册 API
- 添加 Token 刷新 API

### 阶段 4：中间件更新 (Day 6)
- 更新认证中间件
- 添加权限验证
- 兼容旧 Session

### 阶段 5：测试与部署 (Day 7)
- 单元测试
- 集成测试
- 部署上线`,
  scope: "personal"
})

// 返回: Plan 创建成功! code=plan-user-auth-refactor [私有]
```

### 4.2 批量创建任务

```javascript
todo_batch_create({
  items: [
    {
      code: "todo-design-auth-db",
      title: "设计认证数据库架构",
      description: "设计 Token 存储表结构，包括 refresh_tokens 表，支持多设备登录和令牌撤销",
      priority: 3
    },
    {
      code: "todo-impl-jwt-core",
      title: "实现 JWT 令牌机制",
      description: "实现 JWT 签发、验证、刷新的核心逻辑，这是其他任务的前置依赖",
      priority: 4
    },
    {
      code: "todo-dev-auth-api",
      title: "开发登录和注册 API",
      description: "更新 /api/auth/login 和 /api/auth/register 接口，支持 JWT 认证流程",
      priority: 3
    },
    {
      code: "todo-add-auth-middleware",
      title: "添加认证中间件",
      description: "实现 JWT 验证中间件，支持 Bearer Token 和 Cookie 两种方式",
      priority: 2
    },
    {
      code: "todo-write-auth-tests",
      title: "编写认证单元测试",
      description: "为认证模块编写完整的单元测试，覆盖率目标 80%",
      priority: 2
    }
  ],
  scope: "personal"
})

// 返回: 批量创建成功! 共处理 5 个待办事项
```

### 4.3 创建知识记录

```javascript
memory_create({
  code: "mem-jwt-vs-session-decision",
  title: "JWT vs Session 选型分析",
  content: `# 技术选型分析

## 为什么选择 JWT

### 优点
1. **无状态**: 服务器无需存储会话信息，便于负载均衡
2. **可扩展**: 易于水平扩展，无需共享会话存储
3. **跨域友好**: 通过 Header 传递，支持多域名部署
4. **移动端友好**: 适合 RESTful API 和移动应用

### 缺点
1. **无法主动失效**: Token 在过期前无法主动撤销
2. **载荷较大**: 每次请求都需要携带完整 Token

## 解决方案

采用双 Token 机制：
- **Access Token**: 有效期 15 分钟，存储在内存中
- **Refresh Token**: 有效期 7 天，存储在 HttpOnly Cookie

## 安全措施
1. 强制 HTTPS 传输
2. Refresh Token 使用 HttpOnly + Secure + SameSite Cookie
3. Access Token 存储在 JavaScript 内存中（非 localStorage）
4. 实现 Token 黑名单机制处理注销场景

## 参考资料
- [JWT Best Practices](https://auth0.com/blog/jwt-handbook/)
- [OWASP Authentication Cheat Sheet](https://cheatsheetseries.owasp.org/)`,
  category: "架构决策",
  tags: ["auth", "jwt", "decision", "architecture"],
  priority: 3,
  global: false,
  scope: "personal"
})

// 返回: Memory 创建成功! code=mem-jwt-vs-session-decision [私有]
```

## 步骤 5：确认结果

```
✅ 创建成功：
- Plan 创建成功! code=plan-user-auth-refactor [私有]
- 批量创建成功! 共处理 5 个待办事项
- Memory 创建成功! code=mem-jwt-vs-session-decision [私有]
```

---

## 进度跟踪示例

### 第 1 天：开始数据库设计任务

```javascript
// 查看所有待办
todo_list({ scope: "personal" })

// 开始数据库设计任务
todo_batch_start({ codes: ["todo-design-auth-db"] })
```

### 第 2 天：完成数据库设计，记录 Schema

```javascript
// 完成任务
todo_batch_complete({ codes: ["todo-design-auth-db"] })

// 记录数据库 Schema
memory_create({
  code: "mem-refresh-token-schema",
  title: "Refresh Token 表结构设计",
  content: `\`\`\`sql
CREATE TABLE refresh_tokens (
  id BIGSERIAL PRIMARY KEY,
  user_id BIGINT NOT NULL REFERENCES users(id),
  token_hash VARCHAR(255) NOT NULL UNIQUE,
  device_info VARCHAR(255),
  ip_address VARCHAR(45),
  expires_at TIMESTAMP NOT NULL,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  revoked_at TIMESTAMP,
  INDEX idx_user_id (user_id),
  INDEX idx_token_hash (token_hash),
  INDEX idx_expires_at (expires_at)
);
\`\`\`

## 设计要点
- 使用 token_hash 存储哈希后的令牌，不存储明文
- device_info 和 ip_address 用于安全审计
- expires_at 支持自动清理过期令牌
- revoked_at 支持手动撤销令牌`,
  category: "数据库设计",
  tags: ["database", "auth", "schema"],
  priority: 3,
  global: false
})

// 开始下一个任务（JWT 实现）
todo_batch_start({ codes: ["todo-impl-jwt-core"] })
```

### 第 7 天：项目完成

```javascript
// 更新计划进度为 100%
plan_update({
  code: "plan-user-auth-refactor",
  progress: 100
})
// 自动设置状态为 completed

// 记录项目总结
memory_create({
  code: "mem-auth-refactor-summary",
  title: "认证系统重构总结",
  content: `# 项目总结

## 完成情况
- ✅ 数据库设计和迁移
- ✅ JWT 核心逻辑实现
- ✅ API 更新
- ✅ 中间件开发
- ✅ 测试覆盖 85%

## 踩坑经验
1. Refresh Token 需要考虑多设备场景
2. Token 黑名单使用 Redis 更高效
3. 前端需要处理 Token 过期自动刷新

## 性能数据
- 认证响应时间: 从 150ms 降到 50ms
- 服务器内存: 减少 30%（无需存储会话）

## 后续优化
- 考虑添加 Token 指纹防止盗用
- 实现设备管理功能
- 添加异常登录检测`,
  category: "项目总结",
  tags: ["summary", "auth", "postmortem"],
  priority: 2,
  global: false
})
```

---

## 关键学习点

### 1. Plan 使用

```
✅ 何时创建 Plan：
- 多步骤任务（>3步）
- 长期目标（>3天）
- 需要跟踪进度
- 有阶段划分

✅ Plan 的特点：
- 支持 Markdown 内容
- 自动进度管理（0-100）
- 状态自动流转（pending → in_progress → completed）
```

### 2. 批量创建任务

```
✅ 优势：
- 一次性创建多个任务
- 原子操作，要么全成功要么失败
- 返回详细的成功/失败信息

✅ 注意事项：
- 最多 100 个任务
- 检查返回的 success_count 和 fail_count
- 处理 failures 列表中的失败项
```

### 3. Memory 管理

```
✅ 重要决策必须记录：
- 技术选型理由
- 架构设计思路
- 踩坑经验总结

✅ 作用域选择：
- personal: 项目特定知识
- global: 通用最佳实践
```

### 4. 作用域隔离

```
✅ personal 作用域：
- 数据只在当前目录可见
- 切换目录后看不到
- 适合项目特定内容

✅ group 作用域：
- 需要先加入组
- 组内所有路径共享
- 适合多仓库协作
```

---

呀~ 这就是一个完整的复杂项目管理示例！希望对你有帮助~ (´∀`)💖
