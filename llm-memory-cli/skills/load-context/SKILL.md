---
name: load-context
description: |
  上下文加载器 (CLI版本) - 在新对话或 Plan 模式启动时自动加载全局上下文。

  **何时调用此 Skill：**
  - 进入新对话
  - 进入 Plan 模式
  - 切换项目路径
  - 用户说"加载上下文"、"当前状态"、"有什么进展"

  **不调用此 Skill：**
  - 只需要查询历史（使用 search-history）
  - 单独操作 memory/plan/todo（使用对应的 skill）
  - 只查看单个类型的数据（使用对应的 list 命令）
---

# Load Context Skill

自动加载全局上下文，汇总展示当前项目的 memory、plan、todo 状态。

## ⚡ 快速参考

### 触发时机

```
主动触发（自动）：
  - 新对话启动
  - Plan 模式启动
  - 项目路径切换

被动触发（用户请求）：
  - "加载上下文"
  - "当前状态"
  - "有什么进展"
```

### 上下文范围

```
Memory:   最相关的 5 条记录
Plan:     所有未完成的计划（progress < 100）
Todo:     所有未完成的任务（status != completed）
```

---

## 🔧 核心操作

### 加载上下文

**CLI 命令**：

```bash
# 搜索记忆
./main memory search --keyword "关键词"

# 列出计划
./main plan list

# 列出待办
./main todo list
```

**操作流程**：

```bash
# 1. 搜索相关记忆
./main memory search --keyword "$KEYWORD" | head -5

# 2. 列出未完成计划
./main plan list | grep -v "completed"

# 3. 列出未完成待办
./main todo list | grep -v "completed"
```

### 组合命令示例

```bash
#!/bin/bash
# load-context.sh

KEYWORD=$1

echo "🔍 当前上下文加载完成"
echo ""

echo "📚 相关 Memory"
./main memory search --keyword "$KEYWORD" | head -5
echo ""

echo "📋 进行中的 Plan"
./main plan list | grep -E "(pending|in_progress)"
echo ""

echo "✅ 待完成的 Todo"
./main todo list | grep -E "(pending|in_progress)"
```

---

## 📊 展示格式

### 控制台输出示例

```
🔍 当前上下文加载完成

📚 相关 Memory（5条）
  1. [mem-jwt-decision] JWT 技术选型
  2. [mem-api-design] API 设计文档
  3. [mem-database-schema] 数据库架构
  ...

📋 进行中的 Plan（2个）
  1. [plan-auth-refactor] 用户认证系统重构 (进度: 45%)
  2. [plan-api-redesign] API 重新设计 (进度: 20%)

✅ 待完成的 Todo（8个）
  🔴 [todo-fix-login-bug] 修复登录 Bug (优先级: 4)
  🟠 [todo-add-api-docs] 添加 API 文档 (优先级: 3)
  🟡 [todo-update-readme] 更新 README (优先级: 2)
  ...
```

---

## 🎯 使用场景

### 场景 1：新对话启动

```
用户：开始新对话
↓
load-context 自动触发
↓
执行 memory/plan/todo 查询命令
↓
展示当前上下文
```

### 场景 2：手动加载

```bash
# 手动执行加载
./load-context.sh "认证"
```

---

## 📚 CLI 命令清单

本 Skill 使用以下 CLI 命令：

- `./main memory search` - 搜索记忆（来自 memory-cli）
- `./main plan list` - 列出计划（来自 plan-cli）
- `./main todo list` - 列出待办（来自 todo-cli）

详见：[完整命令参考](./references/commands.md)

---

## 🔗 参考文档

- [完整命令参考](./references/commands.md) - CLI 命令详细说明
- [使用示例](./references/examples.md) - 真实场景案例
- [Memory Skill](../memory-cli/SKILL.md) - 记忆管理
- [Plan Skill](../plan-cli/SKILL.md) - 计划管理
- [Todo Skill](../todo-cli/SKILL.md) - 待办管理
- [最佳实践](../shared-references/best-practices.md) - 优化建议
- [架构迁移](../shared-references/architecture-migration.md) - 从 workflow-orchestrator 迁移
