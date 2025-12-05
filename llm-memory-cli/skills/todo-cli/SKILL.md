---
name: todo-cli
description: |
  LLM-Memory 待办管理工具 (CLI版本) - 管理短期任务和具体行动项。

  **何时调用此 Skill：**
  - 用户说"创建任务"、"添加待办"、"TODO"、"需要做"
  - 单一任务或短期行动项（<1天）
  - 用户需要"查看任务"、"完成任务"、"批量操作"

  **不调用此 Skill：**
  - 复杂的多步骤项目（使用 plan-cli）
  - 长期目标（>3天，使用 plan-cli）
---

# Todo CLI 管理 Skill

管理短期任务和具体行动项，支持批量操作和优先级管理。

## ⚡ 快速参考

### Code 格式

```
全小写 + 连字符，字母开头/结尾，≥3 字符
推荐：todo-<动作>-<对象>
✅ todo-fix-login-bug  ❌ Task_001
```

### 优先级规则

```
4🔴 紧急：Bug/阻塞/安全/24h内
3🟠 高：重要功能/影响体验/3天内
2🟡 中：常规任务（默认）
1🟢 低：可选改进/技术债/长期计划
```

详见：[优先级判断指南](../shared-references/priority-guide.md)

### 常用命令

```bash
# 创建待办
./main todo create --code <code> --title <title> --priority <1-4>

# 批量创建（推荐）
./main todo batch-create --json '[{"code":"t1","title":"任务1","priority":3}]'

# 管理
./main todo list
./main todo start --code <code>
./main todo complete --code <code>
```

---

## 🔧 核心操作

### 创建待办

**单个创建：**
```bash
./main todo create \
  --code "todo-xxx" \
  --title "任务标题" \
  --description "任务详情" \
  --priority 2 \
  [--global]
```

**批量创建（推荐）：**
```bash
# JSON 格式
./main todo batch-create --json '[
  {"code":"todo-1","title":"任务1","priority":3},
  {"code":"todo-2","title":"任务2","description":"详情"}
]'

# JSON 文件
./main todo batch-create --json-file ./todos.json
```

### 状态管理

```bash
# 开始任务
./main todo start --code "todo-xxx"
./main todo batch-start --codes "todo-1,todo-2"

# 完成任务
./main todo complete --code "todo-xxx"
./main todo batch-complete --codes "todo-1,todo-2"

# 标记所有为完成
./main todo final
```

### 查看和管理

```bash
# 列出所有待办
./main todo list

# 删除待办
./main todo delete --code "todo-xxx"
./main todo batch-delete --codes "todo-1,todo-2"
```

---

## 📚 CLI 命令清单

**基础命令：**
- `todo create` - 创建待办
- `todo list` - 列出待办
- `todo start` - 开始任务
- `todo complete` - 完成任务
- `todo delete` - 删除待办
- `todo final` - 完成所有

**批量命令（最多100个）：**
- `todo batch-create` - 批量创建
- `todo batch-start` - 批量开始
- `todo batch-complete` - 批量完成
- `todo batch-cancel` - 批量取消
- `todo batch-delete` - 批量删除
- `todo batch-update` - 批量更新

---

## 🔗 参考文档

- [完整命令参考](./references/commands.md) - 所有命令的详细说明
- [批量操作指南](./references/batch-operations.md) - 批量操作完整手册
- [使用示例](./references/examples.md) - 真实场景案例
- [优先级规则](../shared-references/priority-guide.md) - 详细判断指南
- [Code 格式](../shared-references/code-format.md) - 格式规则详解
- [故障排除](../shared-references/troubleshooting.md) - 错误处理
- [最佳实践](../shared-references/best-practices.md) - 优化建议
