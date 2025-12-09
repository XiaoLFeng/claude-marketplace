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
- 需要取消任务 → 使用 `llm-memory todo cancel`
- 规划阶段

## 操作流程

### 单个任务完成

```bash
llm-memory todo done todo-auth-login
```

### 批量完成

```bash
llm-memory todo done todo-auth-login todo-auth-register todo-auth-jwt
```

## 状态变化

```
完成前: status = in_progress (进行中)
完成后: status = completed (已完成)
```

## 使用场景

### 场景 1: Task Agent 完成工作

```markdown
Task Agent 完成代码编写后：

1. 确认代码已提交/保存
2. 调用 task-complete skill
3. 执行: llm-memory todo done todo-auth-login
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

## 输出示例

```
✅ 任务已完成

📋 [todo-auth-login] 实现登录功能
   状态: 进行中 → 已完成
   完成时间: 2024-01-15 11:45

太棒了！继续下一个任务~
```

## CLI 命令

- `llm-memory todo done <code>` - 标记任务为已完成
- `llm-memory todo done <code1> <code2> ...` - 批量完成

详见：[状态说明](./references/status.md)
