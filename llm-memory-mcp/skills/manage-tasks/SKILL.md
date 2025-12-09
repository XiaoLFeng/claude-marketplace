---
name: manage-tasks
description: "待办任务管理器 - 管理短期任务和行动项。适用：用户说'创建任务'、'添加待办'、'完成了'、需要跟踪短期工作(<1天)。不适用：复杂多步骤项目(>3天)用 manage-project。"
---

# Manage Tasks

管理短期待办任务，支持批量操作和优先级管理。

## 触发条件

- 用户说"创建任务"、"添加待办"、"TODO"、"需要做"
- 用户说"完成了"、"做完了"、"搞定了"
- 用户需要"查看任务"、"任务列表"
- 短期行动项（<1天）

## 不触发条件

- 复杂多步骤项目（>3步骤）→ 使用 manage-project
- 长期目标（>3天）→ 使用 manage-project
- 需要跟踪整体进度 → 使用 manage-project

## 操作指南

### 创建任务

```javascript
// 单个或批量创建
todo_batch_create({
  items: [
    {
      code: "todo-fix-login",
      title: "修复登录 Bug",
      description: "详细描述...",
      priority: 4  // 1低 2中 3高 4紧急
    },
    {
      code: "todo-add-test",
      title: "添加单元测试",
      priority: 2
    }
  ],
  scope: "personal"
})
```

### 开始任务

```javascript
todo_batch_start({
  codes: ["todo-fix-login"]
})
```

### 完成任务

```javascript
todo_batch_complete({
  codes: ["todo-fix-login", "todo-add-test"]
})
```

### 查看任务

```javascript
todo_list({
  scope: "all"  // personal/group/all
})
```

### 更新任务

```javascript
todo_batch_update({
  items: [
    {
      code: "todo-xxx",
      title: "新标题",
      priority: 3,
      status: 1  // 0=pending, 1=in_progress, 2=completed
    }
  ]
})
```

## Code 格式

```
格式：todo-<动作>-<对象>
规则：全小写 + 连字符，≥3字符

示例：
  todo-fix-login        ✅
  todo-add-api-docs     ✅
  todo-review-pr-123    ✅
  TODO_001              ❌
```

## 优先级规则

| 级别 | 图标 | 含义 | 适用场景 |
|-----|------|------|---------|
| 4 | 🔴 | 紧急 | Bug/阻塞/安全问题/24h内 |
| 3 | 🟠 | 高 | 重要功能/影响体验/3天内 |
| 2 | 🟡 | 中 | 常规任务（默认） |
| 1 | 🟢 | 低 | 可选改进/技术债 |

## MCP 工具清单

- `todo_list` - 列出待办
- `todo_batch_create` - 批量创建
- `todo_batch_start` - 批量开始
- `todo_batch_complete` - 批量完成
- `todo_batch_update` - 批量更新
- `todo_batch_cancel` - 批量取消
- `todo_final` - 清空所有（危险）

详见：
- [工具详解](./references/tools.md)
- [优先级指南](./references/priority-guide.md)
- [使用示例](./references/examples.md)
