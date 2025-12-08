# Load Context - MCP 工具参考

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
    "created_at": "2024-12-01T10:00:00Z"
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
    "status": 0,
    "priority": 4,
    "created_at": "2024-12-01T10:00:00Z"
  }
]
```

---

## 工具组合使用

### 并行加载上下文

```javascript
const [memories, plans, todos] = await Promise.all([
  memory_search({ keyword: "关键词", scope: "all" }),
  plan_list({ scope: "all" }),
  todo_list({ scope: "all" })
]);
```

### 过滤未完成项

```javascript
// 过滤未完成的计划
const activePlans = plans.filter(p => p.progress < 100);

// 过滤未完成的待办
const activeTodos = todos.filter(t => t.status !== 2);
```

---

## 参考链接

- [Memory MCP 工具](../../memory-mcp/references/tools.md)
- [Plan MCP 工具](../../plan-mcp/references/tools.md)
- [Todo MCP 工具](../../todo-mcp/references/tools.md)
