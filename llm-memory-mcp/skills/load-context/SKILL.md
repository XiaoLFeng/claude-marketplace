---
name: load-context
description: |
  上下文加载器 (MCP版本) - 在新对话或 Plan 模式启动时自动加载全局上下文。

  **何时调用此 Skill：**
  - 进入新对话
  - 进入 Plan 模式
  - 切换项目路径
  - 用户说"加载上下文"、"当前状态"、"有什么进展"

  **不调用此 Skill：**
  - 只需要查询历史（使用 search-history）
  - 单独操作 memory/plan/todo（使用对应的 skill）
  - 只查看单个类型的数据（使用对应的 list 工具）
---

# Load Context Skill

自动加载全局上下文，汇总展示当前项目的 memory、plan、todo 状态。

## ⚡ 快速参考

### 触发时机

```
主动触发（自动）：
  - 新对话启动
  - Plan 模式启动
  - 项目路径切换

被动触发（用户请求）：
  - "加载上下文"
  - "当前状态"
  - "有什么进展"
```

### 上下文范围

```
Memory:   最相关的 5 条记录
Plan:     所有未完成的计划（progress < 100）
Todo:     所有未完成的任务（status != completed）
```

---

## 🔧 核心操作

### 加载上下文

**操作流程**：

```javascript
// 1. 提取用户请求中的关键词
const keywords = extractKeywords(userRequest);

// 2. 并行加载三类上下文
const [memories, plans, todos] = await Promise.all([
  memory_search({ keyword: keywords, scope: "all" }),
  plan_list({ scope: "all" }),
  todo_list({ scope: "all" })
]);

// 3. 过滤和汇总
const context = {
  memories: memories.slice(0, 5),  // 最相关的 5 条
  plans: plans.filter(p => p.progress < 100),  // 未完成计划
  todos: todos.filter(t => t.status !== 2)  // 未完成任务
};

// 4. 格式化展示
displayContext(context);
```

### 使用的 MCP 工具

```javascript
// 搜索记忆
memory_search({
  keyword: "关键词",
  scope: "all"  // personal/group/global/all
})

// 列出计划
plan_list({
  scope: "all"  // personal/group/all
})

// 列出待办
todo_list({
  scope: "all"  // personal/group/all
})
```

---

## 📊 展示格式

### 控制台输出示例

```
🔍 当前上下文加载完成

📚 相关 Memory（5条）
  1. [mem-jwt-decision] JWT 技术选型
  2. [mem-api-design] API 设计文档
  3. [mem-database-schema] 数据库架构
  ...

📋 进行中的 Plan（2个）
  1. [plan-auth-refactor] 用户认证系统重构 (进度: 45%)
  2. [plan-api-redesign] API 重新设计 (进度: 20%)

✅ 待完成的 Todo（8个）
  🔴 [todo-fix-login-bug] 修复登录 Bug (优先级: 4)
  🟠 [todo-add-api-docs] 添加 API 文档 (优先级: 3)
  🟡 [todo-update-readme] 更新 README (优先级: 2)
  ...
```

---

## 🎯 使用场景

### 场景 1：新对话启动

```
用户：开始新对话
↓
load-context 自动触发
↓
并行加载 memory/plan/todo
↓
展示当前上下文
```

**好处**：快速了解项目当前状态，避免重复询问

### 场景 2：Plan 模式启动

```
用户：进入 Plan 模式
↓
load-context 自动触发
↓
加载所有相关上下文
↓
帮助制定计划
```

**好处**：基于历史信息制定更合理的计划

### 场景 3：手动加载

```
用户："当前有什么进展？"
↓
load-context 识别意图
↓
加载并展示上下文
```

**好处**：随时查看项目状态

---

## 📚 MCP 工具清单

本 Skill 使用以下 MCP 工具：

- `memory_search` - 搜索记忆（来自 memory-mcp）
- `plan_list` - 列出计划（来自 plan-mcp）
- `todo_list` - 列出待办（来自 todo-mcp）

详见：[完整工具参考](./references/tools.md)

---

## 🔗 参考文档

- [完整工具参考](./references/tools.md) - MCP 工具详细说明
- [使用示例](./references/examples.md) - 真实场景案例
- [Memory Skill](../memory-mcp/SKILL.md) - 记忆管理
- [Plan Skill](../plan-mcp/SKILL.md) - 计划管理
- [Todo Skill](../todo-mcp/SKILL.md) - 待办管理
- [最佳实践](../shared-references/best-practices.md) - 优化建议
- [架构迁移](../shared-references/architecture-migration.md) - 从 workflow-orchestrator 迁移
