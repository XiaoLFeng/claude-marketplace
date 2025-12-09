---
name: manage-tasks
description: "待办任务管理器 - 管理短期任务和行动项。适用：用户说'创建任务'、'添加待办'、'完成了'、需要跟踪短期工作(<1天)。不适用：复杂多步骤项目(>3天)用 manage-project。"
---

# Manage Tasks

管理短期待办任务，支持批量操作和优先级管理。

## 触发条件

- 用户说"创建任务"、"添加待办"、"TODO"
- 用户说"完成了"、"做完了"

## 操作指南

### 创建任务

```bash
llm-memory todo create \
  --code "todo-fix-login" \
  --title "修复登录 Bug" \
  --priority 4
```

### 批量创建

```bash
llm-memory todo batch-create --json '[
  {"code":"todo-1","title":"任务1","priority":3},
  {"code":"todo-2","title":"任务2","priority":2}
]'
```

### 完成任务

```bash
llm-memory todo complete --code "todo-fix-login"

# 批量完成
llm-memory todo batch-complete --codes "todo-1,todo-2"
```

### 查看任务

```bash
llm-memory todo list
```

## Code 格式

```
格式：todo-<动作>-<对象>
示例：todo-fix-login ✅
```

## 优先级规则

| 级别 | 图标 | 含义 |
|-----|------|------|
| 4 | 🔴 | 紧急 |
| 3 | 🟠 | 高 |
| 2 | 🟡 | 中（默认）|
| 1 | 🟢 | 低 |

## CLI 命令清单

- `llm-memory todo list` - 列出待办
- `llm-memory todo create` - 创建任务
- `llm-memory todo complete` - 完成任务
- `llm-memory todo batch-create` - 批量创建
- `llm-memory todo batch-complete` - 批量完成

详见：
- [命令详解](./references/commands.md)
- [优先级指南](./references/priority-guide.md)
- [使用示例](./references/examples.md)
