# Todo 状态说明

## 状态值

| 状态 | 说明 |
|------|------|
| pending | 待处理，任务已创建但未开始 |
| in_progress | 进行中，Agent 正在处理 |
| completed | 已完成，任务成功完成 |
| cancelled | 已取消，任务不再需要 |

## 状态流转

```
创建任务
    ↓
pending (待处理)
    ↓
llm-memory todo start
    ↓
in_progress (进行中)
    ↓
llm-memory todo done / cancel
    ↓
completed (已完成)  或  cancelled (已取消)
```

## CLI 命令

```bash
# 创建任务（状态为 pending）
llm-memory todo add todo-xxx "任务标题"

# 开始任务（状态变为 in_progress）
llm-memory todo start todo-xxx

# 完成任务（状态变为 completed）
llm-memory todo done todo-xxx

# 取消任务（状态变为 cancelled）
llm-memory todo cancel todo-xxx

# 查看任务列表
llm-memory todo list
```

## 并发安全

当任务状态为 `in_progress` 时：
- 其他 Agent 应跳过该任务
- 避免重复处理同一任务
- 主 Agent 可以监控进度
