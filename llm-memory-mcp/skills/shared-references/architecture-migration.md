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

### MCP 版本

```
llm-memory-mcp/skills/
├── load-context/
│   ├── SKILL.md
│   └── references/
│       ├── tools.md
│       └── examples.md
├── bridge-plan-todo/
│   ├── SKILL.md
│   └── references/
│       ├── tools.md
│       ├── conversion-rules.md
│       ├── sync-logic.md
│       └── examples.md
├── record-decision/
│   ├── SKILL.md
│   └── references/
│       ├── tools.md
│       ├── triggers.md
│       └── examples.md
├── search-history/
│   ├── SKILL.md
│   └── references/
│       ├── tools.md
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
| 对话开始时自动加载 | 明确调用 `llm-memory-mcp:load-context` |

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

## MCP 工具对应

| Skill | 使用的 MCP 工具 |
|-------|----------------|
| load-context | memory_search, plan_list, todo_list |
| bridge-plan-todo | plan_get, plan_update, todo_batch_create, todo_batch_complete, todo_batch_start, todo_list |
| record-decision | memory_create, memory_search |
| search-history | memory_search, plan_list, todo_list |

## 迁移步骤

### 1. 更新 CLAUDE.md

将原有的 workflow-orchestrator 引用更新为新的 skill：

```markdown
# 旧配置
- 进入新对话或 Plan 模式 → 调用 `llm-memory-mcp:workflow-orchestrator`

# 新配置
- 进入新对话 → 调用 `llm-memory-mcp:load-context`
- 需要记录知识/决策/经验 → 调用 `llm-memory-mcp:record-decision`（自动触发）
- 需要规划复杂任务 → 调用 `llm-memory-mcp:bridge-plan-todo`
- 用户问"之前/历史/上次怎么做的" → 调用 `llm-memory-mcp:search-history`
```

### 2. 删除旧 skill

```bash
rm -rf llm-memory-mcp/skills/workflow-orchestrator
```

### 3. 验证新 skill

确保所有新 skill 能够正常加载和使用。

## 兼容性说明

此次迁移采用**直接废弃**策略，不保留 workflow-orchestrator 作为兼容层。

原因：
1. 新架构更清晰，职责分明
2. 避免维护两套代码
3. 用户可以根据需要选择性使用

## 常见问题

### Q: 如何一次性加载所有上下文？

A: 使用 `load-context` skill，它会并行加载 memory、plan、todo。

### Q: Plan 进度如何自动更新？

A: `bridge-plan-todo` skill 会在 Todo 完成时自动计算并更新 Plan 进度。

### Q: 什么时候会自动记录决策？

A: `record-decision` skill 会在以下场景自动识别并提示记录：
- 技术方案选型讨论
- 复杂 Bug 排查完成
- 项目规范制定
- 用户明确说"记录决策"

### Q: 如何搜索之前的工作记录？

A: 使用 `search-history` skill，支持在 memory、plan、todo 中进行关键词搜索。
