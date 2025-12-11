# 使用示例

## 示例 1: 完整项目周期

### 场景
开发一个用户管理模块，包含 CRUD 功能。

### 执行过程

```javascript
// ========== 第一天：项目启动 ==========

// 1. 创建计划
await plan_create({
  code: "plan-user-crud",
  title: "用户管理模块",
  description: "实现用户的增删改查功能",
  content: `## 目标
实现完整的用户管理功能

## 功能列表
- 用户列表（分页、搜索）
- 创建用户
- 编辑用户
- 删除用户

## 技术方案
- RESTful API
- 数据库：PostgreSQL
- ORM：Prisma
`
});

// 2. 创建任务
await todo_batch_create({
  items: [
    { code: "todo-user-model", title: "设计数据模型", priority: 3 },
    { code: "todo-user-api-list", title: "用户列表 API", priority: 3 },
    { code: "todo-user-api-create", title: "创建用户 API", priority: 3 },
    { code: "todo-user-api-update", title: "更新用户 API", priority: 2 },
    { code: "todo-user-api-delete", title: "删除用户 API", priority: 2 },
    { code: "todo-user-test", title: "API 测试", priority: 1 }
  ]
});

// 3. 开始第一个任务
await todo_batch_start({ codes: ["todo-user-model"] });

// ... 设计数据模型 ...

await todo_batch_complete({ codes: ["todo-user-model"] });
await plan_update({ code: "plan-user-crud", progress: 15 });

// ========== 第二天：继续开发 ==========

// 1. 新对话初始化
const plans = await plan_list({ scope: "all" });
const todos = await todo_list({ scope: "all" });
// 展示状态：plan-user-crud 15%, 5 个任务待处理

// 2. 批量开始 API 开发
await todo_batch_start({
  codes: ["todo-user-api-list", "todo-user-api-create"]
});

// ... 开发 API ...

// 3. 完成任务
await todo_batch_complete({
  codes: ["todo-user-api-list", "todo-user-api-create"]
});
await plan_update({ code: "plan-user-crud", progress: 50 });

// ========== 第三天：完成开发 ==========

// 1. 完成剩余 API
await todo_batch_start({
  codes: ["todo-user-api-update", "todo-user-api-delete"]
});
// ... 开发 ...
await todo_batch_complete({
  codes: ["todo-user-api-update", "todo-user-api-delete"]
});
await plan_update({ code: "plan-user-crud", progress: 80 });

// 2. 完成测试
await todo_batch_start({ codes: ["todo-user-test"] });
// ... 编写测试 ...
await todo_batch_complete({ codes: ["todo-user-test"] });
await plan_update({ code: "plan-user-crud", progress: 100 });

// 3. 记录经验
await memory_create({
  code: "mem-user-crud-summary",
  title: "用户管理模块开发总结",
  category: "项目总结",
  tags: ["user", "crud", "api"],
  content: `## 完成情况
按计划完成所有功能。

## 技术亮点
- 使用 Prisma 大幅提升开发效率
- 软删除实现优雅

## 改进建议
- 可以添加批量操作功能
- 考虑添加操作日志
`
});
```

---

## 示例 2: Agent 并行开发

### 场景
前后端分离项目，需要同时开发 API 和前端页面。

### 执行过程

```javascript
// ========== Main Agent: 初始化项目 ==========

// 创建计划
await plan_create({
  code: "plan-order-module",
  title: "订单管理模块",
  description: "实现订单的创建、支付、发货功能",
  content: `## 目标
完整的订单生命周期管理

