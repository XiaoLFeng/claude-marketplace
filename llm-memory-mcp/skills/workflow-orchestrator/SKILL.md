---
name: workflow-orchestrator
description: |
  LLM-Memory 工作流自动协调器 - 主动集成 memory/plan/todo 工具。在关键时刻自动使用（无需用户请求）：
  (1) 进入 Plan 模式时 - 从 memory/plan/todo 加载上下文
  (2) 创建计划后 - 从计划阶段自动生成 todos
  (3) 完成任务时 - 更新 todo 状态和 plan 进度
  (4) 做架构决策时 - 记录到 memory
  (5) 用户查询历史时 - 搜索 memory/plan/todo
  协调 memory-mcp、plan-mcp、todo-mcp skills。
---

# Workflow Orchestrator - 工作流自动协调器

自动化 llm-memory 工作流集成 - 加载上下文、跟踪进度、记录决策。

## 自动触发时机

### 1. 进入 Plan 模式时

加载现有上下文：
```javascript
const memories = await memory_search({ keyword: extractKeywords(userRequest)[0] });
const plans = await plan_list({ scope: "all" });
const todos = await todo_list({ scope: "all" });
```

规划时使用此上下文。向用户提及相关的 memories/plans/todos。

### 2. 创建 Plan 后

从 plan 内容提取 todos 并批量创建：
```javascript
// 解析 plan 内容中的 phases/steps
const phases = planContent.match(/## Phase \d+:(.*)/g) || [];

// 创建 todos
await todo_batch_create({
  items: phases.map((phase, i) => ({
    code: `todo-${planCode}-${i + 1}`,
    title: phase.replace(/## Phase \d+:\s*/, ''),
    priority: i === 0 ? 4 : (i === 1 ? 3 : 2)
  }))
});
```

### 3. 任务执行过程中

工作时更新状态：
```javascript
// 开始任务
await todo_batch_start({ codes: [currentTodoCode] });

// 完成任务
await todo_batch_complete({ codes: [currentTodoCode] });

// 更新 plan 进度
const progress = Math.round((completedCount / totalCount) * 100);
await plan_update({ code: planCode, progress });
```

### 4. 做架构决策时

自动记录到 memory：
```javascript
await memory_create({
  code: `mem-${generateCode(decision)}`,
  title: decision.title,
  content: decision.content,
  category: "架构决策",
  tags: relevantTags
});
```

触发时机：
- 选择技术方案
- 解决复杂 bug（>30分钟排查）
- 创建可复用代码模式
- 定义项目标准

### 5. 用户查询时

搜索历史记录：
```javascript
const results = {
  memories: await memory_search({ keyword, scope: "all" }),
  plans: await plan_list({ scope: "all" }),
  todos: await todo_list({ scope: "all" })
};
```

触发时机：用户问 "之前..."、"历史..."、"怎么做的"、"记录..."

## 与其他 Skills 的集成

优先使用 MCP 工具（如果可用）：
- `memory_*` - 参见 [memory-mcp](../memory-mcp/SKILL.md)
- `plan_*` - 参见 [plan-mcp](../plan-mcp/SKILL.md)
- `todo_*` - 参见 [todo-mcp](../todo-mcp/SKILL.md)

CLI 版本使用 bash 命令：
- `./main memory *` - 参见 [memory-cli](../memory-cli/SKILL.md)
- `./main plan *` - 参见 [plan-cli](../plan-cli/SKILL.md)
- `./main todo *` - 参见 [todo-cli](../todo-cli/SKILL.md)

## 关键模式

**Code 生成规则：**
- Plan: `plan-<项目>-<功能>`
- Todo: `todo-<plan-code>-<步骤编号>`
- Memory: `mem-<主题>-<日期>`

**进度计算公式：**
```
progress = (已完成todos数 / 总todos数) * 100
```

**优先级分配：**
- Phase 1: Priority 4（紧急）
- Phase 2: Priority 3（高）
- 其他: Priority 2（中）

