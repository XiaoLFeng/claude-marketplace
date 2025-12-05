# Todo MCP 完整使用示例

嘿嘿~ 这是 todo-mcp 的真实场景示例集合！(´∀`)💖

---

## 示例 1: 紧急 Bug 修复流程

### 场景

用户反馈登录失败，需要紧急处理。

### 工作流程

#### 步骤 1: 创建紧急任务

```javascript
// 立即创建紧急任务
todo_batch_create({
  items: [{
    code: "todo-fix-login-failure",
    title: "修复登录失败 Bug",
    description: `## 问题描述
用户无法登录，错误提示"认证失败"，但密码正确。

## 紧急程度
🔴 阻塞用户正常使用

## 待排查
- 密码加密算法是否变更
- 数据库连接是否正常
- 认证中间件逻辑
- 最近代码变更记录`,
    priority: 4
  }],
  scope: "personal"
})
```

#### 步骤 2: 开始任务

```javascript
// 标记为进行中
todo_batch_start({
  codes: ["todo-fix-login-failure"]
})
```

#### 步骤 3: 记录排查过程

```javascript
// 排查发现问题
memory_create({
  code: "mem-login-bug-investigation",
  title: "登录 Bug 排查记录",
  content: `# 排查过程

## 时间线
- 10:00 收到用户反馈
- 10:15 确认问题可复现
- 10:30 定位到密码比对逻辑

## 根因
昨天的提交中，bcrypt.compare() 的参数顺序写反了。

## 修复方案
\`\`\`javascript
// ❌ 错误
await bcrypt.compare(hashedPassword, plainPassword)

// ✅ 正确
await bcrypt.compare(plainPassword, hashedPassword)
\`\`\`

## 影响范围
- 时间: 约 2 小时
- 用户: 约 50 人
- 风险: 无数据泄露`,
  category: "问题排查",
  tags: ["debug", "auth", "bug"],
  priority: 4,
  global: false
})
```

#### 步骤 4: 完成任务

```javascript
// 修复并部署后
todo_batch_complete({
  codes: ["todo-fix-login-failure"]
})
```

#### 步骤 5: 完整代码集合

```javascript
// JavaScript 完整流程
async function handleLoginBugFix() {
  // 1. 创建任务
  const createResult = todo_batch_create({
    items: [{
      code: "todo-fix-login-failure",
      title: "修复登录失败 Bug",
      priority: 4
    }],
    scope: "personal"
  })

  if (!createResult.success) {
    console.error("创建任务失败:", createResult.failures)
    return
  }

  console.log("✅ 任务已创建")

  // 2. 开始任务
  todo_batch_start({ codes: ["todo-fix-login-failure"] })
  console.log("▶️  任务已开始")

  // 模拟排查过程...
  await new Promise(r => setTimeout(r, 2000))

  // 3. 记录排查过程
  memory_create({
    code: "mem-login-bug-investigation",
    title: "登录 Bug 排查记录",
    content: "# 排查过程\n...",
    category: "问题排查",
    tags: ["debug", "auth"],
    priority: 4
  })
  console.log("📝 排查记录已保存")

  // 4. 完成任务
  todo_batch_complete({ codes: ["todo-fix-login-failure"] })
  console.log("✅ 任务已完成")

  return { success: true }
}

// 执行
handleLoginBugFix()
```

---

## 示例 2: 用户认证系统重构

### 场景

需要将 Session 认证迁移到 JWT 认证，涉及数据库、API、中间件等多个模块。

### 工作流程

#### 步骤 1: 创建重构计划

```javascript
plan_create({
  code: "plan-user-auth-refactor",
  title: "用户认证系统重构",
  description: "将 Session 认证迁移到 JWT 机制",
  content: `# 用户认证系统重构计划

## 目标
将 Session 认证迁移到 JWT 机制，提高系统可扩展性

## 阶段划分

### 阶段 1: 数据库设计 (Day 1-2)
- 设计 Token 存储表结构
- 创建 refresh_tokens 表
- 数据库迁移脚本

### 阶段 2: JWT 核心实现 (Day 3-4)
- 实现 Token 签发逻辑
- 实现 Token 验证逻辑
- 实现 Token 刷新机制

### 阶段 3: API 集成 (Day 5)
- 更新登录 API
- 更新注册 API
- 添加 Token 刷新 API

### 阶段 4: 中间件更新 (Day 6)
- 更新认证中间件
- 添加权限验证
- 兼容旧 Session

### 阶段 5: 测试与部署 (Day 7)
- 单元测试
- 集成测试
- 灰度发布
- 上线`,
  scope: "personal"
})
```

#### 步骤 2: 批量创建相关任务

```javascript
todo_batch_create({
  items: [
    {
      code: "todo-design-auth-db",
      title: "设计认证数据库架构",
      description: "设计 Token 存储表结构，支持 Refresh Token 机制",
      priority: 3
    },
    {
      code: "todo-impl-jwt-core",
      title: "实现 JWT 令牌机制",
      description: "实现 JWT 签发、验证、刷新的核心逻辑，这是其他任务的前置依赖",
      priority: 4  // 阻塞其他任务，必须先完成
    },
    {
      code: "todo-dev-auth-api",
      title: "开发登录和注册 API",
      description: "更新 /api/auth/login 和 /api/auth/register 接口，支持 JWT 流程",
      priority: 3
    },
    {
      code: "todo-add-auth-middleware",
      title: "添加认证中间件",
      description: "实现新的 JWT 验证中间件，支持 Bearer Token",
      priority: 2
    },
    {
      code: "todo-write-auth-tests",
      title: "编写认证单元测试",
      description: "补充完整的单元测试和集成测试，覆盖率目标 80%",
      priority: 2
    }
  ],
  scope: "personal"
})
```

#### 步骤 3: 记录架构决策

```javascript
memory_create({
  code: "mem-jwt-vs-session-decision",
  title: "JWT vs Session 选型分析",
  content: `# 技术选型分析

## 为什么选择 JWT

### 优点
1. **无状态**: 服务器无需存储会话信息
2. **可扩展**: 易于水平扩展和负载均衡
3. **跨域友好**: 适合多域名部署
4. **移动端友好**: 适合 RESTful API

### 缺点
1. **无法主动失效**: Token 在过期前无法撤销
2. **载荷较大**: 每次请求都需要完整 Token

## 解决方案

### 双 Token 机制
- **Access Token**: 15 分钟有效期
- **Refresh Token**: 7 天有效期

### 安全措施
1. Refresh Token 使用 HttpOnly Cookie
2. Access Token 存储在内存
3. 实现 Token 黑名单机制
4. 强制 HTTPS 传输

## 参考资料
- JWT Best Practices: https://auth0.com/blog/jwt-handbook/
- OWASP Cheat Sheet: https://cheatsheetseries.owasp.org/`,
  category: "架构决策",
  tags: ["auth", "jwt", "decision"],
  priority: 3,
  global: false
})
```

#### 步骤 4: 进度跟踪

```javascript
// 第 1 天：开始数据库设计
todo_batch_start({
  codes: ["todo-design-auth-db"]
})

// 第 2 天：完成数据库设计，开始 JWT 实现
todo_batch_complete({ codes: ["todo-design-auth-db"] })
todo_batch_start({ codes: ["todo-impl-jwt-core"] })

// 记录数据库设计结果
memory_create({
  code: "mem-auth-db-schema",
  title: "认证数据库表结构",
  content: `\`\`\`sql
CREATE TABLE refresh_tokens (
  id BIGSERIAL PRIMARY KEY,
  user_id BIGINT NOT NULL,
  token_hash VARCHAR(255) NOT NULL UNIQUE,
  device_info VARCHAR(255),
  ip_address VARCHAR(45),
  expires_at TIMESTAMP NOT NULL,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  revoked_at TIMESTAMP
);

CREATE INDEX idx_user_id ON refresh_tokens(user_id);
CREATE INDEX idx_token_hash ON refresh_tokens(token_hash);
\`\`\``,
  category: "数据库设计",
  tags: ["database", "auth"],
  priority: 3
})
```

#### 步骤 5: 完整代码集合

```javascript
// JavaScript 完整流程
async function executeAuthRefactor() {
  console.log("开始认证系统重构流程...\n")

  // 1. 创建计划
  console.log("1. 创建重构计划")
  plan_create({
    code: "plan-user-auth-refactor",
    title: "用户认证系统重构",
    description: "将 Session 认证迁移到 JWT",
    content: "# 重构计划\n...",
    scope: "personal"
  })

  // 2. 创建任务
  console.log("2. 创建任务列表")
  const createResult = todo_batch_create({
    items: [
      { code: "todo-design-auth-db", title: "设计数据库", priority: 3 },
      { code: "todo-impl-jwt-core", title: "实现 JWT", priority: 4 },
      { code: "todo-dev-auth-api", title: "开发 API", priority: 3 },
      { code: "todo-add-auth-middleware", title: "添加中间件", priority: 2 },
      { code: "todo-write-auth-tests", title: "编写测试", priority: 2 }
    ],
    scope: "personal"
  })

  console.log(`✅ 创建 ${createResult.success_count} 个任务\n`)

  // 3. 记录决策
  console.log("3. 记录架构决策")
  memory_create({
    code: "mem-jwt-vs-session-decision",
    title: "JWT vs Session 选型分析",
    content: "# 选型分析\n...",
    category: "架构决策",
    tags: ["auth", "jwt"],
    priority: 3
  })

  // 4. 启动任务
  console.log("4. 开始第一阶段（数据库设计）")
  todo_batch_start({ codes: ["todo-design-auth-db"] })

  // 5. 更新计划进度
  console.log("5. 更新计划进度")
  plan_update({
    code: "plan-user-auth-refactor",
    progress: 10
  })

  console.log("\n✅ 重构流程已启动！")
}

// 执行
executeAuthRefactor()
```

---

## 示例 3: 优先级重新评估

### 场景

每周一早上进行任务优先级评估，根据新的业务需求重新排序。

```javascript
// JavaScript 完整示例
function weeklyPriorityReview() {
  console.log("🔄 周一优先级评估\n")

  // 获取所有待处理任务
  const allTodos = todo_list({ scope: "all" })

  // 分类统计
  const stats = {
    pending: allTodos.filter(t => t.status === "pending").length,
    inProgress: allTodos.filter(t => t.status === "in_progress").length,
    byPriority: {
      4: allTodos.filter(t => t.priority === 4).length,
      3: allTodos.filter(t => t.priority === 3).length,
      2: allTodos.filter(t => t.priority === 2).length,
      1: allTodos.filter(t => t.priority === 1).length
    }
  }

  console.log("任务统计:")
  console.log(`  待处理: ${stats.pending}`)
  console.log(`  进行中: ${stats.inProgress}`)
  console.log(`  优先级分布: 🔴${stats.byPriority[4]} 🟠${stats.byPriority[3]} 🟡${stats.byPriority[2]} 🟢${stats.byPriority[1]}\n`)

  // 优先级调整规则
  const updates = []

  allTodos.forEach(todo => {
    // 规则 1: 已延期一周的低优先级 → 取消
    if (todo.due_date && todo.priority <= 2) {
      const daysSinceDue = (new Date() - new Date(todo.due_date)) / (1000*60*60*24)
      if (daysSinceDue > 7) {
        updates.push({
          code: todo.code,
          status: 3  // cancelled
        })
      }
    }

    // 规则 2: 关键模块的任务 → 升级优先级
    if (todo.code.includes("auth") && todo.priority < 4) {
      updates.push({
        code: todo.code,
        priority: 4
      })
    }

    // 规则 3: 完成度 > 80% 的任务 → 降低优先级
    if (todo.status === "in_progress" && todo.priority === 4) {
      updates.push({
        code: todo.code,
        priority: 3
      })
    }
  })

  if (updates.length > 0) {
    console.log(`进行优先级调整: ${updates.length} 个任务`)
    const result = todo_batch_update({ items: updates })
    console.log(`✅ 调整完成: 成功 ${result.success_count}, 失败 ${result.fail_count}\n`)
  } else {
    console.log("无需调整\n")
  }

  // 生成周报
  console.log("📊 周一任务摘要:")
  const highPriority = allTodos.filter(t => t.priority >= 3 && t.status !== "completed")
  highPriority.forEach(t => {
    console.log(`  - [${t.priority}] ${t.title}`)
  })
}

// 执行
weeklyPriorityReview()
```

---

## 示例 4: 批量创建功能模块任务

### 场景

启动新功能开发，需要一次性创建所有模块的任务。

```javascript
// JavaScript 模块化任务创建
function createFeatureTasks(featureName, modules) {
  // 生成任务列表
  const items = modules.flatMap(module => [
    {
      code: `todo-${featureName}-${module}-design`,
      title: `${module} 模块 - 设计`,
      description: `设计 ${module} 模块的接口和数据结构`,
      priority: 3
    },
    {
      code: `todo-${featureName}-${module}-impl`,
      title: `${module} 模块 - 实现`,
      description: `实现 ${module} 模块的核心逻辑`,
      priority: 3
    },
    {
      code: `todo-${featureName}-${module}-test`,
      title: `${module} 模块 - 测试`,
      description: `编写 ${module} 模块的单元测试`,
      priority: 2
    }
  ])

  console.log(`为功能 '${featureName}' 创建 ${items.length} 个任务`)

  const result = todo_batch_create({
    items,
    scope: "personal"
  })

  console.log(`✅ 成功创建 ${result.success_count} 个任务`)

  // 记录功能规划
  memory_create({
    code: `mem-${featureName}-plan`,
    title: `${featureName} 功能规划`,
    content: `# ${featureName} 功能规划

## 模块清单
${modules.map(m => `- ${m}`).join('\n')}

## 任务拆解
已自动生成 ${items.length} 个子任务，涵盖设计、实现、测试三个阶段`,
    category: "功能规划",
    tags: ["feature", "planning"],
    priority: 3
  })

  return result
}

// 使用
createFeatureTasks("user-profile", [
  "api",
  "database",
  "frontend",
  "validation"
])
```

---

## 示例 5: 日周月总结

### 场景

定期统计任务完成情况，生成工作总结。

```javascript
// JavaScript 完整总结生成
function generateProgressSummary(period = "week") {
  const allTodos = todo_list({ scope: "personal" })

  // 统计数据
  const completed = allTodos.filter(t => t.status === "completed").length
  const inProgress = allTodos.filter(t => t.status === "in_progress").length
  const pending = allTodos.filter(t => t.status === "pending").length
  const cancelled = allTodos.filter(t => t.status === "cancelled").length

  const summary = `# ${period === "week" ? "周" : "月"}度工作总结

## 任务完成情况
- 已完成: ${completed} 个 ✅
- 进行中: ${inProgress} 个 ▶️
- 待处理: ${pending} 个 ⏳
- 已取消: ${cancelled} 个 ❌

## 优先级分布（已完成）
${allTodos
  .filter(t => t.status === "completed")
  .reduce((acc, t) => {
    acc[t.priority] = (acc[t.priority] || 0) + 1
    return acc
  }, {})}

## 完成率
${(completed / (completed + inProgress + pending) * 100).toFixed(1)}%

生成时间: ${new Date().toISOString()}`

  // 记录总结
  memory_create({
    code: `mem-summary-${period}-${new Date().toISOString().split('T')[0]}`,
    title: `${period === "week" ? "周" : "月"}度工作总结`,
    content: summary,
    category: "工作总结",
    tags: ["summary", period],
    priority: 2
  })

  console.log(summary)
}

// 使用
generateProgressSummary("week")
generateProgressSummary("month")
```

---

## 快速参考

### 创建紧急任务

```javascript
todo_batch_create({
  items: [{
    code: "todo-urgent-xxx",
    title: "紧急任务",
    priority: 4
  }],
  scope: "personal"
})
```

### 一键启动所有待处理任务

```javascript
const todos = todo_list({ scope: "personal" })
const codes = todos.filter(t => t.status === "pending").map(t => t.code)
if (codes.length > 0) {
  todo_batch_start({ codes })
}
```

### 周五清理

```javascript
const todos = todo_list({ scope: "personal" })
const lowPriority = todos
  .filter(t => t.priority === 1 && t.status === "pending")
  .map(t => t.code)

if (lowPriority.length > 0) {
  todo_batch_complete({ codes: lowPriority })
}
```

---

呀~ 这些都是真实场景的使用示例呢！希望能帮到你～(´∀`)💖
