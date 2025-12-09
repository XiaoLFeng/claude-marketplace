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

```bash
llm-memory todo add todo-auth-email-verify "实现邮箱验证" \
  --description "[Task-B] 注册流程需要邮箱验证功能" \
  --priority 3
```

### 追加多个任务

```bash
llm-memory todo add todo-auth-captcha "添加验证码" \
  --description "[Task-A] 登录需要验证码防护"

llm-memory todo add todo-auth-rate-limit "添加限流" \
  --description "[Main] 需要添加登录限流"
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
2. 执行: llm-memory todo add todo-auth-email-verify "邮箱验证" --description "[Task-B]"
3. 继续完成注册的其他部分
```

### 场景 2: 发现 Bug

```markdown
执行过程中发现已有代码的 Bug：

1. 调用 task-add skill
2. 执行: llm-memory todo add todo-fix-login-validation "修复登录验证"
3. 继续当前任务
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

## CLI 命令

- `llm-memory todo add <code> <title>` - 创建任务
- `--description` - 设置描述（含 Agent 标注）
- `--priority` - 设置优先级 (1-4)

详见：
- [Code 格式规范](./references/code-format.md)
- [优先级指南](./references/priority.md)
