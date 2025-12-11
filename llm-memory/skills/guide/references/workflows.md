# 工作流指南

## 典型工作流程图

```
┌─────────────────────────────────────────────────────────────┐
│                      新对话开始                              │
└─────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│  初始化：plan_list() + todo_list()                          │
│  展示当前状态                                                │
└─────────────────────────────────────────────────────────────┘
                              │
          ┌───────────────────┼───────────────────┐
          ▼                   ▼                   ▼
    ┌──────────┐        ┌──────────┐        ┌──────────┐
    │ 新项目    │        │ 继续任务  │        │ 查询历史  │
    └──────────┘        └──────────┘        └──────────┘
          │                   │                   │
          ▼                   ▼                   ▼
    plan_create         todo_batch_start    memory_search
    todo_batch_create   执行代码编写         memory_get
          │                   │
          └─────────┬─────────┘
                    ▼
            todo_batch_complete
            plan_update (进度)
                    │
                    ▼
            memory_create (记录经验)
```

---

## 新项目启动流程

### Step 1: 创建计划

```javascript
await plan_create({
  code: "plan-user-auth",
  title: "用户认证系统",
  description: "实现完整的用户认证和授权功能",
  content: `## 项目目标

实现安全、可靠的用户认证系统。

## 功能范围

- 用户注册/登录
- JWT Token 认证
- 权限管理
- 会话管理

## 技术选型

- JWT 认证
- bcrypt 密码加密
- Redis 存储 Token

## 里程碑

1. 基础认证（25%）
2. JWT 集成（50%）
3. 权限管理（75%）
4. 测试完善（100%）
`
});
```

### Step 2: 拆解任务

```javascript
await todo_batch_create({
  items: [
    // 基础认证
    { code: "todo-auth-model", title: "设计用户数据模型", priority: 3 },
    { code: "todo-auth-register", title: "实现注册功能", priority: 3 },
    { code: "todo-auth-login", title: "实现登录功能", priority: 3 },

    // JWT 集成
    { code: "todo-auth-jwt-gen", title: "JWT 生成逻辑", priority: 2 },
    { code: "todo-auth-jwt-verify", title: "JWT 验证中间件", priority: 2 },

    // 权限管理
    { code: "todo-auth-rbac", title: "RBAC 权限模型", priority: 2 },
    { code: "todo-auth-permission", title: "权限验证装饰器", priority: 2 },

    // 测试
    { code: "todo-auth-test-unit", title: "单元测试", priority: 1 },
    { code: "todo-auth-test-e2e", title: "端到端测试", priority: 1 }
  ]
});
```

### Step 3: 分配 Agent（如需并行）

更新任务描述，添加 Agent 标注：

```javascript
await todo_batch_update({
  items: [
    { code: "todo-auth-model", description: "[Main] 设计用户数据模型" },
    { code: "todo-auth-register", description: "[Task-A] 实现注册功能" },
    { code: "todo-auth-login", description: "[Task-A] 实现登录功能" },
    { code: "todo-auth-jwt-gen", description: "[Task-B] JWT 生成逻辑" },
    { code: "todo-auth-jwt-verify", description: "[Task-B] JWT 验证中间件" }
  ]
});
```

---

## 任务执行流程

### 单任务执行

```javascript
// 1. 开始任务
await todo_batch_start({ codes: ["todo-auth-login"] });

// 2. 执行代码编写
// ... 实际编码工作 ...

// 3. 完成任务
await todo_batch_complete({ codes: ["todo-auth-login"] });

// 4. 更新计划进度（可选，批量完成后统一更新更好）
await plan_update({ code: "plan-user-auth", progress: 15 });
```

### 批量任务执行

```javascript
// 1. 批量开始
await todo_batch_start({
  codes: ["todo-auth-register", "todo-auth-login"]
});

// 2. 依次执行
// ... 实际编码工作 ...

// 3. 批量完成
await todo_batch_complete({
  codes: ["todo-auth-register", "todo-auth-login"]
});

// 4. 计算并更新进度
const todos = await todo_list({ scope: "all" });
const authTodos = todos.filter(t => t.code.startsWith("todo-auth-"));
const completed = authTodos.filter(t => t.status === 2).length;
const progress = Math.round((completed / authTodos.length) * 100);
await plan_update({ code: "plan-user-auth", progress });
```

---

## Agent 并行协作流程

### 文件隔离原则

```
Task-A 负责：
├── src/auth/login.ts
├── src/auth/register.ts
└── tests/auth/login.test.ts

Task-B 负责：
├── src/middleware/jwt.ts
├── src/utils/token.ts
└── tests/middleware/jwt.test.ts

Main Agent 负责：
├── src/models/user.ts（数据模型）
├── src/types/auth.ts（类型定义）
└── 协调和最终验证
```

