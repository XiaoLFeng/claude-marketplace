---
name: bridge-plan-todo
description: |
  Plan-Todo 桥接器 (CLI版本) - 连接计划和任务的双向数据流。

  **何时调用此 Skill：**
  - 创建 plan 后需要自动生成 todos
  - todo 状态变更时需要同步 plan 进度
  - 需要查看 plan-todo 关联关系
  - 用户说"从计划生成任务"、"同步进度"

  **不调用此 Skill：**
  - 单独创建 plan（使用 plan-cli）
  - 单独创建 todo（使用 todo-cli）
  - 不涉及 plan-todo 关联的操作
---

# Bridge Plan Todo Skill

连接计划和任务的双向数据流，实现 Plan→Todo 转换和 Todo→Plan 进度同步。

## ⚡ 快速参考

### Code 关联规则

```
Plan Code:  plan-<项目>-<功能>
Todo Code:  todo-<plan-code>-<phase>-<step>

示例：
plan-auth-refactor
  ├─ todo-auth-refactor-1-1  (Phase 1, Step 1)
  ├─ todo-auth-refactor-1-2  (Phase 1, Step 2)
  ├─ todo-auth-refactor-2-1  (Phase 2, Step 1)
  └─ todo-auth-refactor-3-1  (Phase 3, Step 1)
```

### 优先级分配规则

```
Phase 1  →  优先级 4 (🔴 紧急)
Phase 2  →  优先级 3 (🟠 高)
Phase 3+ →  优先级 2 (🟡 中)
```

### 进度计算规则

```
progress = (completedTodos / totalTodos) * 100

示例：
5 个 todo，完成 2 个
→ progress = (2/5) * 100 = 40%
```

### 状态映射

```
progress = 0      →  plan.status = pending
progress = 1-99   →  plan.status = in_progress
progress = 100    →  plan.status = completed
```

---

## 🔧 核心操作

### 功能 A：Plan → Todo 转换

创建 plan 后，解析内容并批量生成关联的 todos。

**CLI 命令**：

```bash
# 获取计划详情
./main plan get --code "plan-xxx"

# 批量创建 todos（使用 JSON 格式）
./main todo batch-create --json '[
  {"code": "todo-xxx-1-1", "title": "任务1", "priority": 4},
  {"code": "todo-xxx-1-2", "title": "任务2", "priority": 4}
]'
```

**转换脚本示例**：

```bash
#!/bin/bash
# plan-to-todos.sh

PLAN_CODE=$1

# 获取计划内容
PLAN_CONTENT=$(./main plan get --code "$PLAN_CODE" --format json)

# 解析阶段和步骤（示例：使用 jq）
# 实际实现需要解析 plan.content 的 Markdown 格式

# 生成 todos JSON
TODOS='[
  {"code": "todo-'$PLAN_CODE'-1-1", "title": "Phase 1 Step 1", "priority": 4},
  {"code": "todo-'$PLAN_CODE'-2-1", "title": "Phase 2 Step 1", "priority": 3}
]'

# 批量创建
./main todo batch-create --json "$TODOS"
```

---

### 功能 B：Todo → Plan 进度同步

Todo 状态变更时，自动计算并更新关联 plan 的进度。

**CLI 命令**：

```bash
# 列出所有关联的 todos
./main todo list | grep "todo-plan-xxx"

# 统计完成数量
TOTAL=$(./main todo list | grep "todo-plan-xxx" | wc -l)
COMPLETED=$(./main todo list | grep "todo-plan-xxx" | grep "completed" | wc -l)

# 计算进度
PROGRESS=$((COMPLETED * 100 / TOTAL))

# 更新计划进度
./main plan update --code "plan-xxx" --progress $PROGRESS
```

**同步脚本示例**：

