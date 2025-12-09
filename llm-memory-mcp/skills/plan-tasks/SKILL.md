---
name: plan-tasks
description: "Task planner - Batch create task list during planning phase. Creates todos with agent assignment annotations and priority settings. Use when: project start, sprint planning. Not for: adding single task during execution (use task-add)."
---

# Plan Tasks

规划阶段批量创建任务列表，支持 Agent 分配标注。

## 触发条件

- 项目开始时规划任务
- Sprint/迭代规划
- 创建 Plan 后需要拆分为具体任务
- 用户说"创建任务列表"、"规划任务"

## 不触发条件

- 执行中追加单个任务 → 使用 task-add
- 管理项目计划 → 使用 manage-project
- 开始/完成任务 → 使用 task-start/complete

## 操作流程

### 批量创建任务

```javascript
await todo_batch_create({
  items: [
    {
      code: "todo-auth-login",
      title: "实现登录功能",
      description: "[Task-A] 实现用户登录 API 和前端表单",
      priority: 3
    },
    {
      code: "todo-auth-register",
      title: "实现注册功能",
      description: "[Task-B] 实现用户注册流程和验证",
      priority: 3
    },
    {
      code: "todo-auth-jwt",
      title: "JWT 集成",
      description: "[Task-A] 集成 JWT 令牌，依赖: todo-auth-login",
      priority: 2
    },
    {
      code: "todo-auth-test",
      title: "集成测试",
      description: "[Main] 执行集成测试，依赖: 全部完成后",
      priority: 2
    }
  ]
});
```

### 查看任务列表

```javascript
const todos = await todo_list({ scope: "all" });
```

## Code 命名规范

```
格式: todo-<项目>-<任务>
正则: ^[a-z][a-z\-]*[a-z]$

示例:
  todo-auth-login       ✅
  todo-api-refactor     ✅
  todo-fix-bug          ✅
```

## Agent 分配标注

在 description 中标注负责的 Agent：

```
[Task-A] 任务描述    → Task Agent A 负责
[Task-B] 任务描述    → Task Agent B 负责
[Main] 任务描述      → 主 Agent 负责
```

## 依赖关系描述

在 description 中说明依赖：

```
"[Task-A] JWT 集成，依赖: todo-auth-login 完成后"
"[Main] 集成测试，依赖: 全部任务完成后"
```

## 优先级设置

| 优先级 | 值 | 适用场景 |
|--------|-----|----------|
| 低 | 1 | 优化、文档 |
| 中 | 2 | 正常任务 |
| 高 | 3 | 核心功能 |
| 紧急 | 4 | 阻塞任务 |

## 使用场景

### 场景 1: 新项目规划

```markdown
用户: "帮我规划用户认证系统的开发任务"

1. 分析需求，确定任务列表
2. 分配 Agent 职责
3. 调用 plan-tasks skill
4. 创建所有 todos
```

### 场景 2: Sprint 规划

```markdown
用户: "规划这个 Sprint 的任务"

1. 确定 Sprint 目标
2. 拆分为具体任务
3. 评估优先级
4. 调用 plan-tasks 批量创建
```

### 场景 3: 配合 Plan 使用

```markdown
1. 先调用 manage-project 创建 Plan
2. 再调用 plan-tasks 创建关联的 Todos
3. Plan 和 Todos 通过命名关联:
   - plan-user-auth
   - todo-auth-login
   - todo-auth-register
```

## 输出示例

```
✅ 任务规划完成

📋 创建了 4 个任务:

高优先级 (3):
  • [todo-auth-login] 实现登录功能 [Task-A]
  • [todo-auth-register] 实现注册功能 [Task-B]

中优先级 (2):
  • [todo-auth-jwt] JWT 集成 [Task-A]
  • [todo-auth-test] 集成测试 [Main]

📊 Agent 分配:
  Task-A: 2 个任务 (login, jwt)
  Task-B: 1 个任务 (register)
  Main:   1 个任务 (test)

可以开始执行了~
```

## MCP 工具

- `todo_batch_create` - 批量创建任务
- `todo_list` - 查看任务列表

详见：
- [Code 格式规范](./references/code-format.md)
- [Agent 分配指南](./references/agent-guide.md)
