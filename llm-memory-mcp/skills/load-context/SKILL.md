---
name: load-context
description: "Context loader - Load relevant context for new conversation or Plan mode. Reads plan_list/todo_list/memory_search to get unfinished tasks and memories. Use when: new conversation starts, entering Plan mode, user asks about current status."
---

# Load Context

新对话或 Plan 模式时加载相关上下文，帮助了解项目状态。

## 触发条件

- 新对话开始
- 进入 Plan 模式
- 用户说"当前状态"、"有什么进展"、"项目情况"
- 需要了解项目背景

## 不触发条件

- 已经了解项目状态
- 执行具体任务中
- 搜索特定历史 → 使用 search-history

## 加载策略

### 快速加载（新对话）

适用于新对话开始，快速了解概况：

```javascript
// 1. 获取进行中的计划
const plans = await plan_list({ scope: "all" });
const activePlans = plans.filter(p => p.status === "in_progress");

// 2. 获取待处理的任务
const todos = await todo_list({ scope: "all" });
const pendingTodos = todos.filter(t => t.status === 0 || t.status === 1);

// 3. 简要展示
```

### 深度加载（Plan 模式）

适用于进入 Plan 模式，详细了解上下文：

```javascript
// 1. 获取所有计划
const plans = await plan_list({ scope: "all" });

// 2. 获取所有任务
const todos = await todo_list({ scope: "all" });

// 3. 搜索相关记忆
const memories = await memory_search({ keyword: "项目关键词" });

// 4. 获取关键计划详情
for (const plan of activePlans) {
  const detail = await plan_get({ code: plan.code });
  // 展示详情
}

// 5. 获取关键记忆详情
for (const mem of relevantMemories) {
  const detail = await memory_get({ code: mem.code });
  // 展示详情
}
```

## 操作流程

### Step 1: 获取计划状态

```javascript
const plans = await plan_list({ scope: "all" });

// 分类
const inProgress = plans.filter(p => p.status === "in_progress");
const pending = plans.filter(p => p.status === "pending");
const completed = plans.filter(p => p.status === "completed");
```

### Step 2: 获取任务状态

```javascript
const todos = await todo_list({ scope: "all" });

// 分类
const todosPending = todos.filter(t => t.status === 0);
const todosInProgress = todos.filter(t => t.status === 1);
const todosCompleted = todos.filter(t => t.status === 2);
```

### Step 3: 搜索相关记忆（可选）

```javascript
// 如果有特定项目关键词
const memories = await memory_search({ keyword: "auth" });
```

### Step 4: 获取详情（Plan 模式）

```javascript
// 获取进行中计划的详情
for (const plan of inProgress.slice(0, 3)) {
  const detail = await plan_get({ code: plan.code });
  // 展示完整内容
}
```

## 输出示例

### 快速加载输出

```
📊 项目状态概览

📋 计划 (2 进行中):
  • [plan-user-auth] 用户认证 (45%)
  • [plan-api-refactor] API 重构 (20%)

📝 待办 (5 待处理):
  • [todo-auth-jwt] JWT 集成 [Task-A]
  • [todo-auth-test] 集成测试 [Main]
  • [todo-api-endpoints] API 端点 [Task-B]
  ...

准备好继续工作了~
```

### 深度加载输出

```
📊 项目详细状态

═══════════════════════════════════════

📋 进行中计划

[plan-user-auth] 用户认证系统 (45%)
├── 目标: 实现安全认证系统
├── 步骤:
│   ✓ 设计数据库模型
│   ✓ 实现注册功能
│   ○ JWT 集成
│   ○ 集成测试
└── Agent 分配:
    Task-A: 登录、JWT
    Task-B: 注册

═══════════════════════════════════════

📝 待处理任务

高优先级:
  • [todo-auth-jwt] JWT 集成 [Task-A] - 进行中
  • [todo-auth-test] 集成测试 [Main] - 待处理

中优先级:
  • [todo-api-endpoints] API 端点 [Task-B]

═══════════════════════════════════════

💡 相关记忆

  • [mem-auth-decision] JWT vs Session 选型
  • [mem-api-standard] API 设计规范

═══════════════════════════════════════

上下文加载完成，可以开始规划~
```

## MCP 工具

- `plan_list` - 获取计划列表
- `plan_get` - 获取计划详情
- `todo_list` - 获取任务列表
- `memory_search` - 搜索记忆
- `memory_get` - 获取记忆详情

详见：[加载策略](./references/loading-strategy.md)
