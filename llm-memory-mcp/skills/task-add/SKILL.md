---
name: task-add
description: "Task adder - Called when discovering new tasks during execution. Creates new todo with optional agent assignment. Use when: found extra work, dependencies, bugs need tracking. Not for: batch creation in planning (use plan-tasks)."
---

# Task Add

执行过程中追加新任务，创建新的 todo。

## 触发条件

- 执行任务时发现需要额外工作
- 发现依赖任务需要先完成
- Bug 需要单独跟踪
- 代码审查发现需要修改的地方

## 不触发条件

- 规划阶段批量创建 → 使用 plan-tasks
- 完成任务 → 使用 task-complete
- 开始任务 → 使用 task-start

## 操作流程

### 追加单个任务

```javascript
await todo_batch_create({
  items: [{
    code: "todo-auth-email-verify",
    title: "实现邮箱验证",
    description: "[Task-B] 注册流程需要邮箱验证功能",
    priority: 3  // 高优先级
  }]
});
```

### 追加多个任务

```javascript
await todo_batch_create({
  items: [
    {
      code: "todo-auth-captcha",
      title: "添加验证码",
      description: "[Task-A] 登录需要验证码防护"
    },
    {
      code: "todo-auth-rate-limit",
      title: "添加限流",
      description: "[Main] 需要添加登录限流"
    }
  ]
});
```

## Code 命名规范

```
格式: todo-<项目>-<任务>
正则: ^[a-z][a-z\-]*[a-z]$

示例:
  todo-auth-email-verify    ✅
  todo-api-rate-limit       ✅
  todo-fix-login-bug        ✅
```

## Agent 标注

在 description 中标注负责的 Agent：

```
[Task-A] 任务描述   → 分配给 Task Agent A
[Task-B] 任务描述   → 分配给 Task Agent B
[Main] 任务描述     → 分配给主 Agent
```

## 优先级

| 优先级 | 值 | 说明 |
|--------|-----|------|
| 低 | 1 | 不紧急，可以稍后处理 |
| 中 | 2 | 正常优先级（默认）|
| 高 | 3 | 重要，尽快处理 |
| 紧急 | 4 | 阻塞其他任务，立即处理 |

## 使用场景

### 场景 1: 发现依赖任务

```markdown
Task Agent 在实现注册时发现需要邮箱验证：

1. 调用 task-add skill
2. 创建: todo-auth-email-verify
3. 标注: [Task-B] 当前 Agent 继续负责
4. 继续完成注册的其他部分
```

### 场景 2: 发现 Bug

```markdown
执行过程中发现已有代码的 Bug：

1. 调用 task-add skill
2. 创建: todo-fix-login-validation
3. 标注: [Main] 交给主 Agent 判断优先级
4. 继续当前任务
```

### 场景 3: 代码审查反馈

```markdown
发现需要重构的地方：

1. 调用 task-add skill
2. 创建: todo-refactor-auth-utils
3. 标注: [Task-A] 或 [Main]
4. 设置较低优先级
```

## 输出示例

```
✅ 任务已追加

📋 [todo-auth-email-verify] 实现邮箱验证
   优先级: 高 (3)
   分配给: Task-B
   状态: 待处理

已添加到任务列表，稍后处理~
```

## MCP 工具

- `todo_batch_create` - 批量创建任务

详见：
- [Code 格式规范](./references/code-format.md)
- [优先级指南](./references/priority.md)
