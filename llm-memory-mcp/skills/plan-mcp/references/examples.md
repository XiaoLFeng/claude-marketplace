# Plan MCP 使用示例

本文档提供真实场景的 Plan MCP 使用示例，展示如何使用 MCP 工具调用方式（而非 CLI 命令）来管理复杂的多步骤项目。

---

## 示例 1: 用户认证系统重构

### 场景描述

重构现有的用户认证系统，采用 JWT 机制，支持 refresh token。

**项目特点：**
- 复杂度：高
- 预计时长：1 周
- 涉及模块：数据库、后端 API、中间件、测试
- 依赖关系：有明确的任务依赖

### Step 1: 创建计划

```javascript
// 创建认证系统重构计划
const plan = plan_create({
  code: "plan-auth-refactor",
  title: "用户认证系统重构",
  description: "采用 JWT 机制，支持 refresh token，提升安全性",
  content: `# 用户认证系统重构实施计划

## 阶段 1: 数据库设计 (Day 1-2)
- 设计 users 表结构
- 设计 refresh_tokens 表结构
- 添加必要的索引和约束
- 编写数据库迁移脚本

## 阶段 2: JWT 核心实现 (Day 2-3)
- 实现 JWT 生成逻辑
- 实现 JWT 验证逻辑
- 实现 refresh token 机制
- 配置过期时间和密钥管理

## 阶段 3: API 端点开发 (Day 3-4)
- POST /api/auth/register - 用户注册
- POST /api/auth/login - 用户登录
- POST /api/auth/refresh - 刷新令牌
- POST /api/auth/logout - 登出

## 阶段 4: 中间件和安全 (Day 4-5)
- 实现 JWT 验证中间件
- 添加到受保护路由
- 实现登录失败限流
- CSRF 保护

## 阶段 5: 测试和验证 (Day 5-7)
- 单元测试（覆盖率 > 80%）
- 集成测试
- 安全测试
- 性能测试`,
  scope: "personal"
})

console.log(`计划创建成功: ${plan.data.code}`)
```

### Step 2: 创建关联的待办任务

虽然本例重点是 Plan，但通常会创建相关的 Todo 来跟踪具体任务：

```javascript
// 创建对应的 Todo 项（与 todo-mcp skill 配合使用）
// 这里展示结构，实际调用需要使用 todo_create
const todos = [
  {
    code: "todo-design-auth-schema",
    title: "设计认证数据库架构",
    description: "设计 users、sessions、tokens 等表结构，支持 JWT 和 refresh token",
    priority: 3
  },
  {
    code: "todo-implement-jwt",
    title: "实现 JWT 令牌机制",
    description: "实现 JWT 生成、验证、刷新逻辑",
    priority: 4
  },
  {
    code: "todo-auth-api-endpoints",
    title: "开发登录和注册 API",
    description: "POST /login, POST /register, POST /refresh 等端点",
    priority: 3
  },
  {
    code: "todo-auth-middleware",
    title: "添加认证中间件",
    description: "实现 JWT 验证中间件，保护受限路由",
    priority: 2
  },
  {
    code: "todo-auth-unit-tests",
    title: "编写认证单元测试",
    description: "测试覆盖率达到 80% 以上",
    priority: 2
  }
]
```

### Step 3: 记录架构设计决策

创建 Memory 来记录关键的架构决策（与 memory-mcp skill 配合使用）：

```javascript
// 记录认证系统架构决策
const architecture = {
  code: "mem-auth-system-design",
  title: "用户认证系统设计决策",
  category: "架构设计",
  tags: ["认证", "JWT", "安全", "数据库"],
  content: `# 用户认证系统设计决策

## 技术选型

### 选择 JWT 的原因
1. **无状态**：不需要服务器端 session 存储
2. **易扩展**：支持水平扩展和微服务架构
3. **跨域支持**：天然支持跨域认证
4. **标准化**：行业标准，库支持完善

### Refresh Token 机制
- **Access Token**: 短期（15分钟），存储少量信息
- **Refresh Token**: 长期（7天），用于刷新 access token
- **安全性**: refresh token 存储在 httpOnly cookie，防止 XSS

## 数据库设计

