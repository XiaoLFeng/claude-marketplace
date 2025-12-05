# Todo 批量操作完整指南 (MCP 版本)

嘿嘿~ 这是 todo-mcp 的批量操作深度指南！处理大量任务就用批量工具吧～(´∀`)💖

## 概述

批量操作是 MCP 版本的核心优势。一次调用可以处理多个任务，支持混合模式返回（部分成功也有效），效率远高于逐个操作。

---

## 📊 批量工具全览

所有批量工具支持最多 **100 个** 项目的单次操作。

| 工具 | 操作 | 用途 | 返回 |
|------|------|------|------|
| `todo_batch_create` | 创建 | 一次创建多个任务 | 混合模式 |
| `todo_batch_start` | 开始 | 批量标记为进行中 | 混合模式 |
| `todo_batch_complete` | 完成 | 批量标记为完成 | 混合模式 |
| `todo_batch_update` | 更新 | 批量更新属性/状态 | 混合模式 |
| `todo_final` | 清空 | 一键删除所有任务 | 标准结果 |

---

## 1️⃣ 批量创建 `todo_batch_create`

### 基础用法

```javascript
todo_batch_create({
  items: [
    {
      code: "todo-design-db",
      title: "设计数据库架构",
      description: "设计 Token 存储表结构",
      priority: 3
    },
    {
      code: "todo-impl-jwt",
      title: "实现 JWT 机制",
      description: "实现签发、验证、刷新逻辑",
      priority: 4
    },
    {
      code: "todo-write-tests",
      title: "编写单元测试",
      priority: 2
    }
  ],
  scope: "personal"
})
```

### 返回值处理

```javascript
const result = todo_batch_create({ items: [...] })

// 全部成功的情况
if (result.success_count === items.length) {
  console.log(`✅ 成功创建 ${result.success_count} 个任务`)
  return result.items  // 返回所有创建的任务
}

// 部分成功的情况
if (result.fail_count > 0) {
  console.warn(`⚠️ 部分失败: 成功 ${result.success_count} 个，失败 ${result.fail_count} 个`)

  // 分析失败原因
  const formatErrors = result.failures.filter(f =>
    f.error.includes('code 格式错误')
  )
  const duplicateErrors = result.failures.filter(f =>
    f.error.includes('已存在')
  )

  console.log(`格式错误: ${formatErrors.length} 个`)
  console.log(`重复: ${duplicateErrors.length} 个`)
}
```

### 错误处理策略

**JavaScript 完整处理示例：**

```javascript
async function createTasksWithErrorHandling(items) {
  const result = todo_batch_create({ items })

  // 统计结果
  console.log(`创建任务: ${result.success_count}/${items.length} 成功`)

  // 分类处理失败
  const failuresByType = {}
  result.failures.forEach(f => {
    const type = f.error.split(':')[0]
    if (!failuresByType[type]) {
      failuresByType[type] = []
    }
    failuresByType[type].push(f)
  })

  // 针对不同错误类型的处理
  for (const [type, failures] of Object.entries(failuresByType)) {
    switch (type) {
      case 'code 格式错误':
        console.log(`修正 ${failures.length} 个 code 格式`)
        const corrected = failures.map(f => {
          const item = items.find(i => i.code === f.code)
          return {
            ...item,
            code: item.code.toLowerCase().replace(/_/g, '-')
          }
        })
        // 重新提交修正后的项
        return createTasksWithErrorHandling(corrected)

      case '活跃状态中已存在':
        console.log(`${failures.length} 个 code 已存在，跳过`)
        return result  // 返回已有结果，不重试

      default:
        console.error(`未知错误类型: ${type}`)
    }
  }

  return result
}

// 使用
const items = [
  { code: "todo-1", title: "任务 1", priority: 2 },
  { code: "todo-2", title: "任务 2", priority: 3 }
]
createTasksWithErrorHandling(items)
```

---

## 2️⃣ 批量开始 `todo_batch_start`

### 基础用法

```javascript
// 开始指定任务
todo_batch_start({
  codes: ["todo-design-db", "todo-impl-jwt"]
})

// 开始所有待处理的高优先级任务
const allTodos = todo_list({ scope: "personal" })
const highPriorityPending = allTodos
  .filter(t => t.status === "pending" && t.priority >= 3)
  .map(t => t.code)

if (highPriorityPending.length > 0) {
  todo_batch_start({ codes: highPriorityPending })
}
```

### 混合模式处理

```javascript
function startTasksWithLogging(codes) {
  const result = todo_batch_start({ codes })

  console.log(`开始任务: ${result.success_count}/${codes.length}`)

  if (result.fail_count > 0) {
    console.warn('以下任务无法开始:')
    result.failures.forEach(f => {
      console.log(`  - ${f.code}: ${f.error}`)
    })

    // 返回成功和失败的分开列表
    return {
      started: result.items.map(i => i.code),
      failed: result.failures.map(f => f.code),
      total: codes.length
    }
  }

  return { started: result.items.map(i => i.code) }
}

// 使用
const status = startTasksWithLogging(["todo-1", "todo-2", "todo-3"])
console.log(`已开始: ${status.started.join(', ')}`)
```

---

## 3️⃣ 批量完成 `todo_batch_complete`

### 基础用法

```javascript
// 完成指定任务
todo_batch_complete({
  codes: ["todo-design-db", "todo-impl-jwt"]
})

// 完成所有高优先级的进行中任务
const allTodos = todo_list({ scope: "all" })
const highPriorityInProgress = allTodos
  .filter(t => t.status === "in_progress" && t.priority === 4)
  .map(t => t.code)

todo_batch_complete({ codes: highPriorityInProgress })
```

### 自动后处理

```javascript
function completeTasksAndRecordMemory(codes) {
  // 获取任务详情
  const allTodos = todo_list({ scope: "personal" })
  const tasksToComplete = allTodos.filter(t => codes.includes(t.code))

  // 完成任务
  const result = todo_batch_complete({ codes })

  // 如果是重要任务，记录完成信息
  if (result.success_count > 0) {
    const importantTasks = tasksToComplete.filter(t => t.priority >= 3)

    importantTasks.forEach(task => {
      memory_create({
        code: `mem-completed-${task.code}`,
        title: `任务完成: ${task.title}`,
        content: `## 任务: ${task.title}\n\n已于 ${new Date().toISOString()} 完成`,
        category: "任务记录",
        tags: ["completed"],
        priority: 2,
        global: false
      })
    })
  }

  return result
}
```

---

## 4️⃣ 批量更新 `todo_batch_update`

### 更新场景 1：批量升级优先级

```javascript
// 场景：发现某个模块有多个任务需要升级优先级
function upgradePriorityByModule(modulePrefix) {
  const allTodos = todo_list({ scope: "personal" })
  const moduleTasks = allTodos.filter(t => t.code.startsWith(modulePrefix))

  const updates = moduleTasks.map(t => ({
    code: t.code,
    priority: Math.min(4, t.priority + 1)  // 优先级 +1，最高 4
  }))

  return todo_batch_update({ items: updates })
}

// 使用
upgradePriorityByModule("todo-auth-")  // 升级所有认证相关任务
```

### 更新场景 2：批量更改状态

```javascript
// JavaScript 完整示例：根据条件批量更新状态
function updateTasksConditionally() {
  const allTodos = todo_list({ scope: "personal" })

  const updates = []

  allTodos.forEach(todo => {
    // 条件 1：pending 的低优先级任务 → 取消
    if (todo.status === "pending" && todo.priority === 1) {
      updates.push({
        code: todo.code,
        status: 3  // cancelled
      })
    }

    // 条件 2：in_progress 且优先级为 4 的 → 标记高优先级
    if (todo.status === "in_progress" && todo.priority === 4) {
      updates.push({
        code: todo.code,
        description: `[🔴 紧急] ${todo.title} - 正在处理中`,
        priority: 4
      })
    }

    // 条件 3：pending 超过 7 天的 → 标记为已取消
    if (todo.status === "pending" && todo.due_date) {
      const dueDate = new Date(todo.due_date)
      const now = new Date()
      const daysDiff = (now - dueDate) / (1000 * 60 * 60 * 24)

      if (daysDiff > 7) {
        updates.push({
          code: todo.code,
          status: 3  // cancelled
        })
      }
    }
  })

  if (updates.length > 0) {
    return todo_batch_update({ items: updates })
  }

  return { success: true, message: "无需更新" }
}

// 使用
updateTasksConditionally()
```

### 更新场景 3：批量更新描述和优先级

```javascript
// JavaScript 场景：修复文档错误
function updateTaskDescriptions(updates) {
  const items = updates.map(u => ({
    code: u.code,
    description: u.newDescription,
    priority: u.newPriority
  }))

  const result = todo_batch_update({ items })

  // 记录更新
  if (result.success_count > 0) {
    console.log(`✅ 更新了 ${result.success_count} 个任务的描述`)
  }

  return result
}

// 使用
updateTaskDescriptions([
  {
    code: "todo-fix-bug",
    newDescription: "修复登录验证中的参数顺序问题\n\n参考: mem-login-bug-investigation",
    newPriority: 4
  }
])
```

---

## 5️⃣ 清空所有 `todo_final`

### 基础用法

```javascript
// 清空私有任务（⚠️ 谨慎操作）
todo_final({ scope: "personal" })

// 清空组内任务（⚠️ 谨慎操作）
todo_final({ scope: "group" })
```

### 安全的清空流程

```javascript
// JavaScript: 带确认的安全清空
async function safelyFinalTodos(scope = "personal") {
  // 步骤 1：列出将被清空的任务
  const todos = todo_list({ scope })

  if (todos.length === 0) {
    console.log("无任务需要清空")
    return { cleared: 0 }
  }

  // 步骤 2：显示统计信息
  console.log(`\n将清空 ${todos.length} 个任务:`)

  const statsByStatus = {}
  todos.forEach(t => {
    statsByStatus[t.status] = (statsByStatus[t.status] || 0) + 1
  })

  Object.entries(statsByStatus).forEach(([status, count]) => {
    console.log(`  - ${status}: ${count} 个`)
  })

  // 步骤 3：归档重要任务的信息
  const importantTodos = todos.filter(t => t.priority >= 3)

  if (importantTodos.length > 0) {
    console.log(`\n归档 ${importantTodos.length} 个重要任务:`)

    importantTodos.forEach(t => {
      memory_create({
        code: `mem-archived-${t.code}`,
        title: `已清空: ${t.title}`,
        content: `## 任务\n${t.title}\n\n清空时间: ${new Date().toISOString()}`,
        category: "归档",
        tags: ["archived"],
        global: false
      })

      console.log(`  ✓ 已归档: ${t.code}`)
    })
  }

  // 步骤 4：执行清空
  console.log("\n执行清空...")
  const result = todo_final({ scope })

  console.log(`✅ 已清空 ${result.cleared_count} 个任务`)
  return result
}

// 使用
safelyFinalTodos("personal")
```

---

## 🎯 分批处理大量数据

### 场景：一次性创建 500 个任务

```javascript
// JavaScript: 自动分批处理
function batchCreateLargeSet(allItems, batchSize = 100) {
  const batches = []

  // 分批
  for (let i = 0; i < allItems.length; i += batchSize) {
    batches.push(allItems.slice(i, i + batchSize))
  }

  console.log(`将 ${allItems.length} 个任务分为 ${batches.length} 批处理`)

  // 逐批提交
  const results = []

  batches.forEach((batch, index) => {
    console.log(`\n处理第 ${index + 1}/${batches.length} 批...`)

    const result = todo_batch_create({
      items: batch,
      scope: "personal"
    })

    console.log(`  成功: ${result.success_count}, 失败: ${result.fail_count}`)
    results.push(result)

    // 如果有失败，记录下来
    if (result.fail_count > 0) {
      const failedCodes = result.failures.map(f => f.code)
      console.warn(`  失败的任务: ${failedCodes.join(', ')}`)
    }
  })

  // 汇总
  const totalSuccess = results.reduce((sum, r) => sum + r.success_count, 0)
  const totalFail = results.reduce((sum, r) => sum + r.fail_count, 0)

  console.log(`\n📊 汇总: 成功 ${totalSuccess}，失败 ${totalFail}`)

  return results
}

// 使用
const items = Array.from({ length: 500 }, (_, i) => ({
  code: `todo-task-${String(i+1).padStart(4, '0')}`,
  title: `任务 ${i+1}`,
  priority: Math.floor(Math.random() * 4) + 1
}))

batchCreateLargeSet(items)
```

---

## 🔗 混合模式最佳实践

### 处理模式

```javascript
// 模式 1：严格模式（全部成功或全部失败）
function strictBatchCreate(items) {
  const result = todo_batch_create({ items })

  if (!result.success) {
    throw new Error(`批量创建失败: ${result.message}`)
  }

  return result.items
}

// 模式 2：宽松模式（允许部分失败）
function tolerantBatchCreate(items) {
  const result = todo_batch_create({ items })

  // 即使有失败也继续
  const created = result.items || []
  const failed = result.failures || []

  console.log(`创建成功: ${created.length}, 失败: ${failed.length}`)

  return { created, failed }
}

// 模式 3：重试模式（自动重试失败）
async function retryableBatchCreate(items, maxRetries = 3) {
  let remaining = items
  let attempt = 0

  while (remaining.length > 0 && attempt < maxRetries) {
    const result = todo_batch_create({ items: remaining })

    if (result.success_count === remaining.length) {
      return { success: true, totalCreated: items.length }
    }

    // 提取可重试的失败项
    remaining = result.failures
      .filter(f => f.error.includes('temporary') || f.error.includes('timeout'))
      .map(f => items.find(i => i.code === f.code))
      .filter(Boolean)

    attempt++

    if (remaining.length > 0) {
      console.log(`第 ${attempt} 次重试，${remaining.length} 个任务...`)
      await new Promise(r => setTimeout(r, 1000))  // 等待 1 秒
    }
  }

  return { success: false, totalAttempts: attempt }
}

// 使用
retryableBatchCreate([...items])
```

---

## 💡 实战场景

### 场景 1：项目启动时批量创建任务

```javascript
function initializeProjectTasks(projectName) {
  const tasks = [
    { code: "todo-project-kickoff", title: "项目启动会议", priority: 4 },
    { code: "todo-setup-dev-env", title: "搭建开发环境", priority: 3 },
    { code: "todo-design-arch", title: "架构设计评审", priority: 3 },
    { code: "todo-setup-ci", title: "配置 CI/CD", priority: 2 },
    { code: "todo-write-docs", title: "编写项目文档", priority: 2 }
  ]

  const result = todo_batch_create({ items: tasks, scope: "personal" })

  console.log(`✅ 项目 ${projectName} 初始化完成`)
  console.log(`创建任务: ${result.success_count}/${tasks.length}`)

  return result
}
```

### 场景 2：日报中批量更新任务状态

```javascript
function dailyStatusUpdate() {
  const today = new Date().toISOString().split('T')[0]

  const allTodos = todo_list({ scope: "personal" })

  // 找出今天的任务
  const todayTasks = allTodos.filter(t =>
    t.title.includes(today) || t.due_date?.startsWith(today)
  )

  // 批量标记为进行中
  const toStart = todayTasks.filter(t => t.status === "pending")
  if (toStart.length > 0) {
    todo_batch_start({ codes: toStart.map(t => t.code) })
  }

  console.log(`📅 日报更新: ${toStart.length} 个任务开始`)
}
```

### 场景 3：周五清理任务

```javascript
function fridayCleanup() {
  const allTodos = todo_list({ scope: "personal" })

  const updates = []

  // 取消超期的低优先级任务
  allTodos.forEach(todo => {
    const daysSince = (new Date() - new Date(todo.due_date)) / (1000*60*60*24)

    if (daysSince > 14 && todo.priority <= 2 && todo.status === "pending") {
      updates.push({
        code: todo.code,
        status: 3  // cancelled
      })
    }
  })

  if (updates.length > 0) {
    const result = todo_batch_update({ items: updates })
    console.log(`🧹 清理完成: 取消 ${result.success_count} 个过期任务`)
  }
}
```

---

## 📚 快速参考

### 创建 50 个任务

```javascript
const items = Array.from({ length: 50 }, (_, i) => ({
  code: `todo-item-${i+1}`,
  title: `任务 ${i+1}`,
  priority: 2
}))

todo_batch_create({ items, scope: "personal" })
```

### 完成所有高优先级任务

```javascript
const todos = todo_list({ scope: "personal" })
const highPriority = todos
  .filter(t => t.priority >= 3 && t.status !== "completed")
  .map(t => t.code)

if (highPriority.length > 0) {
  todo_batch_complete({ codes: highPriority })
}
```

### 升级所有紧急任务的优先级

```javascript
const todos = todo_list({ scope: "all" })
const urgent = todos
  .filter(t => t.priority === 4)
  .map(t => ({
    code: t.code,
    title: `🔴 ${t.title}`
  }))

if (urgent.length > 0) {
  todo_batch_update({ items: urgent })
}
```

---

呀~ 批量操作真的很强大呢！掌握这些技巧可以大大提高效率～(´∀`)💖
