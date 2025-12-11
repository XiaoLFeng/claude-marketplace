# MCP 工具详解

## Plan 管理工具

### plan_list

列出所有计划。

```javascript
plan_list({
  scope: "all"  // personal | group | global | all
})

// 返回
[
  {
    code: "plan-user-auth",
    title: "用户认证系统",
    progress: 45,
    status: "in_progress",  // pending | in_progress | completed
    created_at: "2025-01-01T00:00:00Z",
    updated_at: "2025-01-02T00:00:00Z"
  }
]
```

### plan_create

创建新计划。

```javascript
plan_create({
  code: "plan-user-auth",           // 必填：唯一标识
  title: "用户认证系统",             // 必填：计划标题
  description: "实现完整认证功能",   // 必填：简短描述
  content: "## 目标\n- 登录注册..."  // 必填：Markdown 详细内容
})
```

### plan_get

获取计划详情。

```javascript
plan_get({
  code: "plan-user-auth"  // 必填：计划 code
})

// 返回完整计划信息，包括 content
```

### plan_update

更新计划。

```javascript
plan_update({
  code: "plan-user-auth",    // 必填：计划 code
  title: "新标题",            // 可选
  description: "新描述",      // 可选
  content: "新内容",          // 可选
  progress: 75               // 可选：0-100，系统自动调整状态
})

// progress 规则：
// 0 → status = pending
// 1-99 → status = in_progress
// 100 → status = completed
```

---

## Todo 管理工具

### todo_list

列出所有任务。

```javascript
todo_list({
  scope: "all"  // personal | group | all
})

// 返回
[
  {
    code: "todo-auth-login",
    title: "实现登录功能",
    description: "[Task-A] 登录页面和 API",
    priority: 3,
    status: 1,  // 0=待处理 1=进行中 2=已完成 3=已取消
    created_at: "2025-01-01T00:00:00Z"
  }
]
```

### todo_batch_create

批量创建任务（最多 100 个）。

```javascript
todo_batch_create({
  items: [
    {
      code: "todo-auth-login",     // 必填：唯一标识
      title: "实现登录功能",        // 必填：任务标题
      description: "[Task-A] ...",  // 可选：详细描述
      priority: 3                   // 可选：1-4，默认 2
    },
    {
      code: "todo-auth-register",
      title: "实现注册功能",
      priority: 3
    }
  ]
})
```

### todo_batch_start

批量开始任务（状态 0 → 1）。

```javascript
todo_batch_start({
  codes: ["todo-auth-login", "todo-auth-register"]
})
```

### todo_batch_complete

批量完成任务（状态 → 2）。

```javascript
todo_batch_complete({
  codes: ["todo-auth-login"]
})
```

### todo_batch_cancel

批量取消任务（状态 → 3）。

```javascript
todo_batch_cancel({
  codes: ["todo-auth-deprecated"]
})
```

### todo_batch_update

批量更新任务。

```javascript
todo_batch_update({
  items: [
    {
      code: "todo-auth-login",     // 必填：任务 code
      title: "新标题",              // 可选
      description: "新描述",        // 可选
      priority: 4,                  // 可选：1-4
      status: 1                     // 可选：0-3
    }
  ]
})
```

### todo_final

清理当前作用域内所有任务（不可恢复）。

```javascript
todo_final({
  scope: "all"  // personal | group | all
})
```

---

## Memory 管理工具

### memory_list

列出所有记忆。

```javascript
memory_list({
  scope: "all"  // personal | group | global | all
})

// 返回
[
  {
    code: "mem-jwt-decision",
    title: "JWT vs Session 选型",
    category: "技术决策",
    tags: ["auth", "jwt"],
    priority: 2,
    created_at: "2025-01-01T00:00:00Z"
  }
]
```

### memory_search

搜索记忆（标题和内容模糊匹配）。

```javascript
memory_search({
  keyword: "jwt",      // 必填：搜索关键词
  scope: "all"         // 可选：作用域
})
```

### memory_create

创建记忆。

```javascript
memory_create({
  code: "mem-jwt-decision",        // 必填：唯一标识
  title: "JWT vs Session 选型",    // 必填：标题
  content: "## 决策\n选择 JWT...", // 必填：Markdown 内容
  category: "技术决策",             // 可选：分类
  tags: ["auth", "jwt"],           // 可选：标签数组
  global: false                    // 可选：是否全局可见
})
```

### memory_get

获取记忆详情。

```javascript
memory_get({
  code: "mem-jwt-decision"  // 必填：记忆 code
})

// 返回完整记忆信息，包括 content
```

### memory_update

更新记忆。

```javascript
memory_update({
  code: "mem-jwt-decision",  // 必填：记忆 code
  title: "新标题",            // 可选
  content: "新内容",          // 可选
  category: "新分类",         // 可选
  tags: ["new-tag"],         // 可选
  priority: 3                // 可选：1-4
})
```

### memory_delete

删除记忆（不可恢复）。

```javascript
memory_delete({
  code: "mem-jwt-decision"  // 必填：记忆 code
})
```

---

## Group 管理工具

### group_add_path

添加路径到组。

```javascript
group_add_path({
  group_name: "my-team",  // 必填：组名称
  path: "/path/to/project" // 可选：路径，默认当前目录
})
```

**用途**：将多个项目路径加入同一组，实现数据共享。