## 技术分工
- Task-A: 后端 API 开发
- Task-B: 前端页面开发
- Main: 协调和集成测试
`
});

// 创建任务并分配
await todo_batch_create({
  items: [
    // 后端任务 - Task-A
    {
      code: "todo-order-api-create",
      title: "创建订单 API",
      description: "[Task-A] POST /api/orders",
      priority: 3
    },
    {
      code: "todo-order-api-pay",
      title: "支付订单 API",
      description: "[Task-A] POST /api/orders/:id/pay",
      priority: 3
    },
    {
      code: "todo-order-api-ship",
      title: "发货 API",
      description: "[Task-A] POST /api/orders/:id/ship",
      priority: 2
    },

    // 前端任务 - Task-B
    {
      code: "todo-order-page-list",
      title: "订单列表页",
      description: "[Task-B] /orders 页面",
      priority: 3
    },
    {
      code: "todo-order-page-detail",
      title: "订单详情页",
      description: "[Task-B] /orders/:id 页面",
      priority: 3
    },
    {
      code: "todo-order-page-create",
      title: "创建订单页",
      description: "[Task-B] /orders/new 页面",
      priority: 2
    },

    // 集成任务 - Main
    {
      code: "todo-order-integration",
      title: "集成测试",
      description: "[Main] 前后端联调",
      priority: 2
    }
  ]
});

// ========== Task-A: 后端开发 ==========

// 开始任务
await todo_batch_start({
  codes: ["todo-order-api-create", "todo-order-api-pay", "todo-order-api-ship"]
});

// ... 开发后端 API ...
// 文件：src/api/orders.ts, src/services/order.ts

// 完成任务
await todo_batch_complete({
  codes: ["todo-order-api-create", "todo-order-api-pay", "todo-order-api-ship"]
});

// ========== Task-B: 前端开发 (同时进行) ==========

// 开始任务
await todo_batch_start({
  codes: ["todo-order-page-list", "todo-order-page-detail", "todo-order-page-create"]
});

// ... 开发前端页面 ...
// 文件：pages/orders/index.tsx, pages/orders/[id].tsx

// 完成任务
await todo_batch_complete({
  codes: ["todo-order-page-list", "todo-order-page-detail", "todo-order-page-create"]
});

// ========== Main Agent: 集成和收尾 ==========

// 更新进度
await plan_update({ code: "plan-order-module", progress: 80 });

// 集成测试
await todo_batch_start({ codes: ["todo-order-integration"] });
// ... 前后端联调测试 ...
await todo_batch_complete({ codes: ["todo-order-integration"] });

// 完成项目
await plan_update({ code: "plan-order-module", progress: 100 });
```

---

## 示例 3: 知识管理

### 场景
团队积累技术决策和最佳实践。

### 执行过程

```javascript
// ========== 记录技术决策 ==========

await memory_create({
  code: "mem-db-choice-postgres",
  title: "数据库选型：PostgreSQL",
  category: "技术决策",
  tags: ["database", "postgresql", "架构"],
  content: `## 背景
新项目需要选择主数据库。

## 候选方案
1. PostgreSQL
2. MySQL
3. MongoDB

## 决策
选择 **PostgreSQL**

## 原因
- 对 JSON 支持更好
- 更强大的查询能力
- 扩展性好（PostGIS 等）
- 团队熟悉度高

## 影响
- ORM 选择 Prisma
- 部署需要 PostgreSQL 实例
`
});

// ========== 记录最佳实践 ==========

await memory_create({
  code: "mem-api-design-standard",
  title: "RESTful API 设计规范",
  category: "开发规范",
  tags: ["api", "rest", "规范"],
  content: `## URL 设计

\`\`\`
GET    /api/users          # 列表
GET    /api/users/:id      # 详情
POST   /api/users          # 创建
PUT    /api/users/:id      # 全量更新
PATCH  /api/users/:id      # 部分更新
DELETE /api/users/:id      # 删除
\`\`\`

## 响应格式

\`\`\`json
{
  "code": 0,
  "message": "success",
  "data": {}
}
\`\`\`

## 错误码

- 400: 请求参数错误
- 401: 未认证
- 403: 无权限
- 404: 资源不存在
- 500: 服务器错误
`
});

// ========== 搜索知识 ==========

// 搜索 API 相关
const apiMemories = await memory_search({ keyword: "api" });
// 结果：mem-api-design-standard, mem-auth-jwt-decision, ...

// 搜索数据库相关
const dbMemories = await memory_search({ keyword: "database" });
// 结果：mem-db-choice-postgres, ...

