---
name: workflow-orchestrator
description: |
  LLM-Memory 工作流自动协调器 - 自动管理 Memory/Plan/Todo 的生命周期和进度跟踪。

  **Claude Code 的核心集成 Skill - 你应该始终使用此 Skill！**

  **自动调用时机：**
  1. 进入 Plan 模式时 - 读取相关 memory/plan/todo 作为上下文
  2. 创建 Plan 后 - 自动创建对应的 Todos
  3. 完成关键步骤时 - 自动更新进度，记录重要决策到 memory
  4. 用户查询历史时 - 搜索 memory/plan/todo

  **核心职责：**
  - 自动读取和更新 Plan/Todo 进度
  - 在关键节点自动记录 Memory
  - 协调 memory-cli/plan-cli/todo-cli（或 MCP 版本）的调用
  - 提供统一的工作流视图
---

# Workflow Orchestrator - 工作流自动协调器

> 让 Claude Code 自动管理你的工作流 - 无需手动调用，智能集成 Memory/Plan/Todo

## 🎯 核心理念

这个 skill 是你（Claude Code）的"自动驾驶系统"，它告诉你：
- **何时**应该调用 memory/plan/todo skills
- **如何**自动跟踪进度
- **怎样**与用户的工作流无缝集成

## 🔄 自动调用流程

### 1. Plan 模式启动时 ⭐ 必须执行

```javascript
// 用户进入 Plan 模式（EnterPlanMode）
// 你应该立即执行以下操作：

// 步骤 1：读取相关 Memory
const memories = await memory_search({
  keyword: "<从用户请求中提取关键词>",
  scope: "all"
});

// 步骤 2：读取现有 Plan
const plans = await plan_list({ scope: "all" });

// 步骤 3：读取相关 Todos
const todos = await todo_list({ scope: "all" });

// 步骤 4：分析上下文
// - 是否有现有计划？
// - 之前是否有相关记忆？
// - 是否有未完成的任务？

// 步骤 5：在规划时考虑这些上下文
```

**为什么重要：**
- 避免重复规划
- 复用历史决策
- 了解当前进度

### 2. Plan 创建后 ⭐ 必须执行

```javascript
// 你创建了一个 Plan
const planResult = await plan_create({
  code: "plan-xxx",
  title: "...",
  description: "...",
  content: "..."
});

// 立即拆解为 Todos
const todos = extractTodosFromPlan(planResult.content);

// 批量创建 Todos
await todo_batch_create({
  items: todos.map((todo, index) => ({
    code: `todo-${planResult.code}-${index + 1}`,
    title: todo.title,
    description: todo.description,
    priority: todo.priority || 2
  }))
});

// 告知用户
console.log(`✅ Plan 创建成功，已自动创建 ${todos.length} 个关联任务`);
```

**为什么重要：**
- Plan 和 Todo 自动关联
- 用户无需手动拆解
- 保持一致性

### 3. 执行任务过程中 ⭐ 自动跟踪

```javascript
// 当你完成一个步骤时

// 步骤 1：自动标记 Todo 为完成
await todo_batch_complete({
  codes: ["todo-xxx"]
});

// 步骤 2：自动更新 Plan 进度
const completedTodos = await getCompletedTodosForPlan("plan-xxx");
const totalTodos = await getAllTodosForPlan("plan-xxx");
const progress = Math.round((completedTodos.length / totalTodos.length) * 100);

await plan_update({
  code: "plan-xxx",
  progress: progress
});

// 步骤 3：关键决策时自动记录 Memory
if (isImportantDecision()) {
  await memory_create({
    code: "mem-xxx",
    title: "关键决策记录",
    content: "<决策内容>",
    category: "架构决策"
  });
}

// 告知用户当前进度
console.log(`✅ 任务已完成，整体进度: ${progress}%`);
```

**为什么重要：**
- 进度自动同步
- 用户无需手动更新
- 关键知识不丢失

### 4. 用户查询时 ⭐ 主动搜索

```javascript
// 用户问："之前关于 JWT 的决策是什么？"
// 你应该主动执行：

const memories = await memory_search({
  keyword: "JWT",
  scope: "all"
});

if (memories.length > 0) {
  // 找到了！返回相关记忆
  return memories.map(m => m.content).join("\n\n---\n\n");
} else {
  // 没找到，搜索 Plan 和 Todo
  const plans = await plan_list({ scope: "all" });
  const todos = await todo_list({ scope: "all" });

  // 在 title/description 中搜索
  // ...
}
```

