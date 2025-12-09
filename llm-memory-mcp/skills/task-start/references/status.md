# Todo 状态说明

## 状态值

| 状态码 | 名称 | 说明 |
|--------|------|------|
| 0 | pending | 待处理，任务已创建但未开始 |
| 1 | in_progress | 进行中，Agent 正在处理 |
| 2 | completed | 已完成，任务成功完成 |
| 3 | cancelled | 已取消，任务不再需要 |

## 状态流转

```
创建任务
    ↓
[0] pending (待处理)
    ↓
task-start
    ↓
[1] in_progress (进行中)
    ↓
task-complete / task-cancel
    ↓
[2] completed (已完成)  或  [3] cancelled (已取消)
```

## 状态检查

调用 `todo_list` 查看任务状态：

```javascript
const todos = await todo_list({ scope: "all" });

// 过滤待处理任务
const pending = todos.filter(t => t.status === 0);

// 过滤进行中任务
const inProgress = todos.filter(t => t.status === 1);

// 过滤已完成任务
const completed = todos.filter(t => t.status === 2);
```

## 并发安全

当任务状态为 `in_progress` 时：
- 其他 Agent 应跳过该任务
- 避免重复处理同一任务
- 主 Agent 可以监控进度

```
Agent-A 调用 task-start(todo-xxx)
    ↓
todo-xxx 状态变为 in_progress
    ↓
Agent-B 查看 todo_list
    ↓
发现 todo-xxx 已在处理中
    ↓
Agent-B 跳过 todo-xxx
```