// 获取详情
const apiStandard = await memory_get({ code: "mem-api-design-standard" });
console.log(apiStandard.content);
```

---

## 示例 4: Bug 追踪

### 场景
发现并修复生产环境 Bug。

### 执行过程

```javascript
// ========== 发现 Bug ==========

// 创建紧急任务
await todo_batch_create({
  items: [{
    code: "todo-bug-login-crash",
    title: "修复登录崩溃问题",
    description: "用户反馈登录页面偶发崩溃",
    priority: 4  // 紧急
  }]
});

// 立即开始
await todo_batch_start({ codes: ["todo-bug-login-crash"] });

// ========== 修复 Bug ==========

// ... 定位问题、修复代码 ...

// 完成修复
await todo_batch_complete({ codes: ["todo-bug-login-crash"] });

// ========== 记录修复经验 ==========

await memory_create({
  code: "mem-bug-login-crash-fix",
  title: "登录页面崩溃 Bug 修复",
  category: "Bug修复",
  tags: ["bug", "login", "crash", "hotfix"],
  content: `## 问题现象
用户在登录页面点击登录按钮后，页面偶发白屏崩溃。

## 影响范围
- 影响版本：v1.2.0 - v1.2.3
- 影响用户：约 5%

## 根因分析
1. 登录接口在特定条件下返回空响应
2. 前端未处理空响应情况
3. 后续代码访问空对象属性导致崩溃

## 解决方案
1. 后端添加响应格式校验
2. 前端添加空值判断
3. 添加全局错误边界

## 修改文件
- api/auth/login.ts（后端）
- components/LoginForm.tsx（前端）
- app/error-boundary.tsx（新增）

## 测试验证
1. 模拟空响应场景
2. 验证错误边界生效
3. 回归测试正常登录流程

## 预防措施
- 添加 E2E 测试覆盖此场景
- 代码审查时关注空值处理
`
});
```

---

## 示例 5: 多项目管理

### 场景
同时管理多个项目的进度。

### 执行过程

```javascript
// ========== 查看所有项目状态 ==========

const plans = await plan_list({ scope: "all" });

// 输出：
// [plan-user-auth] 用户认证系统 - 75% (in_progress)
// [plan-order-module] 订单管理模块 - 100% (completed)
// [plan-payment] 支付集成 - 30% (in_progress)

// ========== 查看所有待办 ==========

const todos = await todo_list({ scope: "all" });

// 按项目分组
const todosByProject = {};
todos.forEach(t => {
  const project = t.code.split('-')[1];
  if (!todosByProject[project]) {
    todosByProject[project] = [];
  }
  todosByProject[project].push(t);
});

// 输出：
// auth (3 tasks):
//   - [todo-auth-test] 集成测试 - 待处理
//   - [todo-auth-docs] 文档编写 - 待处理
//   - [todo-auth-review] 代码审查 - 进行中
//
// payment (5 tasks):
//   - [todo-payment-sdk] SDK 集成 - 进行中
//   - [todo-payment-callback] 回调处理 - 待处理
//   - ...

// ========== 优先级排序 ==========

const urgentTodos = todos
  .filter(t => t.status < 2)  // 未完成
  .sort((a, b) => b.priority - a.priority);  // 按优先级排序

// 输出：
// 🔴 [todo-auth-test] 集成测试 (P4)
// 🟠 [todo-payment-sdk] SDK 集成 (P3)
// 🟡 [todo-auth-docs] 文档编写 (P2)
// 🟢 [todo-auth-review] 代码审查 (P1)
```

---

## 快速参考卡片

### 新对话初始化

```javascript
const plans = await plan_list({ scope: "all" });
const todos = await todo_list({ scope: "all" });
```

### 创建项目

```javascript
await plan_create({ code, title, description, content });
await todo_batch_create({ items: [...] });
```

### 执行任务

```javascript
await todo_batch_start({ codes: [...] });
// ... 工作 ...
await todo_batch_complete({ codes: [...] });
await plan_update({ code, progress });
```

### 记录知识

```javascript
await memory_create({ code, title, content, category, tags });
```

### 搜索历史

```javascript
const results = await memory_search({ keyword });
const detail = await memory_get({ code });
```
