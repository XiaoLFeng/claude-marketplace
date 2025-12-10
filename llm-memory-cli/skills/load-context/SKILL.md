---
name: load-context
description: "Context loader - Load relevant context for new conversation or Plan mode. AUTOMATICALLY INVOKED BY BOOTSTRAP SKILL. Reads plan/todo/memory to get unfinished tasks and memories. After completion, MUST invoke manage-project and plan-tasks. Use when: new conversation starts, entering Plan mode, user asks about current status."
---

# Load Context

新对话或 Plan 模式时加载相关上下文，帮助了解项目状态。

## 调用来源

- **bootstrap skill** (自动调用 - 新对话时) ← 主要来源
- **用户请求** (手动触发)
- **Plan 模式入口** (自动触发)

## 级联调用链

=====================================
>>> SKILL CHAIN CONTINUATION <<<
=====================================

**本 SKILL 完成后，必须继续调用：**

```
[COMPLETED] bootstrap         - 会话初始化（已完成）
[THIS]      load-context      - 加载上下文（当前）
[NEXT]      manage-project    - 获取项目计划（必须调用）
[NEXT]      plan-tasks        - 获取任务列表（必须调用）
```

**>>> 完成加载后，必须调用 manage-project 和 plan-tasks <<<**

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

### Step 5: 触发后续 SKILL (MANDATORY)

=====================================
>>> MUST INVOKE NEXT SKILLS <<<
=====================================

```bash
# 完成 load-context 后，必须继续调用：
# 1. manage-project - 获取项目详细状态
# 2. plan-tasks - 获取任务详细列表
```

**>>> 调用 llm-memory-cli:manage-project <<<**
**>>> 调用 llm-memory-cli:plan-tasks <<<**

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

>>> 继续调用 manage-project 和 plan-tasks...
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

>>> 继续调用 manage-project 和 plan-tasks...
```

## CLI 命令

- `llm-memory plan list` - 获取计划列表
- `llm-memory plan show <code>` - 获取计划详情
- `llm-memory todo list` - 获取任务列表
- `llm-memory memory search <keyword>` - 搜索记忆

## 后续 SKILL

**>>> 必须调用 (MANDATORY) <<<**
- `manage-project` - 获取/管理项目计划
- `plan-tasks` - 获取/管理任务列表

详见：[加载策略](./references/loading-strategy.md)
