# Bridge Plan Todo - MCP 工具参考

## 使用的 MCP 工具

### plan_get

获取计划详情。

**参数**：

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| code | string | ✅ | 计划唯一标识码 |

**示例**：

```javascript
plan_get({
  code: "plan-auth-refactor"
})
```

**返回**：

```json
{
  "code": "plan-auth-refactor",
  "title": "用户认证系统重构",
  "description": "采用 JWT 机制",
  "content": "# 详细内容...",
  "progress": 45,
  "status": "in_progress"
}
```

---

### plan_update

更新计划（含进度）。

**参数**：

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| code | string | ✅ | 计划唯一标识码 |
| title | string | ❌ | 新标题 |
| description | string | ❌ | 新描述 |
| content | string | ❌ | 新内容 |
| progress | number | ❌ | 进度 0-100 |

**示例**：

```javascript
plan_update({
  code: "plan-auth-refactor",
  progress: 50
})
```

---

### todo_list

列出所有待办。

**参数**：

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| scope | string | ❌ | 作用域：personal/group/all |

**示例**：

```javascript
todo_list({
  scope: "all"
})
```

---

### todo_batch_create

批量创建待办。

**参数**：

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| items | array | ✅ | 待办列表（最多 100 个） |
| scope | string | ❌ | 作用域 |

**items 结构**：

| 字段 | 类型 | 必填 | 说明 |
|------|------|------|------|
| code | string | ✅ | 待办唯一标识码 |
| title | string | ✅ | 待办标题 |
| description | string | ❌ | 待办描述 |
| priority | number | ❌ | 优先级 1-4（默认 2） |

**示例**：

```javascript
todo_batch_create({
  items: [
    {
      code: "todo-auth-refactor-1-1",
      title: "设计 users 表结构",
      description: "Phase 1 Step 1",
      priority: 4
    },
    {
      code: "todo-auth-refactor-1-2",
      title: "设计 refresh_tokens 表结构",
      description: "Phase 1 Step 2",
      priority: 4
    }
  ],
  scope: "personal"
})
```

**返回**：

```json
{
  "success": true,
  "created": 2,
  "failed": 0,
  "details": []
}
```

---

### todo_batch_complete

批量完成待办。

**参数**：

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| codes | array | ✅ | 待办 code 列表（最多 100 个） |

**示例**：

```javascript
todo_batch_complete({
  codes: ["todo-auth-refactor-1-1", "todo-auth-refactor-1-2"]
})
```

---

## 工具组合使用

### Plan → Todo 转换流程

```javascript
// 1. 获取计划详情
const plan = await plan_get({ code: "plan-xxx" });

// 2. 解析内容生成 todos
const todos = parsePlanToTodos(plan);

// 3. 批量创建
await todo_batch_create({ items: todos });
```

### Todo → Plan 同步流程

```javascript
// 1. 获取所有关联 todos
const allTodos = await todo_list({ scope: "all" });
const relatedTodos = allTodos.filter(t =>
  t.code.startsWith(`todo-${planCode}`)
);

// 2. 计算进度
const completed = relatedTodos.filter(t => t.status === 2).length;
const progress = Math.round((completed / relatedTodos.length) * 100);

// 3. 更新计划
await plan_update({ code: planCode, progress });
```

---

## 参考链接

- [Plan MCP 工具完整文档](../../plan-mcp/references/tools.md)
- [Todo MCP 工具完整文档](../../todo-mcp/references/tools.md)
- [批量操作指南](../../todo-mcp/references/batch-operations.md)
