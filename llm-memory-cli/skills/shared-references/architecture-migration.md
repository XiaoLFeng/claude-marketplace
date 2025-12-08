# 架构迁移指南

## 迁移背景

原有的 `workflow-orchestrator` skill 违反了单一职责原则（SRP），包含了 5 个不同的职责：

1. **上下文加载** - 加载 memory/plan/todo
2. **Plan→Todo 转换** - 将计划拆分为待办
3. **Todo↔Plan 同步** - 双向进度同步
4. **决策记录** - 自动记录架构决策
5. **历史查询** - 搜索历史记录

## 新架构

将 `workflow-orchestrator` 拆分为 4 个独立的 skill：

| 原功能 | 新 Skill | 职责 |
|--------|----------|------|
| 上下文加载 | `load-context` | 会话开始时加载上下文 |
| Plan→Todo 转换 + 同步 | `bridge-plan-todo` | Plan 和 Todo 的双向桥接 |
| 决策记录 | `record-decision` | 自动记录决策到 memory |
| 历史查询 | `search-history` | 全局搜索历史记录 |

## 文件结构

### CLI 版本

```
llm-memory-cli/skills/
├── load-context/
│   ├── SKILL.md
│   └── references/
│       ├── commands.md
│       └── examples.md
├── bridge-plan-todo/
│   ├── SKILL.md
│   └── references/
│       ├── commands.md
│       ├── conversion-rules.md
│       ├── sync-logic.md
│       └── examples.md
├── record-decision/
│   ├── SKILL.md
│   └── references/
│       ├── commands.md
│       ├── triggers.md
│       └── examples.md
├── search-history/
│   ├── SKILL.md
│   └── references/
│       ├── commands.md
│       └── examples.md
└── shared-references/
    └── architecture-migration.md  # 本文件
```

## 使用方式变化

### 旧方式（已废弃）

```
调用 workflow-orchestrator skill
↓
一次性处理所有功能
```

### 新方式

```
根据场景调用对应的专用 skill：

- 新对话开始 → load-context
- 计划拆分任务 → bridge-plan-todo
- 技术选型/Bug 排查 → record-decision
- 查询历史 → search-history
```

## 触发条件对照

### load-context

| 旧触发 | 新触发 |
|--------|--------|
| 对话开始时自动加载 | 明确调用 `llm-memory-cli:load-context` |

### bridge-plan-todo

| 旧触发 | 新触发 |
|--------|--------|
| 计划创建/更新时 | 用户说"把计划拆分成待办" |
| Todo 完成时 | Todo 完成后自动同步 Plan 进度 |

### record-decision

| 旧触发 | 新触发 |
|--------|--------|
| 技术选型讨论 | 识别到选型场景自动记录 |
| Bug 排查完成 | 识别到排查场景自动记录 |
| 用户说"记录决策" | 用户明确要求时记录 |

### search-history

| 旧触发 | 新触发 |
|--------|--------|
| 用户说"之前/历史/上次" | 识别到历史查询意图 |

## CLI 命令对应

| Skill | 使用的 CLI 命令 |
|-------|----------------|
| load-context | `llm-memory memory search`, `llm-memory plan list`, `llm-memory todo list` |
| bridge-plan-todo | `llm-memory plan get`, `llm-memory plan update`, `llm-memory todo batch-create`, `llm-memory todo complete`, `llm-memory todo list` |
| record-decision | `llm-memory memory create`, `llm-memory memory search` |
| search-history | `llm-memory memory search`, `llm-memory plan list`, `llm-memory todo list` |

## 迁移步骤

### 1. 更新配置

将原有的 workflow-orchestrator 引用更新为新的 skill：

```markdown
# 旧配置
- 进入新对话或 Plan 模式 → 调用 `llm-memory-cli:workflow-orchestrator`

# 新配置
- 进入新对话 → 调用 `llm-memory-cli:load-context`
- 需要记录知识/决策/经验 → 调用 `llm-memory-cli:record-decision`（自动触发）
- 需要规划复杂任务 → 调用 `llm-memory-cli:bridge-plan-todo`
- 用户问"之前/历史/上次怎么做的" → 调用 `llm-memory-cli:search-history`
```

### 2. 删除旧 skill

```bash
rm -rf llm-memory-cli/skills/workflow-orchestrator
```

### 3. 验证新 skill

确保所有新 skill 能够正常加载和使用。

## 兼容性说明

此次迁移采用**直接废弃**策略，不保留 workflow-orchestrator 作为兼容层。

原因：
1. 新架构更清晰，职责分明
2. 避免维护两套代码
3. 用户可以根据需要选择性使用

## Shell 脚本迁移

如果之前有使用 workflow-orchestrator 的脚本，需要更新：

### 旧脚本

```bash
#!/bin/bash
# 旧的综合脚本
workflow_orchestrator() {
  llm-memory memory search "$1"
  llm-memory plan list
  llm-memory todo list
  # ... 其他逻辑
}
```

### 新脚本

```bash
#!/bin/bash
# 拆分为独立脚本

# load-context.sh
load_context() {
  llm-memory memory search "$1" --scope all
  llm-memory plan list --scope all
  llm-memory todo list --scope all
}

# bridge-plan-todo.sh
bridge_plan_todo() {
  # Plan 转 Todo 逻辑
}

# record-decision.sh
record_decision() {
  # 记录决策逻辑
}

# search-history.sh
search_history() {
  # 搜索历史逻辑
}
```

## 常见问题

### Q: 如何一次性加载所有上下文？

A: 使用 `load-context` skill 或执行以下命令：

```bash
llm-memory memory search "<keyword>" --scope all
llm-memory plan list --scope all
llm-memory todo list --scope all
```

### Q: Plan 进度如何自动更新？

A: `bridge-plan-todo` skill 提供同步脚本，或手动执行：

```bash
# 完成 Todo
llm-memory todo complete todo-xxx-1-1

# 计算并更新进度
# (需要手动计算或使用同步脚本)
llm-memory plan update plan-xxx --progress 20
```

### Q: 什么时候会自动记录决策？

A: `record-decision` skill 会在以下场景识别并提示记录：
- 技术方案选型讨论
- 复杂 Bug 排查完成
- 项目规范制定
- 用户明确说"记录决策"

### Q: 如何搜索之前的工作记录？

A: 使用 `search-history` skill 或执行：

```bash
llm-memory memory search "<keyword>" --scope all
llm-memory plan list | grep -i "<keyword>"
llm-memory todo list | grep -i "<keyword>"
```
