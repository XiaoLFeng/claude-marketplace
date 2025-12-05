# 示例 2: 修复登录 Bug (MCP 版本)

这是一个简单任务的快速处理示例，展示如何使用 llm-memory MCP 工具处理紧急 Bug。

## 场景描述

```
用户反馈：
"用户报告登录失败，错误信息显示'认证失败'，
但密码是正确的。这是紧急问题，需要尽快修复。"
```

## 步骤 1：需求分析

分析这个需求的特点：

```
✅ 复杂度：低 - 单一 Bug 修复
✅ 紧急程度：高 - 影响用户登录
✅ 时间跨度：短 - 预计 2 小时内
✅ 知识积累：可选 - 如果发现有价值的信息
✅ 作用域：项目特定（personal）

结论：
- 创建单个 Todo 即可
- 优先级设为 4（紧急）
- 可能需要记录排查过程
```

## 步骤 2：设计方案

向用户展示简化方案：

```markdown
📋 **工作流建议方案**

---

## Todo: 修复登录失败 Bug

**Code:** `todo-fix-login-failure`
**Scope:** `personal`
**Priority:** 4 🔴 紧急
**描述:** 排查并修复用户无法登录的问题，错误信息为"认证失败"

---

**是否确认创建？**
```

## 步骤 3：用户确认

```
用户回复：
"确认，开始处理"
```

## 步骤 4：执行 MCP 工具

```javascript
// 创建紧急任务
todo_batch_create({
  items: [{
    code: "todo-fix-login-failure",
    title: "修复登录失败 Bug",
    description: `## 问题描述
用户报告登录失败，密码正确但显示"认证失败"

## 待排查点
- [ ] 密码加密算法是否变更
- [ ] 数据库连接是否正常
- [ ] 认证中间件逻辑
- [ ] 最近代码变更

## 紧急程度
🔴 紧急 - 影响用户正常登录`,
    priority: 4
  }],
  scope: "personal"
})

// 返回: 批量创建成功! 共处理 1 个待办事项
```

## 步骤 5：确认结果

```
✅ 创建成功：
- 批量创建成功! 共处理 1 个待办事项
- code: todo-fix-login-failure [私有]
```

---

## 任务执行流程

### 开始任务

```javascript
// 标记为进行中
todo_batch_start({ codes: ["todo-fix-login-failure"] })
```

### 排查过程

排查发现问题：最近的代码变更中，密码比对逻辑有误。

```javascript
// 记录排查过程（可选）
memory_create({
  code: "mem-login-bug-investigation",
  title: "登录 Bug 排查记录",
  content: `# 排查过程

## 时间线
- 10:00 收到用户反馈
- 10:15 确认问题可复现
- 10:30 定位到密码比对逻辑

## 根因
昨天的提交 (commit: abc1234) 中，
将 bcrypt.compare() 的参数顺序写反了。

## 修复方案
\`\`\`javascript
// 错误写法
await bcrypt.compare(hashedPassword, plainPassword)

// 正确写法
await bcrypt.compare(plainPassword, hashedPassword)
\`\`\`

## 教训
1. 关键认证逻辑需要完整测试
2. Code Review 需要更仔细
3. 考虑添加自动化登录测试

## 影响范围
- 影响时间: 约 2 小时
- 影响用户: 约 50 人
- 无数据泄露风险`,
  category: "问题排查",
  tags: ["debug", "auth", "bug", "postmortem"],
  priority: 3,
  global: false
})
```

### 完成任务

```javascript
// 修复完成后，更新任务
todo_batch_update({
  items: [{
    code: "todo-fix-login-failure",
    description: `## 问题描述
用户报告登录失败，密码正确但显示"认证失败"

## 根因
bcrypt.compare() 参数顺序错误

## 修复方案
修正参数顺序，添加单元测试

## 状态
✅ 已修复并部署`,
    status: 2  // 2 = completed
  }]
})

// 或者直接完成
todo_batch_complete({ codes: ["todo-fix-login-failure"] })
```

### 验证修复

```javascript
// 查看任务详情
todo_list({ scope: "personal" })

