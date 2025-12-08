# Load Context - 使用示例

## 场景 1：新对话启动加载

**用户请求**：开始新对话

**执行流程**：

```javascript
// 1. 识别新对话启动
const isNewConversation = true;

// 2. 并行加载上下文
const [memories, plans, todos] = await Promise.all([
  memory_search({ keyword: "", scope: "all" }),
  plan_list({ scope: "all" }),
  todo_list({ scope: "all" })
]);

// 3. 展示汇总
console.log("🔍 当前上下文：\n");
console.log(`📚 Memory: ${memories.length} 条`);
console.log(`📋 Plan: ${plans.filter(p => p.progress < 100).length} 个进行中`);
console.log(`✅ Todo: ${todos.filter(t => t.status !== 2).length} 个待完成`);
```

**输出**：

```
🔍 当前上下文：

📚 Memory: 15 条
📋 Plan: 2 个进行中
✅ Todo: 8 个待完成
```

---

## 场景 2：Plan 模式启动

**用户请求**：进入 Plan 模式，准备规划新功能

**执行流程**：

```javascript
// 1. 提取关键词
const keyword = "用户认证";

// 2. 搜索相关上下文
const [memories, plans, todos] = await Promise.all([
  memory_search({ keyword, scope: "all" }),
  plan_list({ scope: "all" }),
  todo_list({ scope: "all" })
]);

// 3. 过滤相关项
const relatedPlans = plans.filter(p =>
  p.title.includes(keyword) || p.description?.includes(keyword)
);

const relatedTodos = todos.filter(t =>
  t.title.includes(keyword) || t.description?.includes(keyword)
);

// 4. 展示
console.log(`📚 相关 Memory: ${memories.length} 条`);
memories.slice(0, 5).forEach(m =>
  console.log(`  - [${m.code}] ${m.title}`)
);

console.log(`\n📋 相关 Plan: ${relatedPlans.length} 个`);
relatedPlans.forEach(p =>
  console.log(`  - [${p.code}] ${p.title} (${p.progress}%)`)
);
```

**输出**：

```
📚 相关 Memory: 3 条
  - [mem-jwt-decision] JWT 认证方案选型
  - [mem-auth-flow] 用户认证流程设计
  - [mem-session-issue] Session 超时问题记录

📋 相关 Plan: 1 个
  - [plan-auth-refactor] 用户认证系统重构 (45%)
```

---

## 场景 3：手动请求加载

**用户请求**："当前有什么进展？"

**执行流程**：

```javascript
// 识别意图：查询当前状态
const intent = "status_query";

// 加载所有进行中的项目
const [plans, todos] = await Promise.all([
  plan_list({ scope: "all" }),
  todo_list({ scope: "all" })
]);

// 过滤进行中的
const inProgressPlans = plans.filter(p =>
  p.status === "in_progress"
);
const inProgressTodos = todos.filter(t =>
  t.status === 1  // in_progress
);

// 展示进展
console.log("📊 当前进展：\n");

console.log("📋 进行中的计划：");
inProgressPlans.forEach(p =>
  console.log(`  - [${p.code}] ${p.title} (进度: ${p.progress}%)`)
);

console.log("\n✅ 进行中的任务：");
inProgressTodos.forEach(t =>
  console.log(`  - [${t.code}] ${t.title}`)
);
```

**输出**：

```
📊 当前进展：

📋 进行中的计划：
  - [plan-auth-refactor] 用户认证系统重构 (进度: 45%)
  - [plan-api-redesign] API 重新设计 (进度: 20%)

✅ 进行中的任务：
  - [todo-auth-refactor-2-1] 实现 JWT 生成逻辑
  - [todo-api-endpoint] 开发 API 端点
```

---

## 最佳实践

### 1. 关键词提取

```javascript
// 从用户请求中提取关键词
function extractKeywords(userRequest) {
  // 移除停用词
  const stopWords = ['的', '是', '在', '有', '和', '了'];
  const words = userRequest.split(/\s+/);
  return words.filter(w => !stopWords.includes(w));
}
```

### 2. 结果限制

```javascript
// 限制返回数量，避免信息过载
const MAX_MEMORIES = 5;
const MAX_PLANS = 10;
const MAX_TODOS = 20;

const limitedMemories = memories.slice(0, MAX_MEMORIES);
const limitedPlans = plans.slice(0, MAX_PLANS);
const limitedTodos = todos.slice(0, MAX_TODOS);
```

### 3. 按优先级排序

```javascript
// 按优先级排序待办
const sortedTodos = todos.sort((a, b) => b.priority - a.priority);
```
