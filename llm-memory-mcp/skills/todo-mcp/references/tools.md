# Todo MCP 工具完整参考

嘿嘿~ 这是 todo-mcp skill 的所有 MCP 工具详细说明！(´∀`)💖

## 工具概览

Todo 系统提供 **6 个 MCP 工具**，支持待办任务的全生命周期管理。所有工具都使用 MCP 接口，返回统一的混合模式结果。

---

## 1️⃣ `todo_list` - 列出待办任务

### 功能
列出指定作用域内的所有待办任务，可按优先级、状态排序。

### 调用方式

```javascript
todo_list({
  scope: "personal" | "group" | "all"  // 可选，默认 all
})
```

### 参数详解

| 参数 | 类型 | 必需 | 说明 |
|------|------|------|------|
| `scope` | string | 否 | 作用域范围：`personal`（私有）、`group`（组内）、`all`（全部）|

### 返回格式

```javascript
// 返回成功示例
[
  {
    code: "todo-fix-login-bug [私有]",
    title: "修复登录 Bug",
    status: "pending",           // pending/in_progress/completed/cancelled
    priority: 4,                 // 1-4
    due_date: "2024-12-31"       // 可能为空
  },
  {
    code: "todo-add-api-docs [小组]",
    title: "添加 API 文档",
    status: "in_progress",
    priority: 2,
    due_date: null
  }
]
```

### 使用示例

```javascript
// 查看所有待办
todo_list({ scope: "all" })

// 查看私有待办
todo_list({ scope: "personal" })

// 查看组内待办
todo_list({ scope: "group" })
```

### 错误处理

```javascript
// 权限不足
{
  error: "无权限访问此作用域的数据",
  code: "PERMISSION_DENIED"
}

// 作用域无效
{
  error: "作用域参数无效: xxx",
  code: "INVALID_SCOPE"
}
```

---

## 2️⃣ `todo_batch_create` - 批量创建待办

### 功能
一次性创建多个待办任务，支持最多 100 个，使用混合模式返回（部分成功的处理）。

### 调用方式

```javascript
todo_batch_create({
  items: [
    {
      code: "todo-xxx",                    // 必填，活跃状态唯一
      title: "任务标题",                   // 必填
      description: "任务详情",             // 可选
      priority: 2,                         // 可选，1-4，默认2
      due_date: "2024-12-31T23:59:59Z"    // 可选，ISO 8601
    }
    // ... 最多 100 个
  ],
  scope: "personal" | "group"              // 可选，默认 personal
})
```

### 参数详解

**items 数组元素：**

| 字段 | 类型 | 必需 | 说明 |
|------|------|------|------|
| `code` | string | ✓ | 任务代码，活跃状态中唯一 |
| `title` | string | ✓ | 任务标题（≤100 字符） |
| `description` | string | 否 | 任务详细描述（支持 Markdown） |
| `priority` | number | 否 | 优先级 1-4，默认 2 |
| `due_date` | string | 否 | 截止日期（ISO 8601 格式） |

**作用域：**
- `personal`：当前目录私有（默认）
- `group`：加入组后在组内共享

### 返回格式（混合模式）

```javascript
// 全部成功
{
  success: true,
  message: "批量创建成功! 共处理 5 个待办事项",
  success_count: 5,
  fail_count: 0,
  items: [
    { code: "todo-1", status: "created" },
    { code: "todo-2", status: "created" },
    // ...
  ]
}

// 部分成功
{
  success: false,
  message: "批量创建部分完成! 成功 3 个，失败 2 个",
  success_count: 3,
  fail_count: 2,
  items: [
    { code: "todo-1", status: "created" },
    { code: "todo-2", status: "created" },
    { code: "todo-3", status: "created" }
  ],
  failures: [
    {
      code: "todo-bad-format",
      error: "code 格式错误: 全小写字母，可含连字符，开头末尾必须是字母"
    },
    {
      code: "todo-duplicate",
      error: "活跃状态中已存在相同的 code: todo-duplicate"
    }
  ]
}

// 全部失败
{
  success: false,
  message: "批量创建全部失败!",
  success_count: 0,
  fail_count: 5,
  failures: [
    // ... 所有失败项
  ]
}
```

### 使用示例

```javascript
// 创建单个任务（即使只有一个也建议用批量接口）
todo_batch_create({
  items: [{
    code: "todo-fix-login-bug",
    title: "修复登录 Bug",
    description: "用户报告无法登录，需要排查认证模块",
    priority: 4
  }],
  scope: "personal"
})

// 创建多个相关任务
todo_batch_create({
  items: [
    {
      code: "todo-design-db",
      title: "设计认证数据库架构",
      priority: 3,
      due_date: "2024-12-10T23:59:59Z"
    },
    {
      code: "todo-impl-jwt",
      title: "实现 JWT 令牌机制",
      description: "实现签发、验证、刷新逻辑\n\n需要参考：mem-jwt-vs-session-decision",
      priority: 4
    },
    {
      code: "todo-write-tests",
      title: "编写认证单元测试",
      priority: 2
    }
  ],
  scope: "personal"
})
```

### 错误处理与重试

```javascript
// 检查部分失败的情况
const result = todo_batch_create({ items: [...] })

if (result.fail_count > 0) {
  // 处理失败项
  const failedCodes = result.failures.map(f => f.code)
  console.log(`需要修正的任务: ${failedCodes.join(', ')}`)

  // 修正后重新提交失败的项
  const correctedItems = result.failures.map(f => {
    if (f.error.includes('code 格式错误')) {
      // 修正 code 格式
      return { ...originalItems[f.code], code: fixCode(f.code) }
    }
    return null
  }).filter(Boolean)

  if (correctedItems.length > 0) {
    todo_batch_create({ items: correctedItems })
  }
}
```

---

## 3️⃣ `todo_batch_start` - 批量开始待办

### 功能
将多个待办任务状态从 pending 变更为 in_progress，最多支持 100 个。

### 调用方式

```javascript
todo_batch_start({
  codes: ["todo-1", "todo-2", "todo-3"]  // 必填，最多 100 个
})
```

### 参数详解

| 参数 | 类型 | 必需 | 说明 |
|------|------|------|------|
| `codes` | string[] | ✓ | 待办代码数组，最多 100 个 |

### 返回格式（混合模式）

```javascript
// 全部成功
{
  success: true,
  message: "批量开始成功! 共处理 3 个待办事项",
  success_count: 3,
  fail_count: 0,
  items: [
    { code: "todo-1", status: "in_progress" },
    { code: "todo-2", status: "in_progress" },
    { code: "todo-3", status: "in_progress" }
  ]
}

// 部分成功（某些任务不存在或已完成）
{
  success: false,
  message: "批量开始部分完成! 成功 2 个，失败 1 个",
  success_count: 2,
  fail_count: 1,
  items: [
    { code: "todo-1", status: "in_progress" },
    { code: "todo-2", status: "in_progress" }
  ],
  failures: [
    {
      code: "todo-3",
      error: "任务不存在或已完成，无法开始"
    }
  ]
}
```

### 使用示例

```javascript
// 开始单个任务
todo_batch_start({
  codes: ["todo-fix-login-bug"]
})

// 开始多个任务
todo_batch_start({
  codes: ["todo-design-db", "todo-impl-jwt", "todo-write-tests"]
})

// 结合 todo_list 进行有条件的开始
const todos = todo_list({ scope: "personal" })
const pendingHighPriority = todos
  .filter(t => t.status === "pending" && t.priority >= 3)
  .map(t => t.code)

if (pendingHighPriority.length > 0) {
  todo_batch_start({ codes: pendingHighPriority })
}
```

### 状态转移

```
pending (0) → in_progress (1)

注意：
- 只能从 pending 转移到 in_progress
- 已完成的任务无法重新开始
- 已取消的任务无法重新开始
```

---

## 4️⃣ `todo_batch_complete` - 批量完成待办

### 功能
将多个待办任务标记为已完成，最多支持 100 个。

### 调用方式

```javascript
todo_batch_complete({
  codes: ["todo-1", "todo-2"]  // 必填，最多 100 个
})
```

### 参数详解

| 参数 | 类型 | 必需 | 说明 |
|------|------|------|------|
| `codes` | string[] | ✓ | 待办代码数组，最多 100 个 |

### 返回格式（混合模式）

```javascript
// 全部成功
{
  success: true,
  message: "批量完成成功! 共处理 2 个待办事项",
  success_count: 2,
  fail_count: 0,
  items: [
    { code: "todo-1", status: "completed" },
    { code: "todo-2", status: "completed" }
  ]
}

// 部分成功
{
  success: false,
  message: "批量完成部分成功! 成功 1 个，失败 1 个",
  success_count: 1,
  fail_count: 1,
  items: [
    { code: "todo-1", status: "completed" }
  ],
  failures: [
    {
      code: "todo-not-exist",
      error: "任务不存在"
    }
  ]
}
```

### 使用示例

```javascript
// 完成单个任务
todo_batch_complete({
  codes: ["todo-fix-login-bug"]
})

// 完成多个相关任务
todo_batch_complete({
  codes: ["todo-1", "todo-2", "todo-3"]
})

// 批量完成高优先级任务
const highPriorityDone = ["todo-impl-jwt", "todo-design-db"]
todo_batch_complete({ codes: highPriorityDone })
```

### 状态转移

```
pending/in_progress → completed

自动删除：已完成的任务会被存档，code 可以重用
```

---

## 5️⃣ `todo_batch_update` - 批量更新待办

### 功能
精细控制待办任务的状态和属性，最多支持 100 个。

### 调用方式

```javascript
todo_batch_update({
  items: [
    {
      code: "todo-1",          // 必填
      title: "新标题",         // 可选
      description: "新描述",   // 可选
      priority: 4,             // 可选，1-4
      status: 2                // 可选，0=pending, 1=in_progress, 2=completed, 3=cancelled
    }
    // ... 最多 100 个
  ]
})
```

### 参数详解

**items 数组元素：**

| 字段 | 类型 | 必需 | 说明 |
|------|------|------|------|
| `code` | string | ✓ | 任务代码 |
| `title` | string | 否 | 新标题 |
| `description` | string | 否 | 新描述（支持 Markdown） |
| `priority` | number | 否 | 新优先级 1-4 |
| `status` | number | 否 | 新状态：0=pending, 1=in_progress, 2=completed, 3=cancelled |

**状态值映射：**
```
0 = pending     （待处理）
1 = in_progress （进行中）
2 = completed   （已完成）
3 = cancelled   （已取消）
```

### 返回格式（混合模式）

```javascript
// 全部成功
{
  success: true,
  message: "批量更新成功! 共处理 2 个待办事项",
  success_count: 2,
  fail_count: 0,
  items: [
    { code: "todo-1", status: "completed", priority: 4 },
    { code: "todo-2", status: "in_progress", priority: 2 }
  ]
}

// 部分成功
{
  success: false,
  message: "批量更新部分完成! 成功 1 个，失败 1 个",
  success_count: 1,
  fail_count: 1,
  items: [
    { code: "todo-1", status: "completed" }
  ],
  failures: [
    {
      code: "todo-not-exist",
      error: "任务不存在"
    }
  ]
}
```

### 使用示例

```javascript
// 更新单个任务的优先级
todo_batch_update({
  items: [{
    code: "todo-1",
    priority: 4  // 升级为紧急
  }]
})

// 更新多个任务的状态
todo_batch_update({
  items: [
    {
      code: "todo-1",
      status: 2,  // 标记完成
      description: "完成！已部署到生产环境"
    },
    {
      code: "todo-2",
      status: 3   // 标记取消
    },
    {
      code: "todo-3",
      title: "新标题",
      priority: 3
    }
  ]
})

// 带优先级重新评估的场景
todo_batch_update({
  items: [
    { code: "todo-low-prio", priority: 1 },   // 降低优先级
    { code: "todo-high-prio", priority: 4 }   // 提升优先级
  ]
})
```

### 错误处理

```javascript
// 尝试更新不存在的任务
{
  code: "todo-not-exist",
  error: "任务不存在，无法更新"
}

// 状态值无效
{
  code: "todo-1",
  error: "状态值无效: 5，应该为 0-3"
}
```

---

## 6️⃣ `todo_final` - 清空所有待办

### 功能
一次性完成或清空指定作用域内的所有待办任务。**此操作不可恢复！**

### 调用方式

```javascript
todo_final({
  scope: "personal" | "group"  // 可选，默认 personal
})
```

### 参数详解

| 参数 | 类型 | 必需 | 说明 |
|------|------|------|------|
| `scope` | string | 否 | 作用域：`personal`（默认）或 `group` |

### 返回格式

```javascript
// 执行成功
{
  success: true,
  message: "已清空所有待办任务! 共处理 5 个任务",
  cleared_count: 5,
  scope: "personal"
}

// 无任务需要清空
{
  success: true,
  message: "没有待办任务需要清空",
  cleared_count: 0,
  scope: "personal"
}

// 错误示例
{
  success: false,
  error: "未加入任何组，无法清空组内任务",
  code: "NOT_IN_GROUP"
}
```

### 使用示例

```javascript
// 清空私有待办（谨慎操作！）
todo_final({ scope: "personal" })

// 清空组内待办（必须已加入组）
todo_final({ scope: "group" })

// 清空前的确认流程
const allTodos = todo_list({ scope: "personal" })
if (allTodos.length > 0) {
  console.log(`即将清空 ${allTodos.length} 个待办，无法恢复！`)
  // 用户确认后才执行
  todo_final({ scope: "personal" })
}
```

### 警告

```
⚠️ 重要提醒：
- 此操作不可逆转
- 所有待办任务会被永久删除
- 建议操作前备份重要信息
- 确认用户真的想要清空
```

---

## 🔄 作用域系统详解

### 三个作用域层级

**Personal（私有）：**
```
- 数据存储在当前目录
- 仅当前路径可见
- 切换目录后看不到
- 用途：项目特定的任务
```

**Group（组内）：**
```
- 需要先加入组（使用 group_add_path）
- 组内所有路径共享
- 跨仓库协作时使用
- 用途：团队任务、多仓库项目
```

**Global（全局）：**
```
- 仅 Memory 支持
- Todo 和 Plan 不支持 global 作用域
- 查询 todo_list 时使用 scope: "all" 可看到其他作用域
```

### 作用域选择指南

```javascript
// ✅ 使用 personal
todo_batch_create({
  items: [{ code: "todo-feature-x", ... }],
  scope: "personal"  // 项目特定任务
})

// ✅ 使用 group（需要先加入组）
todo_batch_create({
  items: [{ code: "todo-shared-task", ... }],
  scope: "group"     // 团队共享任务
})

// ✅ 查看所有
todo_list({
  scope: "all"       // 查看 personal + group + global
})
```

---

## 💡 混合模式结果处理

所有批量工具都返回混合模式结果，表示部分成功也是有效的。

### 处理混合模式结果的最佳实践

```javascript
// 方式 1：完全检查
const result = todo_batch_create({ items: [...] })

if (result.success) {
  console.log(`✅ 全部成功: ${result.success_count} 个`)
} else {
  console.log(`⚠️ 部分成功: ${result.success_count} 个成功，${result.fail_count} 个失败`)

  // 处理失败项
  if (result.failures && result.failures.length > 0) {
    result.failures.forEach(f => {
      console.error(`  - ${f.code}: ${f.error}`)
    })
  }
}

// 方式 2：简化处理（允许部分失败）
const result = todo_batch_create({ items: [...] })

if (result.success_count > 0) {
  console.log(`成功创建 ${result.success_count} 个任务`)
}

if (result.fail_count > 0) {
  // 重试或修正失败项
  const needsRetry = result.failures.filter(f =>
    f.error.includes('code 格式错误')
  )
  if (needsRetry.length > 0) {
    // 修正后重新提交
    console.log(`需要修正 ${needsRetry.length} 个任务的格式`)
  }
}

// 方式 3：JavaScript 处理示例
async function batchCreateWithRetry(items, maxRetries = 3) {
  let remaining = items
  let attempts = 0

  while (remaining.length > 0 && attempts < maxRetries) {
    const result = todo_batch_create({ items: remaining })

    if (result.success) break

    remaining = result.failures
      .filter(f => f.error.includes('code 格式错误'))
      .map(f => fixItemCode(items.find(i => i.code === f.code)))

    attempts++
  }

  return { success: attempts < maxRetries }
}
```

---

## 📚 快速参考表

| 工具 | 功能 | 输入 | 输出 | 混合模式 |
|------|------|------|------|---------|
| `todo_list` | 列出任务 | scope | 任务数组 | 否 |
| `todo_batch_create` | 批量创建 | items[] | success/failures | ✓ |
| `todo_batch_start` | 批量开始 | codes[] | success/failures | ✓ |
| `todo_batch_complete` | 批量完成 | codes[] | success/failures | ✓ |
| `todo_batch_update` | 批量更新 | items[] | success/failures | ✓ |
| `todo_final` | 清空全部 | scope | cleared_count | 否 |

---

呀~ 这就是 todo MCP 的所有工具啦！每个工具都很强大呢～(´∀`)💖
