---
name: task-start
description: "Task starter - Called when agent begins processing a task. Marks todo as in-progress to prevent duplicate processing. Use when: Task Agent starts work. Not for: planning phase (use plan-tasks)."
---

# Task Start

Agent 开始处理任务时调用，标记 todo 为进行中状态。

## 触发条件

- Task Agent 开始执行任务前
- 主 Agent 开始处理某个具体 todo
- 需要"锁定"任务避免重复处理

## 不触发条件

- 规划阶段创建任务 → 使用 plan-tasks
- 完成任务 → 使用 task-complete
- 只是查看任务列表

## 操作流程

### 单个任务启动

```bash
llm-memory todo start todo-auth-login
```

### 多个任务启动

```bash
llm-memory todo start todo-auth-login todo-auth-jwt
```

## 状态变化

```
启动前: status = pending (待处理)
启动后: status = in_progress (进行中)
```

## 使用场景

### 场景 1: Task Agent 开始工作

```markdown
Task Agent 收到任务后，第一步：

1. 调用 task-start skill
2. 执行: llm-memory todo start todo-auth-login
3. 开始编写代码
```

### 场景 2: 主 Agent 开始处理

```markdown
主 Agent 决定亲自处理某任务：

1. 调用 task-start skill
2. 执行: llm-memory todo start todo-integration-test
3. 执行测试
```

## 输出示例

```
✅ 任务已启动

📋 [todo-auth-login] 实现登录功能
   状态: 待处理 → 进行中
   开始时间: 2024-01-15 10:30

现在可以开始工作了~
```

## CLI 命令

- `llm-memory todo start <code>` - 标记任务为进行中

详见：[状态说明](./references/status.md)
