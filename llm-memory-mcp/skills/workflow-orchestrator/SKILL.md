---
name: workflow-orchestrator
description: |
  LLM-Memory 工作流协调器 - 协调 memory/plan/todo 三个 Skill 的协同工作。

  **主动触发（PROACTIVELY）- 无需用户请求：**
  - 新对话/Plan 模式 → 自动加载全局上下文
  - 创建计划后 → 自动从 plan 生成 todos
  - 完成任务时 → 同步更新 todo 状态和 plan 进度
  - 架构决策时 → 自动记录到 memory

  **被动触发 - 用户查询时：**
  - 用户问"之前..."、"历史..."、"怎么做的" → 搜索全部记录

  **此 Skill 是协调层，不直接处理单个工具操作。**
  单独操作请使用：memory-mcp、plan-mcp、todo-mcp
---

# Workflow Orchestrator - 工作流协调器

协调 memory/plan/todo 三个 Skill 的协同工作，实现全局上下文管理和自动化工作流。

## 核心职责

```
┌─────────────────────────────────────────────┐
│        Workflow Orchestrator (协调层)        │
│  ┌───────────────────────────────────────┐  │
│  │  上下文加载 │ 流程控制 │ 进度同步    │  │
│  └───────────────────────────────────────┘  │
└──────────────────┬──────────────────────────┘
                   │ 协调调用
         ┌─────────┼─────────┐
         ▼         ▼         ▼
     ┌───────┐ ┌───────┐ ┌───────┐
     │Memory │ │ Plan  │ │ Todo  │
     │ Skill │ │ Skill │ │ Skill │
     │ 知识  │ │ 规划  │ │ 执行  │
     └───────┘ └───────┘ └───────┘
```

---

## 🔄 协调工作流

### 工作流 1：新对话/Plan 模式启动

**触发条件**：进入新对话或 Plan 模式时

**协调动作**：并行加载三类上下文

```
用户请求 → 提取关键词
              ↓
    ┌─────────┼─────────┐
    ↓         ↓         ↓
memory_search plan_list todo_list
    ↓         ↓         ↓
    └─────────┼─────────┘
              ↓
      汇总上下文 → 展示给用户
```

**执行示例**：
```javascript
// 并行加载
const [memories, plans, todos] = await Promise.all([
  memory_search({ keyword: extractKeywords(userRequest) }),
  plan_list({ scope: "all" }),
  todo_list({ scope: "all" })
]);

// 汇总展示
summarizeContext({ memories, plans, todos });
```

---

### 工作流 2：Plan → Todo 自动转换

**触发条件**：使用 plan_create 创建计划后

**协调动作**：解析 plan 内容，批量生成 todos

```
plan_create 完成
        ↓
解析 content 中的 Phase/Step
        ↓
todo_batch_create 批量创建
        ↓
关联 plan code 和 todo codes
```

**Code 关联规则**：
```
Plan Code:  plan-<项目>-<功能>
Todo Code:  todo-<plan-code>-<步骤编号>

示例：
plan-auth-refactor
  ├─ todo-auth-refactor-1  (Phase 1, priority: 4)
  ├─ todo-auth-refactor-2  (Phase 2, priority: 3)
  └─ todo-auth-refactor-3  (Phase 3, priority: 2)
```

**优先级分配**：
| 阶段 | 优先级 | 说明 |
|------|--------|------|
| Phase 1 | 4 (紧急) | 首要任务 |
| Phase 2 | 3 (高) | 次要任务 |
| Phase 3+ | 2 (中) | 后续任务 |

---

### 工作流 3：Todo ↔ Plan 进度同步

**触发条件**：todo 状态变更时

**协调动作**：同步更新关联 plan 的进度

```
todo_batch_complete
        ↓
计算完成比例
        ↓
plan_update 更新进度
        ↓
检查是否全部完成
```

**进度计算公式**：
```javascript
const completedTodos = todos.filter(t => t.status === 2).length;
const totalTodos = todos.length;
const progress = Math.round((completedTodos / totalTodos) * 100);

// 更新 plan
await plan_update({ code: planCode, progress });
```

**状态映射**：
```
progress = 0      → plan.status = pending
progress = 1-99   → plan.status = in_progress
progress = 100    → plan.status = completed
```

---

### 工作流 4：决策 → Memory 自动记录

**触发条件**：做出架构决策时

**自动记录场景**：
- 技术方案选型（框架/库/工具）
- 复杂 Bug 排查过程（>30分钟）
- 可复用代码模式
- 项目规范和约定

**协调动作**：
```
识别决策场景
        ↓
提取决策信息
        ↓
memory_create 自动记录
```

**Memory Code 规则**：
```
mem-<主题>-<日期>
示例：mem-jwt-auth-20241208
```

---

### 工作流 5：历史查询

**触发条件**：用户查询历史信息

**触发关键词**：
- "之前..."、"历史..."
- "怎么做的"、"记录..."
- "上次..."、"以前..."

**协调动作**：全量搜索三类数据

```javascript
const results = await Promise.all([
  memory_search({ keyword, scope: "all" }),
  plan_list({ scope: "all" }),
  todo_list({ scope: "all" })
]);

// 按相关性排序展示
displayResults(results);
```

---

## 🔗 Skill 调用指南

### 何时调用子 Skill

| 场景 | 调用 Skill | 说明 |
|------|-----------|------|
| 单独记录知识 | memory-mcp | 不涉及计划/任务 |
| 单独创建计划 | plan-mcp | 不需要自动生成 todos |
| 单独管理任务 | todo-mcp | 独立任务，不关联 plan |
| 协调多个工具 | workflow-orchestrator | 需要跨工具协作 |

### 子 Skill 参考

- [memory-mcp](../memory-mcp/SKILL.md) - 知识库管理
- [plan-mcp](../plan-mcp/SKILL.md) - 计划管理
- [todo-mcp](../todo-mcp/SKILL.md) - 待办管理

---

## 📋 Code 命名规范汇总

| 类型 | 格式 | 示例 |
|------|------|------|
| Plan | `plan-<项目>-<功能>` | `plan-auth-refactor` |
| Todo | `todo-<plan-code>-<编号>` | `todo-auth-refactor-1` |
| Memory | `mem-<主题>-<日期>` | `mem-jwt-decision-20241208` |

**通用规则**：
- 全小写 + 连字符
- 字母开头和结尾
- 最少 3 个字符
- 同类型活跃状态唯一

---

## ⚠️ 协调边界

**Workflow Orchestrator 负责**：
- ✅ 跨工具流程编排
- ✅ 数据流转和同步
- ✅ 上下文加载和汇总
- ✅ 自动触发判断

**Workflow Orchestrator 不负责**：
- ❌ 单个工具的具体操作（交给子 Skill）
- ❌ 工具参数的详细说明（参考子 Skill 文档）
- ❌ 错误处理的具体逻辑（参考 troubleshooting）

---
