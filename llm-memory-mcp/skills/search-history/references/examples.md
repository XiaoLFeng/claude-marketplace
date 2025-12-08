# Search History - 使用示例

## 场景 1：查询历史决策

### 用户请求

```
"之前认证系统是怎么做的？"
```

### 识别关键词

```javascript
// 识别到 "之前" 触发历史查询
const keyword = "认证";
```

### 执行搜索

```javascript
// 并行搜索
const [memories, plans, todos] = await Promise.all([
  memory_search({ keyword: "认证", scope: "all" }),
  plan_list({ scope: "all" }),
  todo_list({ scope: "all" })
]);

// 过滤 plans 和 todos
const filteredPlans = plans.filter(p =>
  p.title.includes("认证") || p.description?.includes("认证")
);
const filteredTodos = todos.filter(t =>
  t.title.includes("认证") || t.description?.includes("认证")
);
```

### 展示结果

```
🔍 搜索 "认证" 的结果：

### 💡 Memory 记录
- [mem-jwt-decision] JWT 认证方案选型
  分类: 架构决策 | 标签: auth, jwt
- [mem-auth-flow] 用户认证流程设计
  分类: 技术文档 | 标签: auth, flow

### 📋 Plan 计划
- ⏳ [plan-auth-refactor] 用户认证系统重构 (进度: 45%)

### ✅ Todo 任务
- ✅ 🔴 [todo-auth-refactor-1-1] 设计 users 表结构
- ✅ 🔴 [todo-auth-refactor-1-2] 设计 refresh_tokens 表结构
- ⏳ 🟠 [todo-auth-refactor-2-1] 实现 JWT 生成逻辑
```

---

## 场景 2：查找 Bug 解决方案

### 用户请求

```
"上次内存泄漏是怎么解决的？"
```

### 执行搜索

```javascript
const keyword = "内存泄漏";
const results = await searchHistory(keyword);
```

### 展示结果

```
🔍 搜索 "内存泄漏" 的结果：

### 💡 Memory 记录
- [mem-memory-leak-websocket] WebSocket 内存泄漏问题排查与解决
  分类: 问题排查 | 标签: bug, memory-leak, nodejs

### ✅ Todo 任务
- ✅ 🔴 [todo-fix-memory-leak] 修复内存泄漏问题
```

### 进一步查看详情

```javascript
// 获取详细内容
const detail = await memory_get({ code: "mem-memory-leak-websocket" });
console.log(detail.content);
```

---

## 场景 3：检查任务状态

### 用户请求

```
"上次那个登录功能做完了吗？"
```

### 执行搜索

```javascript
const keyword = "登录";
const results = await searchHistory(keyword);

// 重点关注 todos 的状态
const loginTodos = results.todos.filter(t =>
  t.title.includes("登录")
);
```

### 展示结果

```
🔍 搜索 "登录" 的结果：

### ✅ Todo 任务
- ✅ 🔴 [todo-fix-login-bug] 修复登录 Bug - 已完成
- ✅ 🟡 [todo-add-login-validation] 添加登录验证 - 已完成
- ⏳ 🟡 [todo-login-rate-limit] 登录限流 - 进行中

答：登录相关的大部分任务已完成，还有一个"登录限流"正在进行中。
```

---

## 场景 4：综合历史查询

### 用户请求

```
"之前有没有做过数据库优化相关的工作？"
```

### 执行搜索

```javascript
const keyword = "数据库优化";
const results = await searchHistory(keyword);
```

### 展示结果

```
🔍 搜索 "数据库优化" 的结果：

### 💡 Memory 记录
- [mem-query-optimization] 查询优化最佳实践
  分类: 最佳实践 | 标签: database, performance
- [mem-index-strategy] 索引策略设计
  分类: 架构决策 | 标签: database, index

### 📋 Plan 计划
- ✅ [plan-db-optimization] 数据库性能优化 (进度: 100%)

### ✅ Todo 任务
- ✅ 🟠 [todo-add-index] 添加缺失的索引
- ✅ 🟠 [todo-optimize-slow-query] 优化慢查询
- ✅ 🟡 [todo-update-explain] 更新查询分析文档
```

---

## 最佳实践

### 1. 关键词提取

```javascript
function extractKeywords(userInput) {
  // 移除常见停用词
  const stopWords = ['的', '是', '在', '有', '和', '了', '之前', '上次', '以前'];
  const words = userInput.split(/\s+/);
  return words.filter(w => !stopWords.includes(w));
}

// 使用
const keywords = extractKeywords("之前认证系统是怎么做的");
// ['认证', '系统', '怎么', '做']
```

### 2. 多关键词搜索

```javascript
async function searchMultipleKeywords(keywords) {
  const allResults = {
    memories: [],
    plans: [],
    todos: []
  };

  for (const keyword of keywords) {
    const results = await searchHistory(keyword);

    // 合并结果（去重）
    allResults.memories = mergeUnique(allResults.memories, results.memories, 'code');
    allResults.plans = mergeUnique(allResults.plans, results.plans, 'code');
    allResults.todos = mergeUnique(allResults.todos, results.todos, 'code');
  }

  return allResults;
}

function mergeUnique(arr1, arr2, key) {
  const map = new Map(arr1.map(item => [item[key], item]));
  arr2.forEach(item => map.set(item[key], item));
  return Array.from(map.values());
}
```

### 3. 结果格式化

```javascript
function formatSearchResults(results, keyword) {
  let output = `🔍 搜索 "${keyword}" 的结果：\n\n`;

  if (results.memories.length > 0) {
    output += "### 💡 Memory 记录\n";
    results.memories.forEach(m => {
      output += `- [${m.code}] ${m.title}\n`;
      output += `  分类: ${m.category || '默认'}`;
      if (m.tags?.length > 0) {
        output += ` | 标签: ${m.tags.join(', ')}`;
      }
      output += "\n";
    });
    output += "\n";
  }

  if (results.plans.length > 0) {
    output += "### 📋 Plan 计划\n";
    results.plans.forEach(p => {
      const icon = p.progress === 100 ? '✅' : p.progress > 0 ? '⏳' : '⏸️';
      output += `- ${icon} [${p.code}] ${p.title} (进度: ${p.progress}%)\n`;
    });
    output += "\n";
  }

  if (results.todos.length > 0) {
    output += "### ✅ Todo 任务\n";
    results.todos.forEach(t => {
      const statusIcon = t.status === 2 ? '✅' : t.status === 1 ? '⏳' : '⏸️';
      const priorityIcon = ['🟢', '🟢', '🟡', '🟠', '🔴'][t.priority] || '🟡';
      output += `- ${statusIcon} ${priorityIcon} [${t.code}] ${t.title}\n`;
    });
    output += "\n";
  }

  if (results.memories.length === 0 &&
      results.plans.length === 0 &&
      results.todos.length === 0) {
    output += "未找到相关记录。\n";
  }

  return output;
}
```