### 并行执行流程

```
┌──────────────────────────────────────────────────────────────┐
│                    Main Agent                                │
│  1. plan_create + todo_batch_create                          │
│  2. 分配任务给 Task Agents                                    │
└──────────────────────────────────────────────────────────────┘
                              │
         ┌────────────────────┴────────────────────┐
         ▼                                         ▼
┌─────────────────────┐                 ┌─────────────────────┐
│     Task-A          │                 │     Task-B          │
│                     │                 │                     │
│ todo_batch_start    │                 │ todo_batch_start    │
│ 处理登录/注册        │                 │ 处理 JWT 相关        │
│ todo_batch_complete │                 │ todo_batch_complete │
└─────────────────────┘                 └─────────────────────┘
         │                                         │
         └────────────────────┬────────────────────┘
                              ▼
┌──────────────────────────────────────────────────────────────┐
│                    Main Agent                                │
│  1. 验证各 Agent 完成状态                                     │
│  2. plan_update 更新整体进度                                  │
│  3. memory_create 记录经验                                    │
└──────────────────────────────────────────────────────────────┘
```

---

## 进度同步流程

### 手动计算

```javascript
// 获取计划相关的所有任务
const todos = await todo_list({ scope: "all" });
const planTodos = todos.filter(t => t.code.startsWith("todo-auth-"));

// 计算进度
const total = planTodos.length;
const completed = planTodos.filter(t => t.status === 2).length;
const inProgress = planTodos.filter(t => t.status === 1).length;

// 方式1: 简单计算
const progress1 = Math.round((completed / total) * 100);

// 方式2: 含进行中权重
const progress2 = Math.round(((completed + inProgress * 0.5) / total) * 100);

// 更新计划
await plan_update({ code: "plan-user-auth", progress: progress1 });
```

### 里程碑更新

按预定义里程碑更新进度：

```javascript
// 里程碑定义（在 plan content 中）
const milestones = {
  "基础认证完成": 25,
  "JWT 集成完成": 50,
  "权限管理完成": 75,
  "测试完善": 100
};

// 达成里程碑时更新
await plan_update({ code: "plan-user-auth", progress: 50 });
```

---

## 知识记录流程

### 记录技术决策

```javascript
await memory_create({
  code: "mem-auth-jwt-vs-session",
  title: "JWT vs Session 选型决策",
  category: "技术决策",
  tags: ["auth", "jwt", "session", "架构"],
  content: `## 背景

需要为用户认证系统选择 Token 方案。

## 候选方案

### 方案 A: JWT (JSON Web Token)
- 优点：无状态、分布式友好、前后端分离
- 缺点：无法主动失效、Token 较大

### 方案 B: Session
- 优点：可主动失效、安全性高
- 缺点：需要存储、分布式复杂

## 决策

选择 **JWT** 方案。

## 原因

1. 系统需要支持微服务架构，JWT 无状态特性更适合
2. 前后端分离架构，JWT 更易于集成
3. 通过短期 Token + Refresh Token 缓解无法主动失效问题

## 后续行动

- 实现 Token 刷新机制
- 考虑添加 Token 黑名单功能
`
});
```

### 记录 Bug 修复

```javascript
await memory_create({
  code: "mem-auth-bug-token-expire",
  title: "Token 过期处理 Bug 修复",
  category: "Bug修复",
  tags: ["auth", "jwt", "bug"],
  content: `## 问题描述

用户反馈 Token 过期后无法自动刷新，需要重新登录。

## 根因分析

Refresh Token 接口返回的新 Token 未正确更新到客户端存储。

## 解决方案

1. 修复响应拦截器中的 Token 更新逻辑
2. 添加 Token 刷新失败重试机制
3. 增加单元测试覆盖此场景

## 修改文件

- src/utils/request.ts
- tests/utils/request.test.ts

## 验证方式

1. Token 过期后自动刷新
2. 刷新失败后重试 3 次
3. 重试失败后跳转登录页
`
});
```

---

## 查询历史流程

### 搜索相关记忆

```javascript
// 搜索
const results = await memory_search({ keyword: "jwt" });

// 展示结果
results.forEach(mem => {
  console.log(`[${mem.code}] ${mem.title}`);
  console.log(`  分类: ${mem.category}`);
  console.log(`  标签: ${mem.tags.join(", ")}`);
});
```

### 获取详情

```javascript
// 根据搜索结果获取详情
const detail = await memory_get({ code: "mem-auth-jwt-vs-session" });

// 展示完整内容
console.log(detail.content);
```