### users 表
\`\`\`sql
CREATE TABLE users (
  id INT PRIMARY KEY AUTO_INCREMENT,
  username VARCHAR(255) UNIQUE NOT NULL,
  email VARCHAR(255) UNIQUE NOT NULL,
  password_hash VARCHAR(255) NOT NULL,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP
);
\`\`\`

### refresh_tokens 表
\`\`\`sql
CREATE TABLE refresh_tokens (
  id INT PRIMARY KEY AUTO_INCREMENT,
  user_id INT NOT NULL,
  token_hash VARCHAR(255) NOT NULL,
  expires_at TIMESTAMP NOT NULL,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  FOREIGN KEY (user_id) REFERENCES users(id)
);
\`\`\`

## 安全考虑
1. 密码使用 bcrypt 哈希（cost=12）
2. 防止暴力破解：登录失败限流（5次失败后 15 分钟内禁止）
3. HTTPS only
4. CSRF 保护（双重 cookie 提交）
5. Refresh token 存储在 httpOnly、secure、sameSite 的 cookie

## 实现参考
- JWT 库：jsonwebtoken (Node.js)
- 密码哈希：bcryptjs
- 参考资料：[JWT Best Practices](https://jwt.io/introduction/)`
}
```

### Step 4: 执行阶段 1（数据库设计）

```javascript
// Day 1: 开始执行，更新进度到 1%
plan_update({
  code: "plan-auth-refactor",
  progress: 1
})

// Day 2: 完成数据库设计，更新到 20%
plan_update({
  code: "plan-auth-refactor",
  progress: 20
})

// 同时更新对应的 Todo 为完成
// todo_complete({ code: "todo-design-auth-schema" })
```

### Step 5: 执行阶段 2（JWT 核心实现）

```javascript
// Day 2.5: 开始 JWT 实现
// todo_start({ code: "todo-implement-jwt" })

// Day 3: JWT 实现完成，更新进度到 40%
plan_update({
  code: "plan-auth-refactor",
  progress: 40
})

// 完成相应的 Todo
// todo_complete({ code: "todo-implement-jwt" })
```

### Step 6: 执行阶段 3 和 4

```javascript
// Day 4: API 端点完成，更新到 60%
plan_update({
  code: "plan-auth-refactor",
  progress: 60
})

// Day 5: 中间件完成，更新到 80%
plan_update({
  code: "plan-auth-refactor",
  progress: 80
})
```

### Step 7: 项目完成

```javascript
// Day 7: 所有测试完成，标记为完成
plan_update({
  code: "plan-auth-refactor",
  progress: 100
})

// 查看最终的计划状态
const finalPlan = plan_get({ code: "plan-auth-refactor" })
console.log(`项目完成！最终状态: ${finalPlan.data.status}`)
```

### 中途检查计划进度

```javascript
// 随时查看当前进度
function checkProgress() {
  const plan = plan_get({ code: "plan-auth-refactor" })
  return {
    progress: `${plan.data.progress}%`,
    status: plan.data.status,
    updated: plan.data.updated_at
  }
}

// 输出：{ progress: '60%', status: 'in_progress', updated: '2025-12-04T14:30:00Z' }
```

---

## 示例 2: Plan + Todo + Memory 协作工作流

### 场景: 数据库性能优化项目

这个例子展示如何将 Plan、Todo 和 Memory 三者结合使用。

### Step 1: 创建计划

```javascript
// 创建性能优化计划
plan_create({
  code: "plan-db-optimization",
  title: "数据库性能优化项目",
  description: "优化查询性能，降低响应时间到 100ms 以内",
  content: `# 数据库性能优化项目计划

## 阶段 1: 基准测试和分析 (Week 1)
- 建立性能基准
- 识别瓶颈查询
- 分析执行计划
- 生成报告

## 阶段 2: 索引优化 (Week 2)
- 分析缺失的索引
- 创建复合索引
- 清理未使用的索引
- 验证性能改进

## 阶段 3: 查询优化 (Week 2-3)
- 重写低效查询
- 优化 JOIN 操作
- 实现查询缓存
- 测试性能

## 阶段 4: 架构优化 (Week 3-4)
- 实现分表分库
- 添加读副本
- 配置连接池
- 压力测试

## 阶段 5: 上线和监控 (Week 4-5)
- 灰度发布
- 实时监控
- 性能验证
- 文档更新`,
  scope: "personal"
})
```

### Step 2: 创建相关的记忆记录

```javascript
// 记录性能优化技术知识
plan_create({
  code: "mem-db-optimization-techniques",
  title: "数据库性能优化技术",
  category: "技术文档",
  tags: ["数据库", "性能", "优化"],
  content: `# 数据库性能优化技术

## 常见问题和解决方案

### 问题 1: N+1 查询
**现象**：加载 1000 条记录需要 1001 次查询
**原因**：每条记录都需要额外的子查询
**解决**：
- 使用 JOIN 合并查询
- 使用 UNION 合并结果
- 实现批量查询

### 问题 2: 全表扫描
**现象**：某个查询需要扫描整个表
**原因**：缺少合适的索引
**解决**：
- 分析执行计划
- 创建合适的索引
- 优化查询条件

## 索引最佳实践
1. 选择性 > 0.1 的字段才值得建索引
2. 复合索引遵循最左匹配原则
3. 定期清理无用索引
4. 监控索引大小和维护成本

## 查询优化清单
- [ ] 检查执行计划
- [ ] 确认索引使用
- [ ] 优化 JOIN 顺序
- [ ] 添加必要的过滤条件
- [ ] 考虑使用缓存`,
})