```bash
#!/bin/bash
# sync-plan-progress.sh

PLAN_CODE=$1

# 获取关联的 todos
TODOS=$(./main todo list --format json | jq "[.[] | select(.code | startswith(\"todo-$PLAN_CODE\"))]")

# 计算完成比例
TOTAL=$(echo "$TODOS" | jq 'length')
COMPLETED=$(echo "$TODOS" | jq '[.[] | select(.status == 2)] | length')

if [ "$TOTAL" -gt 0 ]; then
  PROGRESS=$((COMPLETED * 100 / TOTAL))
else
  PROGRESS=0
fi

# 更新计划进度
./main plan update --code "$PLAN_CODE" --progress "$PROGRESS"

echo "Plan $PLAN_CODE progress updated to $PROGRESS%"
```

---

## 📊 完整示例

### 示例：用户认证系统重构

**1. 创建 Plan**：

```bash
./main plan create \
  --code "plan-auth-refactor" \
  --title "用户认证系统重构" \
  --description "采用 JWT 机制，支持 refresh token" \
  --content "
# 用户认证系统重构实施计划

## 阶段 1: 数据库设计
- 设计 users 表结构
- 设计 refresh_tokens 表结构

## 阶段 2: JWT 核心实现
- 实现 JWT 生成逻辑
- 实现 JWT 验证逻辑

## 阶段 3: API 端点开发
- POST /api/auth/register
- POST /api/auth/login
"
```

**2. 生成关联 Todos**：

```bash
./main todo batch-create --json '[
  {"code": "todo-auth-refactor-1-1", "title": "设计 users 表结构", "priority": 4},
  {"code": "todo-auth-refactor-1-2", "title": "设计 refresh_tokens 表结构", "priority": 4},
  {"code": "todo-auth-refactor-2-1", "title": "实现 JWT 生成逻辑", "priority": 3},
  {"code": "todo-auth-refactor-2-2", "title": "实现 JWT 验证逻辑", "priority": 3},
  {"code": "todo-auth-refactor-3-1", "title": "POST /api/auth/register", "priority": 2},
  {"code": "todo-auth-refactor-3-2", "title": "POST /api/auth/login", "priority": 2}
]'
```

**3. 完成任务并同步进度**：

```bash
# 完成 Phase 1 的任务
./main todo complete --code "todo-auth-refactor-1-1"
./main todo complete --code "todo-auth-refactor-1-2"

# 同步进度
./sync-plan-progress.sh "plan-auth-refactor"
# 输出：Plan plan-auth-refactor progress updated to 33%
```

---

## 🎯 使用场景

### 场景 1：创建计划后生成任务

```bash
# 创建计划
./main plan create --code "plan-xxx" ...

# 运行转换脚本
./plan-to-todos.sh "plan-xxx"
```

### 场景 2：完成任务后同步进度

```bash
# 完成任务
./main todo complete --code "todo-xxx-1-1"

# 同步进度
./sync-plan-progress.sh "plan-xxx"
```

---

## 📚 CLI 命令清单

本 Skill 使用以下 CLI 命令：

**Plan 相关**：
- `./main plan get` - 获取计划详情（来自 plan-cli）
- `./main plan update` - 更新计划进度（来自 plan-cli）

**Todo 相关**：
- `./main todo list` - 列出待办（来自 todo-cli）
- `./main todo batch-create` - 批量创建待办（来自 todo-cli）
- `./main todo complete` - 完成待办（来自 todo-cli）

详见：[完整命令参考](./references/commands.md)

---

## 🔗 参考文档

- [完整命令参考](./references/commands.md) - CLI 命令详细说明
- [转换规则详解](./references/conversion-rules.md) - Plan→Todo 转换规则
- [同步逻辑详解](./references/sync-logic.md) - Todo→Plan 同步逻辑
- [使用示例](./references/examples.md) - 真实场景案例
- [Plan Skill](../plan-cli/SKILL.md) - 计划管理
- [Todo Skill](../todo-cli/SKILL.md) - 待办管理
- [Code 格式](../shared-references/code-format.md) - 格式规则详解
- [架构迁移](../shared-references/architecture-migration.md) - 从 workflow-orchestrator 迁移
