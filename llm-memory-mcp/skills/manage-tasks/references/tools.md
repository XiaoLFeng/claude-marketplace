# Todo MCP 工具详解

## todo_list

列出待办任务。

```javascript
todo_list({
  scope: "all"  // personal/group/all
})

// 返回
[
  {
    code: "todo-xxx",
    title: "任务标题",
    description: "描述",
    priority: 3,
    status: 0,  // 0=pending, 1=in_progress, 2=completed, 3=cancelled
    created_at: "2024-01-01T00:00:00Z"
  }
]
```

## todo_batch_create

批量创建待办（最多 100 个）。

```javascript
todo_batch_create({
  items: [
    {
      code: "todo-xxx",           // 必填：唯一标识
      title: "任务标题",           // 必填：简洁描述
      description: "详细描述",     // 可选
      priority: 2                 // 可选：1-4，默认2
    }
  ],
  scope: "personal"              // 可选：personal/group
})

// 返回混合模式结果
{
  success: 5,
  failed: 1,
  errors: [
    { code: "todo-dup", error: "Code already exists" }
  ]
}
```

## todo_batch_start

批量将任务标记为进行中。

```javascript
todo_batch_start({
  codes: ["todo-1", "todo-2"]
})
```

## todo_batch_complete

批量将任务标记为已完成。

```javascript
todo_batch_complete({
  codes: ["todo-1", "todo-2"]
})
```

## todo_batch_update

批量更新任务属性。

```javascript
todo_batch_update({
  items: [
    {
      code: "todo-xxx",          // 必填
      title: "新标题",            // 可选
      description: "新描述",      // 可选
      priority: 4,               // 可选
      status: 1                  // 可选
    }
  ]
})
```

## todo_batch_cancel

批量取消任务。

```javascript
todo_batch_cancel({
  codes: ["todo-1", "todo-2"]
})
```

## todo_final

⚠️ **危险操作**：清空所有待办（不可恢复）。

```javascript
todo_final({
  scope: "personal"  // 清空范围
})
```

## 状态码说明

| 状态码 | 名称 | 说明 |
|-------|------|------|
| 0 | pending | 待处理 |
| 1 | in_progress | 进行中 |
| 2 | completed | 已完成 |
| 3 | cancelled | 已取消 |

## 作用域说明

| Scope | 说明 |
|-------|------|
| personal | 当前目录私有（默认） |
| group | 组内共享（需先加入组） |
| all | 查询时返回全部可见 |
