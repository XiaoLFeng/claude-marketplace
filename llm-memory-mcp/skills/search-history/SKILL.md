---
name: search-history
description: |
  历史查询器 (MCP版本) - 全局搜索 memory/plan/todo 的历史记录。

  **何时调用此 Skill：**
  - 用户询问"之前怎么做的"
  - 需要查找历史记录
  - 搜索相关内容
  - 用户说"查找..."、"搜索..."、"历史..."

  **不调用此 Skill：**
  - 只需要列出全部（使用对应的 list 工具）
  - 创建新记录（使用对应的 create 工具）
  - 加载全局上下文（使用 load-context）
---

# Search History Skill

全局搜索 memory/plan/todo 的历史记录，快速找到相关信息。

## ⚡ 快速参考

### 触发关键词

```
历史相关：
  - "之前..."、"历史..."
  - "上次..."、"以前..."
  - "曾经..."、"过去..."

查询相关：
  - "怎么做的"、"记录..."
  - "查找..."、"搜索..."
  - "有没有..."、"找一下..."
```

### 搜索范围

```
Memory:   搜索标题和内容
Plan:     搜索标题和描述
Todo:     搜索标题和描述
Scope:    all（所有作用域）
```

### 结果限制

```
每类最多返回 10 条
按相关性/时间排序
```

---

## 🔧 核心操作

### 识别查询意图

**关键词识别**：

```javascript
const historyKeywords = [
  // 时间词
  '之前', '历史', '上次', '以前', '曾经', '过去',

  // 查询词
  '怎么做的', '记录', '查找', '搜索', '找一下',
  '有没有', '是什么', '在哪里'
];

function isHistoryQuery(userInput) {
  return historyKeywords.some(kw => userInput.includes(kw));
}
```

### 并行搜索

**操作流程**：

```javascript
async function searchHistory(keyword) {
  // 1. 并行搜索三类数据
  const [memories, plans, todos] = await Promise.all([
    memory_search({ keyword, scope: "all" }),
    plan_list({ scope: "all" }),
    todo_list({ scope: "all" })
  ]);

  // 2. 过滤 plans（标题或描述包含关键词）
  const filteredPlans = plans.filter(p =>
    p.title.toLowerCase().includes(keyword.toLowerCase()) ||
    (p.description && p.description.toLowerCase().includes(keyword.toLowerCase()))
  );

  // 3. 过滤 todos（标题或描述包含关键词）
  const filteredTodos = todos.filter(t =>
    t.title.toLowerCase().includes(keyword.toLowerCase()) ||
    (t.description && t.description.toLowerCase().includes(keyword.toLowerCase()))
  );

  // 4. 限制结果数量
  return {
    memories: memories.slice(0, 10),
    plans: filteredPlans.slice(0, 10),
    todos: filteredTodos.slice(0, 10)
  };
}
```

**使用的 MCP 工具**：

```javascript
// 搜索记忆
memory_search({
  keyword: "关键词",
  scope: "all"
})

// 列出计划
plan_list({
  scope: "all"
})

// 列出待办
todo_list({
  scope: "all"
})
```

### 格式化展示

**输出格式**：

```javascript
function displayHistory(results, keyword) {
  console.log(`🔍 搜索 "${keyword}" 的结果：\n`);

  // Memory 结果
  if (results.memories.length > 0) {
    console.log("### 💡 Memory 记录");
    results.memories.forEach(m => {
      console.log(`- [${m.code}] ${m.title}`);
      if (m.category) console.log(`  分类: ${m.category}`);
    });
    console.log("");
  }

  // Plan 结果
  if (results.plans.length > 0) {
    console.log("### 📋 Plan 计划");
    results.plans.forEach(p => {
      const statusIcon = p.progress === 100 ? '✅' :
                        p.progress > 0 ? '⏳' : '⏸️';
      console.log(`- ${statusIcon} [${p.code}] ${p.title} (进度: ${p.progress}%)`);
    });
    console.log("");
  }

  // Todo 结果
  if (results.todos.length > 0) {
    console.log("### ✅ Todo 任务");
    results.todos.forEach(t => {
      const statusIcon = t.status === 2 ? '✅' :
                        t.status === 1 ? '⏳' :
                        t.status === 3 ? '❌' : '⏸️';
      const priorityIcon = ['🟢', '🟢', '🟡', '🟠', '🔴'][t.priority] || '🟡';
      console.log(`- ${statusIcon} ${priorityIcon} [${t.code}] ${t.title}`);
    });
    console.log("");
  }

  // 无结果
  if (results.memories.length === 0 &&
      results.plans.length === 0 &&
      results.todos.length === 0) {
    console.log("未找到相关记录。");
  }
}
```

---

## 📊 完整示例

### 示例 1：查询认证相关记录

**用户输入**：
```
"之前认证系统是怎么做的？"
```

**search-history 处理**：
```javascript
// 识别关键词："之前" + "认证"
const keyword = "认证";

// 并行搜索
const results = await searchHistory(keyword);

// 展示结果
displayHistory(results, keyword);
```

**输出**：
```
🔍 搜索 "认证" 的结果：

### 💡 Memory 记录
- [mem-jwt-decision] JWT 认证方案选型
  分类: 架构决策
- [mem-auth-flow] 用户认证流程设计
  分类: 技术文档

### 📋 Plan 计划
- ⏳ [plan-auth-refactor] 用户认证系统重构 (进度: 45%)

### ✅ Todo 任务
- ✅ 🔴 [todo-auth-refactor-1-1] 设计 users 表结构
- ✅ 🔴 [todo-auth-refactor-1-2] 设计 refresh_tokens 表结构
- ⏳ 🟠 [todo-auth-refactor-2-1] 实现 JWT 生成逻辑
```

### 示例 2：查找 Bug 修复记录

**用户输入**：
```
"上次内存泄漏是怎么解决的？"
```

**search-history 处理**：
```javascript
const keyword = "内存泄漏";
const results = await searchHistory(keyword);
displayHistory(results, keyword);
```

**输出**：
```
🔍 搜索 "内存泄漏" 的结果：

### 💡 Memory 记录
- [mem-memory-leak-fix] 内存泄漏问题排查与解决
  分类: 问题排查

### ✅ Todo 任务
- ✅ 🔴 [todo-fix-memory-leak] 修复内存泄漏问题
```

---

## 🎯 使用场景

### 场景 1：查询历史决策

```
用户："之前为什么选择 JWT？"
↓
search-history 识别历史查询
↓
搜索关键词 "JWT"
↓
找到 mem-jwt-decision
↓
展示决策记录
```

### 场景 2：查找解决方案

```
用户："以前遇到过类似的问题吗？"
↓
search-history 识别历史查询
↓
搜索相关关键词
↓
找到相关的 memory/plan/todo
↓
展示解决方案
```

### 场景 3：检查任务状态

```
用户："上次那个功能做完了吗？"
↓
search-history 识别历史查询
↓
搜索功能关键词
↓
找到相关的 plan/todo
↓
展示完成状态
```

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
- [Load Context](../load-context/SKILL.md) - 上下文加载
- [架构迁移](../shared-references/architecture-migration.md) - 从 workflow-orchestrator 迁移