**为什么重要：**
- 利用历史记录回答问题
- 避免重复工作
- 提供上下文感知

### 5. 关键步骤完成时 ⭐ 主动记录

```javascript
// 当你完成一个重要实现时（如实现了 JWT 机制）

// 自动判断是否需要记录
const shouldRecord = (
  isArchitectureDecision() ||  // 架构决策
  isBugFixWithComplexity() ||  // 复杂 Bug 修复
  isReusableCodeSnippet() ||   // 可复用代码
  isProjectStandard()           // 项目规范
);

if (shouldRecord) {
  await memory_create({
    code: generateMemoryCode(),
    title: "...",
    content: "...",
    category: "...",
    tags: [...]
  });

  console.log("📝 已自动记录关键决策到 Memory");
}
```

**为什么重要：**
- 知识自动积累
- 不依赖用户记忆
- 构建项目知识库

## 🔗 与其他 Skills 的协调

### Memory Skills (memory-cli / memory-mcp)

**你的调用时机：**
- ✅ Plan 模式启动 → `memory_search()` 读取相关记忆
- ✅ 用户查询 → `memory_search()` 搜索历史
- ✅ 关键决策 → `memory_create()` 记录决策
- ✅ 实现完成 → `memory_create()` 记录方案

**调用方式：**
```javascript
// 使用 memory-mcp skill（如果可用）
// 或使用 memory-cli skill（CLI 方式）
```

### Plan Skills (plan-cli / plan-mcp)

**你的调用时机：**
- ✅ Plan 模式启动 → `plan_list()` 读取现有计划
- ✅ 规划完成 → `plan_create()` 创建新计划
- ✅ 任务完成 → `plan_update()` 更新进度
- ✅ 全部完成 → `plan_update({ progress: 100 })` 标记完成

**进度自动计算：**
```javascript
// 根据关联的 Todos 自动计算进度
const progress = (completedTodos / totalTodos) * 100;
await plan_update({ code: planCode, progress });
```

### Todo Skills (todo-cli / todo-mcp)

**你的调用时机：**
- ✅ Plan 模式启动 → `todo_list()` 读取现有任务
- ✅ Plan 创建后 → `todo_batch_create()` 批量创建任务
- ✅ 开始任务 → `todo_batch_start()` 标记开始
- ✅ 完成任务 → `todo_batch_complete()` 标记完成

**批量操作优先：**
```javascript
// 优先使用批量操作
await todo_batch_complete({ codes: completedCodes });
```

## 📋 完整的工作流示例

### 示例：用户说"帮我优化 skills"

#### Phase 1: Plan 模式启动（自动执行）

```javascript
// 1. 搜索相关 Memory
const memories = await memory_search({
  keyword: "skills 优化",
  scope: "all"
});

// 2. 读取现有 Plan
const plans = await plan_list({ scope: "all" });
const relatedPlan = plans.find(p => p.title.includes("skills"));

// 3. 读取相关 Todos
const todos = await todo_list({ scope: "all" });
const relatedTodos = todos.filter(t => t.title.includes("skills"));

// 4. 综合分析
console.log(`
📊 上下文分析：
- 找到 ${memories.length} 条相关记忆
- 找到 ${relatedPlan ? 1 : 0} 个相关计划
- 找到 ${relatedTodos.length} 个相关任务
`);

// 5. 在规划时考虑这些上下文
// 例如：如果已有计划，询问是否继续或创建新计划
```

#### Phase 2: 规划完成（自动执行）

```javascript
// 创建 Plan
const plan = await plan_create({
  code: "plan-skills-optimization",
  title: "Skills 优化重构",
  description: "将 llm-workflow 拆分为 6 个专项 skills",
  content: `
# Skills 优化重构计划

## Phase 1: 创建共享文件
- 创建 shared-references/ 目录
- 创建 4 个共享文件

## Phase 2: 创建 Todo Skills
...
  `
});

// 自动拆解为 Todos
await todo_batch_create({
  items: [
    {
      code: "todo-skills-shared-refs",
      title: "创建共享文件（shared-references/）",
      priority: 4
    },
    {
      code: "todo-skills-todo",
      title: "创建 Todo Skills（CLI + MCP）",
      priority: 3
    },
    // ... 更多任务
  ]
});

console.log("✅ Plan 已创建，已自动生成 7 个关联任务");
```

