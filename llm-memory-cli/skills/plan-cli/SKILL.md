---
name: plan-cli
description: |
  LLM-Memory 计划管理工具 (CLI版本) - 跟踪多步骤目标，管理长期项目进度。

  **何时调用此 Skill：**
  - 用户说"帮我规划"、"制定计划"、"创建方案"
  - 任务复杂度高（>3步骤）且时间跨度长（>3天）
  - 用户需要"跟踪进度"、"更新计划"

  **不调用此 Skill：**
  - 单一简单任务（使用 todo-cli）
  - 短期任务（<1天，使用 todo-cli）
---

# Plan CLI 管理 Skill

管理复杂的多步骤项目，跟踪长期目标和整体进度。

## ⚡ 快速参考

### Code 格式

```
全小写 + 连字符，字母开头/结尾，≥3 字符
推荐：plan-<简短描述>
✅ plan-user-system  ❌ Plan_001
活跃状态唯一（pending/in_progress）
```

### Plan vs Todo 选择

**使用 Plan：**
- 多步骤复杂任务（>3步）
- 长期目标（>3天）
- 需要跟踪整体进度（0-100%）
- 有子任务依赖关系

**使用 Todo：**
- 单一任务
- 短期行动项（<1天）
- 独立任务（无依赖）

### 进度和状态

```
progress=0     → pending（待开始）
progress=1-99  → in_progress（进行中）
progress=100   → completed（已完成）
```

---

## 🔧 核心操作

### 创建计划

```bash
./main plan create \
  --code "plan-xxx" \
  --title "计划标题" \
  --description "计划摘要" \
  --content "# 详细内容\n\n- 步骤1\n- 步骤2" \
  [--global]
```

### 进度管理

```bash
# 开始计划
./main plan start --code "plan-xxx"

# 更新进度（0-100）
./main plan progress --code "plan-xxx" --progress 50

# 完成计划
./main plan complete --code "plan-xxx"
```

### 查看和管理

```bash
# 列出所有计划
./main plan list

# 获取详情
./main plan get --code "plan-xxx"

# 删除计划
./main plan delete --code "plan-xxx"
```

---

## 📚 CLI 命令清单

- `plan create` - 创建计划
- `plan list` - 列出计划
- `plan start` - 开始计划
- `plan progress` - 更新进度
- `plan complete` - 完成计划
- `plan delete` - 删除计划

详见：[完整命令参考](./references/commands.md)

---

## 🔗 参考文档

- [完整命令参考](./references/commands.md) - 所有命令的详细说明
- [使用示例](./references/examples.md) - 真实场景案例
- [Code 格式](../shared-references/code-format.md) - 格式规则详解
- [故障排除](../shared-references/troubleshooting.md) - 错误处理
- [最佳实践](../shared-references/best-practices.md) - 优化建议