// 记录性能优化工具
plan_create({
  code: "mem-optimization-tools",
  title: "性能优化工具和命令",
  category: "工具参考",
  tags: ["MySQL", "工具", "性能分析"],
  content: `# 性能优化常用工具和命令

## MySQL 诊断命令

\`\`\`sql
-- 查看执行计划
EXPLAIN SELECT * FROM users WHERE age > 30;

-- 查看索引
SHOW INDEXES FROM users;

-- 查看表大小
SELECT table_name, ROUND(data_free/1024/1024) as free_mb
FROM information_schema.tables
WHERE table_schema = 'mydb';

-- 查看慢查询
SELECT * FROM mysql.slow_log;
\`\`\`

## 性能监控工具
- **Percona Monitoring and Management (PMM)**：专业的 MySQL 监控
- **MySQL Workbench**：官方 IDE 和管理工具
- **SkyWalking**：分布式追踪和分析
- **Prometheus + Grafana**：指标收集和可视化
`
})
```

### Step 3: 创建相关的待办任务

```javascript
// 待办任务示例结构（实际调用 todo_create）
const todoTasks = [
  {
    code: "todo-baseline-testing",
    title: "建立性能基准",
    description: "运行 sysbench 进行基准测试，记录当前性能指标",
    priority: 3,
    dependencies: []
  },
  {
    code: "todo-analyze-slow-queries",
    title: "分析慢查询",
    description: "启用慢查询日志，分析执行时间超过 1 秒的查询",
    priority: 4,
    dependencies: ["todo-baseline-testing"]
  },
  {
    code: "todo-create-indexes",
    title: "创建优化索引",
    description: "根据分析结果创建复合索引，预期提升 5-10 倍性能",
    priority: 3,
    dependencies: ["todo-analyze-slow-queries"]
  },
  {
    code: "todo-rewrite-queries",
    title: "重写低效查询",
    description: "优化 10 个最常用的查询，使用 JOIN 和缓存替代 N+1",
    priority: 3,
    dependencies: ["todo-create-indexes"]
  }
]
```

### Step 4: 执行和追踪进度

```javascript
// Week 1: 基准测试和分析完成
plan_update({
  code: "plan-db-optimization",
  progress: 20,
  content: `# 数据库性能优化项目计划

## 完成情况更新

### ✅ 阶段 1: 基准测试和分析 (Week 1) - 完成
- 基准测试结果：平均查询时间 500ms
- 发现 15 个性能瓶颈
- 其中 3 个是 N+1 查询问题
- 详见 mem-db-optimization-analysis

## 继续执行阶段...`
})

// Week 2: 索引优化完成
plan_update({
  code: "plan-db-optimization",
  progress: 45
})

// Week 3: 查询优化完成
plan_update({
  code: "plan-db-optimization",
  progress: 70
})

// Week 4: 架构优化完成
plan_update({
  code: "plan-db-optimization",
  progress: 85
})

// Week 5: 项目完成
plan_update({
  code: "plan-db-optimization",
  progress: 100
})
```

### 查看项目摘要

```javascript
// 获取完整计划详情
function getProjectSummary(code) {
  const plan = plan_get({ code })

  return {
    title: plan.data.title,
    description: plan.data.description,
    progress: `${plan.data.progress}%`,
    status: plan.data.status,
    created: plan.data.created_at,
    updated: plan.data.updated_at,
    scope: plan.data.scope
  }
}

const summary = getProjectSummary("plan-db-optimization")
// 输出完整的项目摘要
```

---

## 示例 3: 并行执行多个计划

### 场景: 管理多个并行项目

```javascript
// 创建多个计划（不同的功能模块）
const plans = [
  {
    code: "plan-payment-module",
    title: "支付模块重构",
    description: "支持多种支付方式"
  },
  {
    code: "plan-notification-system",
    title: "通知系统设计",
    description: "实时消息推送"
  },
  {
    code: "plan-analytics-dashboard",
    title: "分析仪表板",
    description: "用户行为分析"
  }
]

// 创建所有计划
plans.forEach(p => {
  plan_create({
    code: p.code,
    title: p.title,
    description: p.description,
    content: `# ${p.title}\n\n## 实施步骤\n...`,
    scope: "personal"
  })
})