#### Phase 3: 执行过程中（自动执行）

```javascript
// 完成 Phase 1
await todo_batch_complete({
  codes: ["todo-skills-shared-refs"]
});

// 自动更新 Plan 进度
await plan_update({
  code: "plan-skills-optimization",
  progress: 14  // 1/7 ≈ 14%
});

console.log("✅ Phase 1 完成，整体进度: 14%");

// 开始 Phase 2
await todo_batch_start({
  codes: ["todo-skills-todo"]
});

console.log("🚀 开始 Phase 2: 创建 Todo Skills");
```

#### Phase 4: 关键决策时（自动执行）

```javascript
// 做出重要决策：拆分为 6 个 skills
await memory_create({
  code: "mem-skills-architecture-decision",
  title: "Skills 拆分架构决策",
  content: `
# Skills 拆分为 6 个专项

## 决策背景
原 llm-workflow 和 llm-workflow-mcp 过于冗长（779-1002行）

## 最终方案
拆分为 memory/plan/todo 三个功能，CLI 和 MCP 各 3 个

## 优势
- 单一职责
- 按需加载
- 上下文成本降低 70%

## 实施日期
2024-12-05
  `,
  category: "架构决策",
  tags: ["skills", "architecture", "optimization"]
});

console.log("📝 已记录架构决策到 Memory");
```

## 🎨 Orchestrator 的核心功能

### 功能 1：上下文自动加载

**在以下时机自动读取：**
- ✅ 进入 Plan 模式
- ✅ 用户提到"之前"、"历史"、"记录"
- ✅ 开始新对话（检查是否有未完成任务）

### 功能 2：进度自动跟踪

**完全自动，无需用户干预：**
- ✅ 开始任务 → `todo_batch_start()`
- ✅ 完成任务 → `todo_batch_complete()`
- ✅ 更新 Plan → `plan_update({ progress })`
- ✅ 全部完成 → `plan_update({ progress: 100 })`

### 功能 3：知识自动积累

**在关键节点自动记录：**
- ✅ 架构决策 → `memory_create(category: "架构决策")`
- ✅ 复杂 Bug 修复 → `memory_create(category: "问题解决")`
- ✅ 可复用代码 → `memory_create(category: "代码示例")`
- ✅ 新的规范 → `memory_create(category: "项目规范")`

### 功能 4：智能查询

**主动搜索历史记录：**
```javascript
// 用户问："之前 JWT 怎么实现的？"
// 你应该：

// 1. 搜索 Memory
const memories = await memory_search({ keyword: "JWT" });

// 2. 搜索 Plan
const plans = await plan_list({ scope: "all" });
const jwtPlans = plans.filter(p =>
  p.title.includes("JWT") || p.content.includes("JWT")
);

// 3. 搜索 Todo
const todos = await todo_list({ scope: "all" });
const jwtTodos = todos.filter(t =>
  t.title.includes("JWT") || t.description.includes("JWT")
);

// 4. 综合返回
return `
📚 找到以下相关记录：

**Memory (${memories.length} 条):**
${memories.map(m => `- ${m.title}`).join('\n')}

**Plan (${jwtPlans.length} 个):**
${jwtPlans.map(p => `- ${p.title} (进度: ${p.progress}%)`).join('\n')}

**Todo (${jwtTodos.length} 个):**
${jwtTodos.map(t => `- ${t.title} [${t.status}]`).join('\n')}
`;
```

## 🚀 与 Plan 模式集成

### Plan 模式工作流（你应该遵循的流程）

```
用户进入 Plan 模式
    ↓
[自动] 调用 workflow-orchestrator
    ↓
读取 Memory/Plan/Todo（获取上下文）
    ↓
开始规划（基于历史上下文）
    ↓
规划完成，创建 Plan
    ↓
[自动] 拆解 Plan 为 Todos
    ↓
[自动] 批量创建 Todos
    ↓
用户批准，开始执行
    ↓
[自动] 标记 Todo 为 in_progress
    ↓
完成一个步骤
    ↓
[自动] 标记 Todo 为 completed
    ↓
[自动] 更新 Plan 进度
    ↓
[自动] 记录关键决策到 Memory
    ↓
所有任务完成
    ↓
[自动] 标记 Plan 为 completed (progress=100)
    ↓
[自动] 记录项目总结到 Memory
```

