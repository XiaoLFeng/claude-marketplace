# Search History - MCP 工具参考

## 使用的 MCP 工具

### memory_search

搜索记忆库中的相关记录。

**参数**：

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| keyword | string | ✅ | 搜索关键词 |
| scope | string | ❌ | 作用域：personal/group/global/all（默认 all） |

**示例**：

```javascript
memory_search({
  keyword: "认证",
  scope: "all"
})
```

**返回**：

```json
[
  {
    "code": "mem-jwt-decision",
    "title": "JWT 认证方案选型",
    "category": "架构决策",
    "tags": ["auth", "jwt"],
    "created_at": "2024-12-01T10:00:00Z",
    "updated_at": "2024-12-01T10:00:00Z"
  }
]
```

---

### plan_list

列出所有计划。

**参数**：

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| scope | string | ❌ | 作用域：personal/group/all（默认 all） |

**示例**：

```javascript
plan_list({
  scope: "all"
})
```

**返回**：

```json
[
  {
    "code": "plan-auth-refactor",
    "title": "用户认证系统重构",
    "description": "采用 JWT 机制",
    "progress": 45,
    "status": "in_progress",
    "created_at": "2024-12-01T10:00:00Z"
  }
]
```

---

### todo_list

列出所有待办。

**参数**：

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| scope | string | ❌ | 作用域：personal/group/all（默认 all） |

**示例**：

```javascript
todo_list({
  scope: "all"
})
```

**返回**：

```json
[
  {
    "code": "todo-fix-login",
    "title": "修复登录 Bug",
    "description": "用户无法登录",
    "status": 2,
    "priority": 4,
    "created_at": "2024-12-01T10:00:00Z"
  }
]
```

---

## 搜索策略

### 并行搜索

```javascript
async function searchHistory(keyword) {
  const [memories, plans, todos] = await Promise.all([
    memory_search({ keyword, scope: "all" }),
    plan_list({ scope: "all" }),
    todo_list({ scope: "all" })
  ]);

  return { memories, plans, todos };
}
```

### 结果过滤

由于 `plan_list` 和 `todo_list` 不支持关键词搜索，需要在客户端过滤：

```javascript
function filterByKeyword(items, keyword, fields = ['title', 'description']) {
  const lowerKeyword = keyword.toLowerCase();

  return items.filter(item =>
    fields.some(field =>
      item[field] && item[field].toLowerCase().includes(lowerKeyword)
    )
  );
}

// 使用
const filteredPlans = filterByKeyword(plans, keyword, ['title', 'description']);
const filteredTodos = filterByKeyword(todos, keyword, ['title', 'description']);
```

### 结果排序

按更新时间倒序排列：

```javascript
function sortByDate(items) {
  return items.sort((a, b) =>
    new Date(b.updated_at || b.created_at) -
    new Date(a.updated_at || a.created_at)
  );
}
```

### 结果限制

```javascript
const MAX_RESULTS_PER_TYPE = 10;

const results = {
  memories: memories.slice(0, MAX_RESULTS_PER_TYPE),
  plans: filteredPlans.slice(0, MAX_RESULTS_PER_TYPE),
  todos: filteredTodos.slice(0, MAX_RESULTS_PER_TYPE)
};
```

---

## 参考链接

- [Memory MCP 工具完整文档](../../memory-mcp/references/tools.md)
- [Plan MCP 工具完整文档](../../plan-mcp/references/tools.md)
- [Todo MCP 工具完整文档](../../todo-mcp/references/tools.md)
