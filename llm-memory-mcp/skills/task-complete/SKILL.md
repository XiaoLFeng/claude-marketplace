---
name: task-complete
description: "Task completer - Called when agent finishes a task. Marks todo as completed, supports batch completion. Use when: Task Agent finishes coding, after bug fix. Not for: incomplete tasks."
---

# Task Complete

Agent 完成任务后调用，标记 todo 为已完成状态。

## 触发条件

- Task Agent 完成代码编写
- 修复 Bug 完成
- 任务目标已达成
- 即使部分完成也可标记（在备注中说明）

## 不触发条件

- 任务还在进行中
- 需要取消任务 → 使用 task-cancel（通过 todo_batch_cancel）
- 规划阶段

## 操作流程

### 单个任务完成

```javascript
await todo_batch_complete({
  codes: ["todo-auth-login"]
});
```

### 批量完成

```javascript
await todo_batch_complete({
  codes: ["todo-auth-login", "todo-auth-register", "todo-auth-jwt"]
});
```

## 状态变化

```
完成前: status = 1 (进行中)
完成后: status = 2 (已完成)
```

## 使用场景

### 场景 1: Task Agent 完成工作

```markdown
Task Agent 完成代码编写后：

1. 确认代码已提交/保存
2. 调用 task-complete skill
3. 参数: todo-auth-login
4. 返回结果给主 Agent
```

### 场景 2: 部分完成

```markdown
如果任务只能部分完成：

1. 完成能做的部分
2. 调用 task-add 创建后续任务
3. 调用 task-complete 标记当前任务完成
4. 在返回结果中说明情况
```

### 场景 3: 批量完成

```markdown
主 Agent 确认多个任务都已完成：

1. 调用 task-complete skill
2. 参数: [todo-a, todo-b, todo-c]
3. 一次性标记完成
```

## 输出示例

```
✅ 任务已完成

📋 [todo-auth-login] 实现登录功能
   状态: 进行中 → 已完成
   完成时间: 2024-01-15 11:45

太棒了！继续下一个任务~
```

## 批量完成输出

```
✅ 批量完成 3 个任务

完成的任务:
  ✓ [todo-auth-login] 实现登录功能
  ✓ [todo-auth-register] 实现注册功能
  ✓ [todo-auth-jwt] JWT 集成

进度: 3/5 完成 (60%)
```

## MCP 工具

- `todo_batch_complete` - 批量标记任务为已完成

详见：[状态说明](./references/status.md)