### 关键集成点

#### 集成点 1：EnterPlanMode 触发后

```javascript
// 在 system prompt 中你会看到 "plan mode is active"
// 此时你应该：

async function onEnterPlanMode(userRequest) {
  // 1. 提取关键词
  const keywords = extractKeywords(userRequest);

  // 2. 读取上下文
  const context = {
    memories: await memory_search({ keyword: keywords[0] }),
    plans: await plan_list({ scope: "all" }),
    todos: await todo_list({ scope: "all" })
  };

  // 3. 在规划时引用这些上下文
  console.log(`
📊 已读取历史上下文：
- Memory: ${context.memories.length} 条
- Plan: ${context.plans.length} 个
- Todo: ${context.todos.length} 个
  `);

  // 4. 开始规划...
}
```

#### 集成点 2：ExitPlanMode 触发前

```javascript
// 在调用 ExitPlanMode 之前
// 此时你应该：

async function beforeExitPlanMode(planContent) {
  // 1. 创建 Plan
  const plan = await plan_create({
    code: generatePlanCode(),
    title: extractTitle(planContent),
    description: extractDescription(planContent),
    content: planContent
  });

  // 2. 自动拆解为 Todos
  const todos = extractTodosFromPlan(planContent);
  await todo_batch_create({ items: todos });

  // 3. 告知用户
  console.log(`
✅ Plan 已创建并同步到 llm-memory
   - Plan Code: ${plan.code}
   - 已创建 ${todos.length} 个关联任务
  `);
}
```

#### 集成点 3：每次使用工具完成重要操作后

```javascript
// 在以下情况后自动调用：

// 1. 编辑文件后
afterEdit(file) {
  if (isImportantFile(file)) {
    markTodoAsInProgress(currentTodo);
  }
}

// 2. 完成一个模块后
afterModuleComplete(module) {
  markTodoAsCompleted(currentTodo);
  updatePlanProgress();
  createMemoryIfNeeded(module);
}

// 3. 遇到错误并解决后
afterBugFixed(bug) {
  markTodoAsCompleted(bugfixTodo);
  createMemory({
    category: "问题解决",
    content: bugFixSolution
  });
}
```

## 🤖 自动化决策规则

### 何时自动创建 Memory

**必须创建（不询问用户）：**
- 你做了架构决策（如选择技术方案）
- 你解决了复杂 Bug（>30分钟排查）
- 你实现了可复用的代码模式
- 你定义了新的项目规范

**询问后创建：**
- 调试技巧（可选）
- 第三方库使用经验（可选）

### 何时自动更新进度

**必须更新（不询问用户）：**
- 完成任何一个 Todo → 立即更新 Plan 进度
- 所有 Todos 完成 → 立即标记 Plan 为 completed

**计算公式：**
```javascript
progress = Math.round((completedTodos / totalTodos) * 100)
```

### 何时自动标记 Todo 状态

**自动标记为 in_progress：**
- 你开始执行任务（如使用 Edit/Write 工具）

**自动标记为 completed：**
- 你完成了任务的所有要求
- 测试通过（如果有测试要求）
- 你说"已完成 XXX"

## 📝 代码示例：完整的集成