// 查看所有已完成的任务
todo_list({ scope: "all" })
// 注意：已完成的任务默认不显示在列表中
```

---

## 扩展场景

### 场景 A：发现安全漏洞

如果在排查过程中发现安全漏洞：

```javascript
// 创建安全漏洞任务
todo_batch_create({
  items: [{
    code: "todo-fix-auth-security-vuln",
    title: "修复认证逻辑安全漏洞",
    description: `## 漏洞描述
在排查登录 Bug 时发现，连续登录失败没有限制，
可能遭受暴力破解攻击。

## 严重程度
🔴 高危

## 建议修复
1. 添加登录失败次数限制（5次）
2. 实现账户锁定机制（15分钟）
3. 添加验证码（连续失败3次后）
4. 记录异常登录日志`,
    priority: 4
  }],
  scope: "personal"
})
```

### 场景 B：需要升级为计划

如果发现问题比预期复杂，需要系统性重构：

```javascript
// 创建重构计划
plan_create({
  code: "plan-refactor-auth-module",
  title: "认证模块重构计划",
  description: "修复登录 Bug 时发现认证模块存在多个问题，需要系统性重构",
  content: `# 认证模块重构

## 背景
修复登录 Bug 时发现认证模块存在多个问题，需要系统性重构。

## 问题清单
1. 密码比对逻辑混乱
2. 缺少登录失败限制
3. 没有完整测试覆盖
4. 代码结构不清晰

## 计划阶段

### 阶段 1：代码审计（1天）
- 审计所有认证相关代码
- 识别安全风险点
- 记录改进建议

### 阶段 2：重构核心逻辑（2天）
- 重构密码验证逻辑
- 统一错误处理
- 提取认证服务类

### 阶段 3：添加安全限制（1天）
- 实现登录失败限制
- 添加账户锁定机制
- 实现验证码

### 阶段 4：测试和文档（1天）
- 补充单元测试
- 集成测试
- 更新文档`,
  scope: "personal"
})

// 原 Bug 修复任务标记完成
todo_batch_complete({ codes: ["todo-fix-login-failure"] })

// 创建新的重构任务
todo_batch_create({
  items: [
    {
      code: "todo-audit-auth-code",
      title: "审计认证模块代码",
      description: "全面审计认证相关代码，识别潜在问题",
      priority: 3
    },
    {
      code: "todo-refactor-password-logic",
      title: "重构密码验证逻辑",
      description: "统一密码验证流程，提取认证服务",
      priority: 4
    },
    {
      code: "todo-add-security-limits",
      title: "添加安全限制机制",
      description: "实现登录失败限制和账户锁定",
      priority: 4
    },
    {
      code: "todo-write-auth-tests",
      title: "补充认证测试",
      description: "补充完整的单元测试和集成测试",
      priority: 2
    }
  ],
  scope: "personal"
})
```

---

## 关键学习点

### 1. 优先级判断

```
🔴 priority=4（紧急）：
- 影响用户正常使用
- 生产环境问题
- 需要立即处理

这个 Bug 符合紧急标准！
```

### 2. 简单任务处理

```
✅ 简单任务不需要创建 Plan
✅ 使用 todo_batch_create 快速创建
✅ 单个任务也建议用批量工具（便于扩展）
✅ 发现复杂问题时再升级为 Plan
```

### 3. 知识积累

```
✅ 值得记录的场景：
- 排查过程有参考价值
- 发现了新的问题
- 总结出了经验教训
- 影响范围较大

❌ 不必记录的场景：
- 简单的拼写错误
- 配置问题
- 一次性修复
- 无通用价值
```

### 4. 状态管理

```
Todo 状态流转：
pending (0) → in_progress (1) → completed (2)
                              ↘ cancelled (3)

使用 batch_* 工具更新状态：
- batch_start: pending → in_progress
- batch_complete: * → completed
- batch_update: 精细控制（可指定 status）
```

### 5. 批量操作优势

```
✅ 即使只有一个任务也建议用批量工具：
- API 一致性好
- 返回格式统一
- 便于后续扩展
- 支持混合模式返回

示例：
todo_batch_create({ items: [{ code: "...", ... }] })
// 而不是单独的 todo_create (不存在)
```

---

## 常用命令速查

```javascript
// 创建任务（批量接口，items 数组）
todo_batch_create({
  items: [{
    code: "todo-xxx",
    title: "任务标题",
    description: "任务描述",
    priority: 4
  }],
  scope: "personal"
})

// 开始任务
todo_batch_start({ codes: ["todo-xxx"] })

// 完成任务
todo_batch_complete({ codes: ["todo-xxx"] })

// 更新任务（可以精细控制）
todo_batch_update({
  items: [{
    code: "todo-xxx",
    description: "更新后的描述",
    status: 2  // 0=pending, 1=in_progress, 2=completed, 3=cancelled
  }]
})

// 查看所有待办
todo_list({ scope: "all" })

// 创建记忆
memory_create({
  code: "mem-xxx",
  title: "知识标题",
  content: "知识内容",
  category: "分类",
  tags: ["tag1", "tag2"],
  priority: 3,
  global: false
})
```

---

呀~ 这就是一个简单 Bug 修复的完整流程！快速、高效！(´∀`)💖
