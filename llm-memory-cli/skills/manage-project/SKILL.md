---
name: manage-project
description: "Project manager - Create and manage complex multi-step project plans. Use when: complex tasks (>3 steps), long timespan, need overall progress tracking (0-100%). Not for: simple tasks (use task-*), batch todo creation (use plan-tasks)."
---

# Manage Project

创建和管理复杂的多步骤项目计划。

## 触发条件

- 任务复杂（>3 步骤）
- 时间跨度长（>3 天）
- 需要跟踪整体进度（0-100%）
- 用户说"帮我规划"、"制定方案"、"创建计划"

## 不触发条件

- 单一简单任务 → 使用 task-* 系列
- 批量创建 todos → 使用 plan-tasks
- 同步进度 → 使用 sync-progress

## 操作流程

### 创建计划

```bash
llm-memory plan add plan-user-auth "用户认证系统重构" \
  --description "重构现有认证系统，支持 OAuth 和 JWT"
```

### 查看计划

```bash
# 列出所有计划
llm-memory plan list

# 获取计划详情
llm-memory plan show plan-user-auth
```

### 更新进度

```bash
# 进度自动转换状态: 0=待开始, 1-99=进行中, 100=已完成
llm-memory plan update plan-user-auth --progress 45
```

### 更新内容

```bash
llm-memory plan update plan-user-auth --title "新标题"
llm-memory plan update plan-user-auth --description "新描述"
```

## Code 命名规范

```
格式: plan-<描述>
正则: ^[a-z][a-z\-]*[a-z]$

示例:
  plan-user-auth        ✅
  plan-api-refactor     ✅
  Plan_001              ❌
```

## 进度与状态

| 进度 | 状态 | 说明 |
|-----|------|------|
| 0 | pending | 待开始 |
| 1-99 | in_progress | 进行中 |
| 100 | completed | 已完成 |

## 使用场景

### 场景 1: 新功能开发

```markdown
用户: "帮我规划用户认证系统的开发"

1. 调用 manage-project 创建 Plan
2. 调用 plan-tasks 创建关联的 Todos
3. 调用 workflow-orchestrator 分配 Agent
```

### 场景 2: 更新进度

```markdown
完成一批任务后:

1. 调用 sync-progress 自动计算
2. 或手动: llm-memory plan update plan-xxx --progress 60
```

## 输出示例

### 创建成功

```
✅ 计划已创建

📋 [plan-user-auth] 用户认证系统重构
   进度: 0% | 状态: 待开始
```

### 进度更新

```
✅ 进度已更新

📋 [plan-user-auth] 用户认证系统重构
   进度: 45% | 状态: 进行中
   ████████░░░░░░░░░░░░
```

## CLI 命令

- `llm-memory plan add <code> <title>` - 创建计划
- `llm-memory plan list` - 列出计划
- `llm-memory plan show <code>` - 获取详情
- `llm-memory plan update <code>` - 更新计划

详见：
- [命令详解](./references/commands.md)
- [使用示例](./references/examples.md)