```javascript
// ========================================
// Claude Code 的核心工作流集成
// ========================================

class WorkflowOrchestrator {

  // 1. Plan 模式启动时调用
  async onPlanModeEnter(userRequest) {
    console.log("🔍 正在读取历史上下文...");

    // 读取相关上下文
    const context = await this.loadContext(userRequest);

    // 显示上下文摘要
    this.displayContextSummary(context);

    // 返回上下文供规划使用
    return context;
  }

  // 2. 创建 Plan 后调用
  async onPlanCreated(planCode, planContent) {
    console.log("📋 正在创建 Plan 和关联任务...");

    // 拆解 Todos
    const todos = this.extractTodos(planContent);

    // 批量创建
    await todo_batch_create({ items: todos });

    console.log(`✅ 已自动创建 ${todos.length} 个任务`);
  }

  // 3. 任务执行中调用
  async onTaskProgress(todoCode, status) {
    // 更新 Todo 状态
    if (status === 'started') {
      await todo_batch_start({ codes: [todoCode] });
    } else if (status === 'completed') {
      await todo_batch_complete({ codes: [todoCode] });
    }

    // 自动更新 Plan 进度
    const planCode = this.getPlanForTodo(todoCode);
    if (planCode) {
      const progress = await this.calculateProgress(planCode);
      await plan_update({ code: planCode, progress });

      console.log(`📊 整体进度已更新: ${progress}%`);
    }
  }

  // 4. 关键决策时调用
  async onImportantDecision(decision) {
    console.log("📝 正在记录关键决策...");

    await memory_create({
      code: this.generateMemoryCode(decision),
      title: decision.title,
      content: decision.content,
      category: decision.category,
      tags: decision.tags
    });

    console.log("✅ 决策已记录到 Memory");
  }

  // 5. 用户查询时调用
  async onUserQuery(query) {
    console.log("🔍 正在搜索历史记录...");

    const results = await this.searchAll(query);

    return this.formatSearchResults(results);
  }

  // ========================================
  // 辅助方法
  // ========================================

  async loadContext(userRequest) {
    const keywords = this.extractKeywords(userRequest);

    return {
      memories: await memory_search({ keyword: keywords[0] }),
      plans: await plan_list({ scope: "all" }),
      todos: await todo_list({ scope: "all" })
    };
  }

  extractTodos(planContent) {
    // 从 Plan 内容中提取 Phase/步骤，转为 Todos
    const phases = planContent.match(/## Phase \d+:(.*)/g) || [];

    return phases.map((phase, index) => ({
      code: `todo-${this.currentPlanCode}-phase-${index + 1}`,
      title: phase.replace(/## Phase \d+:\s*/, ''),
      priority: index === 0 ? 4 : (index === 1 ? 3 : 2)
    }));
  }

  async calculateProgress(planCode) {
    // 获取 Plan 的所有关联 Todos
    const allTodos = await todo_list({ scope: "all" });
    const relatedTodos = allTodos.filter(t =>
      t.code.startsWith(`todo-${planCode}`)
    );

    // 计算完成的数量
    const completed = relatedTodos.filter(t =>
      t.status === 'completed'
    ).length;

    // 返回百分比
    return Math.round((completed / relatedTodos.length) * 100);
  }

  async searchAll(query) {
    const keywords = this.extractKeywords(query);

    return {
      memories: await memory_search({ keyword: keywords[0] }),
      plans: (await plan_list({ scope: "all" })).filter(p =>
        this.matchesQuery(p, keywords)
      ),
      todos: (await todo_list({ scope: "all" })).filter(t =>
        this.matchesQuery(t, keywords)
      )
    };
  }
}

// ========================================
// 使用示例
// ========================================

const orchestrator = new WorkflowOrchestrator();

// Plan 模式启动
const context = await orchestrator.onPlanModeEnter(userRequest);

// Plan 创建后
await orchestrator.onPlanCreated(planCode, planContent);

// 任务执行中
await orchestrator.onTaskProgress(todoCode, 'started');
await orchestrator.onTaskProgress(todoCode, 'completed');

// 关键决策
await orchestrator.onImportantDecision(decision);

// 用户查询
const results = await orchestrator.onUserQuery("JWT 怎么实现的？");
```

## 🔗 调用 Memory/Plan/Todo Skills

Orchestrator 通过以下方式调用专项 skills：

### 方式 1：直接使用 MCP 工具（推荐）

```javascript
// 如果 llm-memory MCP 可用，直接调用工具
await memory_create({ ... });
await plan_update({ ... });
await todo_batch_complete({ ... });
```

### 方式 2：通过 Skill 工具调用

```javascript
// 如果需要使用 skill 的额外指导
// 触发对应的 skill，让它提供详细的使用建议
```

## 📚 参考文档

- [Memory Skills](../memory-cli/SKILL.md) / [Memory MCP](../memory-mcp/SKILL.md)
- [Plan Skills](../plan-cli/SKILL.md) / [Plan MCP](../plan-mcp/SKILL.md)
- [Todo Skills](../todo-cli/SKILL.md) / [Todo MCP](../todo-mcp/SKILL.md)
- [最佳实践](../shared-references/best-practices.md)

## ⚠️ 重要提示

**这个 skill 是 Claude Code 的内部集成指南，不是给用户看的！**

用户只需要：
- 正常使用 Claude Code
- 进入 Plan 模式规划任务
- 执行任务

你（Claude Code）会自动：
- 读取历史上下文
- 创建 Plan 和 Todos
- 跟踪进度
- 记录知识
- 响应查询

**对用户完全透明，无感知！** ✨
