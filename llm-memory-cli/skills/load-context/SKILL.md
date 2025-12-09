---
name: load-context
description: "Context loader - Load relevant context for new conversation or Plan mode. Reads plan/todo/memory to get unfinished tasks and memories. Use when: new conversation starts, entering Plan mode, user asks about current status."
---

# Load Context

新对话或 Plan 模式时加载相关上下文，帮助了解项目状态。

## 触发条件

- 新对话开始
- 进入 Plan 模式
- 用户说"当前状态"、"有什么进展"、"项目情况"
- 需要了解项目背景

## 不触发条件

- 已经了解项目状态
- 执行具体任务中
- 搜索特定历史 → 使用 search-history

## 加载策略

### 快速加载（新对话）

```bash
# 获取进行中的计划
llm-memory plan list

# 获取待处理的任务
llm-memory todo list
```

### 深度加载（Plan 模式）

```bash
# 获取所有计划
llm-memory plan list

# 获取所有任务
llm-memory todo list

# 搜索相关记忆
llm-memory memory search "项目关键词"

# 获取关键计划详情
llm-memory plan show plan-xxx
```

## 操作流程

### Step 1: 获取计划状态

```bash
llm-memory plan list
```

### Step 2: 获取任务状态

```bash
llm-memory todo list
```

### Step 3: 搜索相关记忆（可选）

```bash
llm-memory memory search "auth"
```

### Step 4: 获取详情（Plan 模式）

```bash
llm-memory plan show plan-user-auth
```

## 输出示例

### 快速加载输出

```
📊 项目状态概览

📋 计划 (2 进行中):
  • [plan-user-auth] 用户认证 (45%)
  • [plan-api-refactor] API 重构 (20%)

📝 待办 (5 待处理):
  • [todo-auth-jwt] JWT 集成 [Task-A]
  • [todo-auth-test] 集成测试 [Main]
  ...

准备好继续工作了~
```

### 深度加载输出

```
📊 项目详细状态

═══════════════════════════════════════

📋 进行中计划

[plan-user-auth] 用户认证系统 (45%)
├── 目标: 实现安全认证系统
├── Agent 分配:
│   Task-A: 登录、JWT
│   Task-B: 注册

═══════════════════════════════════════

📝 待处理任务

高优先级:
  • [todo-auth-jwt] JWT 集成 [Task-A]

═══════════════════════════════════════

💡 相关记忆

  • [mem-auth-decision] JWT vs Session 选型

═══════════════════════════════════════
```

## CLI 命令

- `llm-memory plan list` - 获取计划列表
- `llm-memory plan show <code>` - 获取计划详情
- `llm-memory todo list` - 获取任务列表
- `llm-memory memory search <keyword>` - 搜索记忆

详见：[加载策略](./references/loading-strategy.md)
