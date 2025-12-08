---
name: search-history
description: |
  历史查询器 (CLI版本) - 全局搜索 memory/plan/todo 的历史记录。

  **何时调用此 Skill：**
  - 用户询问"之前怎么做的"
  - 需要查找历史记录
  - 搜索相关内容
  - 用户说"查找..."、"搜索..."、"历史..."

  **不调用此 Skill：**
  - 只需要列出全部（使用对应的 list 命令）
  - 创建新记录（使用对应的 create 命令）
  - 加载全局上下文（使用 load-context）
---

# Search History Skill

全局搜索 memory/plan/todo 的历史记录，快速找到相关信息。

## ⚡ 快速参考

### 触发关键词

```
历史相关：
  - "之前..."、"历史..."
  - "上次..."、"以前..."
  - "曾经..."、"过去..."

查询相关：
  - "怎么做的"、"记录..."
  - "查找..."、"搜索..."
  - "有没有..."、"找一下..."
```

### 搜索范围

```
Memory:   搜索标题和内容
Plan:     搜索标题和描述
Todo:     搜索标题和描述
```

### 结果限制

```
每类最多返回 10 条
按相关性/时间排序
```

---

## 🔧 核心操作

### 搜索历史

**CLI 命令**：

```bash
# 搜索记忆
./main memory search --keyword "关键词"

# 列出计划（手动过滤）
./main plan list | grep "关键词"

# 列出待办（手动过滤）
./main todo list | grep "关键词"
```

### 综合搜索脚本

```bash
#!/bin/bash
# search-history.sh

KEYWORD=$1

echo "🔍 搜索 \"$KEYWORD\" 的结果："
echo ""

echo "### 💡 Memory 记录"
./main memory search --keyword "$KEYWORD" | head -10
echo ""

echo "### 📋 Plan 计划"
./main plan list | grep -i "$KEYWORD" | head -10
echo ""

echo "### ✅ Todo 任务"
./main todo list | grep -i "$KEYWORD" | head -10
```

---

## 📊 完整示例

### 示例 1：查询认证相关记录

**执行搜索**：

```bash
./search-history.sh "认证"
```

**输出**：

```
🔍 搜索 "认证" 的结果：

### 💡 Memory 记录
[mem-jwt-decision] JWT 认证方案选型 - 架构决策
[mem-auth-flow] 用户认证流程设计 - 技术文档

### 📋 Plan 计划
[plan-auth-refactor] 用户认证系统重构 - in_progress (45%)

### ✅ Todo 任务
[todo-auth-refactor-1-1] 设计 users 表结构 - completed
[todo-auth-refactor-2-1] 实现 JWT 生成逻辑 - in_progress
```

### 示例 2：查找 Bug 修复记录

**执行搜索**：

```bash
./search-history.sh "内存泄漏"
```

**输出**：

```
🔍 搜索 "内存泄漏" 的结果：

### 💡 Memory 记录
[mem-memory-leak-fix] 内存泄漏问题排查与解决 - 问题排查

### ✅ Todo 任务
[todo-fix-memory-leak] 修复内存泄漏问题 - completed
```

---

## 🎯 使用场景

### 场景 1：查询历史决策

```bash
# 查询为什么选择 JWT
./main memory search --keyword "JWT"
```

### 场景 2：查找解决方案

```bash
# 查找之前类似问题的解决方案
./search-history.sh "超时"
```

### 场景 3：检查任务状态

```bash
# 查看某功能相关的任务状态
./main todo list | grep "登录"
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
- [Load Context](../load-context/SKILL.md) - 上下文加载
- [架构迁移](../shared-references/architecture-migration.md) - 从 workflow-orchestrator 迁移