// 查看所有进行中的计划
const activeProjects = plan_list({
  scope: "personal",
  status: "in_progress"
})

// 按进度排序
activeProjects.data.sort((a, b) => a.progress - b.progress)

// 显示项目看板
activeProjects.data.forEach(plan => {
  const bar = "█".repeat(Math.floor(plan.progress / 5)) +
              "░".repeat(20 - Math.floor(plan.progress / 5))
  console.log(`${plan.code.padEnd(25)} [${bar}] ${plan.progress}%`)
})
```

---

## 示例 4: 错误场景处理

### 场景: 处理常见的错误

```javascript
// 错误 1: Code 格式错误
try {
  plan_create({
    code: "Plan_001",  // ❌ 包含大写和下划线
    title: "测试计划",
    description: "描述",
    content: "内容"
  })
} catch (error) {
  console.error("Code 格式错误，应使用全小写和连字符")
  // 正确做法
  plan_create({
    code: "plan-test",  // ✅ 全小写，使用连字符
    title: "测试计划",
    description: "描述",
    content: "内容"
  })
}

// 错误 2: 计划不存在
const notFound = plan_get({ code: "plan-nonexistent" })
if (!notFound.success) {
  console.error(`未找到计划: ${notFound.error}`)
  // 列出所有计划找相似的
  const allPlans = plan_list({ scope: "all" })
  console.log("可用的计划:", allPlans.data.map(p => p.code))
}

// 错误 3: 无效的进度值
try {
  plan_update({
    code: "plan-test",
    progress: 150  // ❌ 超过 100
  })
} catch (error) {
  console.error("进度值必须在 0-100 之间")
}

// 错误 4: 尝试更新已完成的计划
const completed = plan_get({ code: "plan-test" })
if (completed.data.status === "completed") {
  console.error("无法更新已完成的计划")
  // 如果需要继续工作，创建一个新的计划
}
```

---

## 完整工作流程示例（JavaScript）

```javascript
// ============ 认证系统重构完整工作流 ============

// 1. 初始化：创建计划
console.log("1️⃣ 创建计划...")
const authPlan = plan_create({
  code: "plan-auth-refactor",
  title: "用户认证系统重构",
  description: "采用 JWT 机制，支持 refresh token",
  content: "# 实施计划\n\n## 阶段 1-5\n...",
  scope: "personal"
})
console.log(`✅ 计划创建: ${authPlan.data.code}`)

// 2. 启动项目
console.log("\n2️⃣ 启动项目...")
plan_update({ code: "plan-auth-refactor", progress: 1 })
console.log("✅ 项目已启动")

// 3. 执行各个阶段
const phases = [
  { day: "Day 1-2", progress: 20, phase: "数据库设计" },
  { day: "Day 3", progress: 40, phase: "JWT 实现" },
  { day: "Day 4", progress: 60, phase: "API 开发" },
  { day: "Day 5", progress: 80, phase: "中间件" },
  { day: "Day 6-7", progress: 100, phase: "测试和完成" }
]

phases.forEach(p => {
  console.log(`\n${p.day}: ${p.phase}...`)
  plan_update({
    code: "plan-auth-refactor",
    progress: p.progress
  })

  const plan = plan_get({ code: "plan-auth-refactor" })
  console.log(`✅ 进度: ${plan.data.progress}% | 状态: ${plan.data.status}`)
})

// 4. 项目完成
console.log("\n3️⃣ 项目完成总结...")
const finalPlan = plan_get({ code: "plan-auth-refactor" })
console.log(`
🎉 项目完成!
- Title: ${finalPlan.data.title}
- Status: ${finalPlan.data.status}
- Progress: ${finalPlan.data.progress}%
- Created: ${finalPlan.data.created_at}
- Completed: ${finalPlan.data.updated_at}
`)
```

---

## 最佳实践总结

### ✅ 推荐做法

- 使用语义化的 code：`plan-auth-refactor`
- 内容使用 Markdown 格式化
- 定期更新进度（推荐每天）
- 在 Memory 中记录关键决策
- 使用 Todo 管理具体任务
- 查看计划时附带相关的 Memory

### ❌ 避免做法

- Code 使用大写或下划线
- 进度值不在 0-100 范围内
- 创建大量重复的计划
- 忽视进度更新
- 计划内容不完整或不清晰
- 混淆 Plan 和 Todo 的用途

---

## 参考资源

- [完整工具参考](./tools.md) - 所有 MCP 工具的详细文档
- [Code 格式规范](../shared-references/code-format.md)
- [最佳实践](../shared-references/best-practices.md)
- [故障排除](../shared-references/troubleshooting.md)
