---
name: bridge-plan-todo
description: |
  Plan-Todo 桥接器 (MCP版本) - 连接计划和任务的双向数据流。

  **何时调用此 Skill：**
  - 创建 plan 后需要自动生成 todos
  - todo 状态变更时需要同步 plan 进度
  - 需要查看 plan-todo 关联关系
  - 用户说"从计划生成任务"、"同步进度"

  **不调用此 Skill：**
  - 单独创建 plan（使用 plan-mcp）
  - 单独创建 todo（使用 todo-mcp）
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

**操作流程**：

```javascript
// 1. 解析 plan.content 中的阶段和步骤
const phases = parsePlanContent(plan.content);

// 2. 生成 todos
const todos = [];
phases.forEach((phase, phaseIndex) => {
  phase.steps.forEach((step, stepIndex) => {
    todos.push({
      code: `todo-${plan.code}-${phaseIndex + 1}-${stepIndex + 1}`,
      title: step.title,
      description: step.description || `${plan.title} - Phase ${phaseIndex + 1}`,
      priority: getPriorityByPhase(phaseIndex + 1)
    });
  });
});

// 3. 批量创建
await todo_batch_create({
  items: todos,
  scope: "personal"
});

// 优先级分配函数
function getPriorityByPhase(phaseNumber) {
  if (phaseNumber === 1) return 4;  // 紧急
  if (phaseNumber === 2) return 3;  // 高
  return 2;  // 中（默认）
}
```

**Plan 内容解析规则**：

```markdown
# Plan 内容格式（推荐）

## 阶段 1: 标题
- 步骤 1 描述
- 步骤 2 描述

## 阶段 2: 标题
- 步骤 1 描述
- 步骤 2 描述
```

**使用的 MCP 工具**：

```javascript
// 批量创建 todos
todo_batch_create({
  items: [
    {
      code: "todo-xxx-1-1",
      title: "任务标题",
      description: "任务描述",
      priority: 4
    }
    // ... 更多 todos
  ],
  scope: "personal"
})
```

---

### 功能 B：Todo → Plan 进度同步

Todo 状态变更时，自动计算并更新关联 plan 的进度。

**操作流程**：

```javascript
// 1. 从 todo code 解析关联的 plan code
const planCode = extractPlanCode(todoCode);
// 例如：todo-auth-refactor-1-1 → plan-auth-refactor

// 2. 获取所有关联的 todos
const allTodos = await getRelatedTodos(planCode);
// 匹配所有以 todo-<planCode>- 开头的 todos

// 3. 计算完成比例
const completedCount = allTodos.filter(t => t.status === 2).length;
const totalCount = allTodos.length;
const progress = Math.round((completedCount / totalCount) * 100);

// 4. 更新 plan 进度
await plan_update({
  code: planCode,
  progress: progress
});
```

**使用的 MCP 工具**：

```javascript
// 列出待办（筛选关联 todos）
todo_list({
  scope: "all"
})

// 更新计划进度
plan_update({
  code: "plan-xxx",
  progress: 50  // 0-100
})
```

---

## 📊 完整示例

### 示例：用户认证系统重构

**1. 创建 Plan**：

```javascript
await plan_create({
  code: "plan-auth-refactor",
  title: "用户认证系统重构",
  description: "采用 JWT 机制，支持 refresh token",
  content: `
# 用户认证系统重构实施计划

## 阶段 1: 数据库设计
- 设计 users 表结构
- 设计 refresh_tokens 表结构

## 阶段 2: JWT 核心实现
- 实现 JWT 生成逻辑
- 实现 JWT 验证逻辑
- 实现 refresh token 机制

## 阶段 3: API 端点开发
- POST /api/auth/register
- POST /api/auth/login
- POST /api/auth/refresh
  `
});
```

**2. 自动生成 Todos**（调用 bridge-plan-todo）：

```javascript
// bridge-plan-todo 解析后生成：
const todos = [
  // Phase 1 - 优先级 4 (紧急)
  { code: "todo-auth-refactor-1-1", title: "设计 users 表结构", priority: 4 },
  { code: "todo-auth-refactor-1-2", title: "设计 refresh_tokens 表结构", priority: 4 },

  // Phase 2 - 优先级 3 (高)
  { code: "todo-auth-refactor-2-1", title: "实现 JWT 生成逻辑", priority: 3 },
  { code: "todo-auth-refactor-2-2", title: "实现 JWT 验证逻辑", priority: 3 },
  { code: "todo-auth-refactor-2-3", title: "实现 refresh token 机制", priority: 3 },

  // Phase 3 - 优先级 2 (中)
  { code: "todo-auth-refactor-3-1", title: "POST /api/auth/register", priority: 2 },
  { code: "todo-auth-refactor-3-2", title: "POST /api/auth/login", priority: 2 },
  { code: "todo-auth-refactor-3-3", title: "POST /api/auth/refresh", priority: 2 }
];

await todo_batch_create({ items: todos });
```

**3. 完成任务并同步进度**：

```javascript
// 完成 Phase 1 的 2 个 todos
await todo_batch_complete({
  codes: ["todo-auth-refactor-1-1", "todo-auth-refactor-1-2"]
});

// bridge-plan-todo 自动计算进度：
// completedTodos = 2
// totalTodos = 8
// progress = (2/8) * 100 = 25%

await plan_update({
  code: "plan-auth-refactor",
  progress: 25
});
```

---

## 🎯 使用场景

### 场景 1：创建计划后生成任务

```
用户：创建用户认证重构计划
↓
plan_create 完成
↓
bridge-plan-todo 自动触发
↓
解析 plan.content
↓
todo_batch_create 生成关联 todos
```

### 场景 2：完成任务后同步进度

```
用户：完成 todo-auth-refactor-1-1
↓
todo_batch_complete 完成
↓
bridge-plan-todo 自动触发
↓
计算完成比例
↓
plan_update 更新进度
```

### 场景 3：手动触发转换

```
用户："从 plan-xxx 生成任务"
↓
bridge-plan-todo 识别意图
↓
解析指定 plan
↓
生成关联 todos
```

---

## 📚 MCP 工具清单

本 Skill 使用以下 MCP 工具：

**Plan 相关**：
- `plan_get` - 获取计划详情（来自 plan-mcp）
- `plan_update` - 更新计划进度（来自 plan-mcp）

**Todo 相关**：
- `todo_list` - 列出待办（来自 todo-mcp）
- `todo_batch_create` - 批量创建待办（来自 todo-mcp）
- `todo_batch_complete` - 批量完成待办（来自 todo-mcp）

详见：[完整工具参考](./references/tools.md)

---

## 🔗 参考文档

- [完整工具参考](./references/tools.md) - MCP 工具详细说明
- [转换规则详解](./references/conversion-rules.md) - Plan→Todo 转换规则
- [同步逻辑详解](./references/sync-logic.md) - Todo→Plan 同步逻辑
- [使用示例](./references/examples.md) - 真实场景案例
- [Plan Skill](../plan-mcp/SKILL.md) - 计划管理
- [Todo Skill](../todo-mcp/SKILL.md) - 待办管理
- [Code 格式](../shared-references/code-format.md) - 格式规则详解
- [架构迁移](../shared-references/architecture-migration.md) - 从 workflow-orchestrator 迁移
