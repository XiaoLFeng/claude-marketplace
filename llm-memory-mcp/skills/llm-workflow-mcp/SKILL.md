---
name: llm-workflow-mcp
description: |
  智能工作流管理助手（MCP 版本）- 使用 llm-memory MCP 工具管理记忆、计划和待办。

  **何时调用此 Skill：**
  - 用户说"帮我规划一个任务"、"创建计划"、"制定方案"
  - 用户要求"拆解任务"、"创建待办"、"添加待办"
  - 用户需要"记录信息"、"保存知识"、"创建笔记"
  - 用户提到"跟踪进度"、"更新状态"、"搜索记忆"
  - 用户询问"工作流"、"项目管理"、"任务管理"

  **工作方式（MCP 版本）：**
  1. 分析用户需求，设计 Memory + Plan + Todo 方案
  2. 展示建议给用户确认（包括 code、优先级、作用域）
  3. 用户确认后，调用 MCP 工具创建
  4. 自动跟踪管理，支持批量操作
  5. 在关键节点创建 Memory 记录知识
---

# LLM-Memory 智能工作流管理 Skill (MCP 版本)

嘿嘿~ 欢迎使用 LLM-Memory 工作流管理助手 MCP 版本！(´∀`)💖

> **此版本特点**：使用 llm-memory MCP 工具直接调用，无需 CLI 命令，集成更加丝滑~

## 背景介绍

llm-memory 是一个为 LLM 设计的统一记忆管理系统，提供三大核心功能：

1. **Memory（记忆）**：持久化存储重要信息、偏好、上下文片段
2. **Plan（计划）**：跟踪多步骤目标，支持进度管理和子任务
3. **Todo（待办）**：管理短期任务，支持批量操作

**作用域系统：**
- `personal`：当前目录专属（私有）
- `group`：组内所有路径共享
- `global`：全局可见（仅 Memory 支持）

## ⭐ Code 格式规则（必读！）

这是最容易出错的地方，必须严格遵守！

### 格式要求

```
✅ 规则：
- 全小写字母（a-z）
- 可包含连字符（-）
- 开头和末尾必须是字母
- 最少 3 个字符

✅ 有效示例：
user-pref-001
feature-auth
bug-fix-login
task-database-migration

❌ 无效示例：
test_plan_001    ❌ 含下划线
Task-001         ❌ 含大写字母
-my-task         ❌ 开头不是字母
task-            ❌ 末尾不是字母
ab               ❌ 少于 3 个字符
myTask           ❌ 驼峰命名
```

### 推荐命名模式

```bash
# Memory 命名
mem-<主题>
例：mem-api-design, mem-user-preferences

# Plan 命名
plan-<简短描述>
例：plan-user-auth, plan-database-migration

# Todo 命名
todo-<动作>-<对象>
例：todo-fix-login-bug, todo-add-api-docs
```

### Code 唯一性规则

**Memory：**
- Code 全局唯一（整个系统中不能重复）

**Plan/Todo：**
- Code 在活跃状态中唯一（pending 和 in_progress）
- 已完成或已取消的 Code 可以重用

## 📊 优先级判断规则

自动为 Memory 和 Todo 判断优先级（1-4）：

### Priority 4 - 紧急 🔴

```
触发条件：
✅ Bug 修复、安全漏洞、系统故障
✅ 阻塞其他工作的任务
✅ 生产环境问题
✅ 截止时间在 24 小时内

示例：
- 修复用户无法登录的 bug
- 处理数据库连接失败
- 紧急安全补丁
```

### Priority 3 - 高 🟠

```
触发条件：
✅ 重要功能开发
✅ 影响用户体验的问题
✅ 关键里程碑任务
✅ 截止时间在 3 天内

示例：
- 实现核心 API 端点
- 重要页面的 UI 优化
- 集成第三方服务
```

### Priority 2 - 中 🟡（默认）

```
触发条件：
✅ 常规开发任务
✅ 功能优化改进
✅ 无明确截止时间
✅ 不确定的优先级

示例：
- 添加单元测试
- 代码重构
- 文档更新
```

### Priority 1 - 低 🟢

```
触发条件：
✅ 可选的改进
✅ 技术债务清理
✅ 长期优化计划
✅ 学习和探索任务

示例：
- 性能优化探索
- 代码注释补充
- 依赖包升级
```

## 🔄 混合模式交互流程

这是此 Skill 的核心工作方式！必须严格遵循：

### 步骤 1：分析需求

当用户提出需求时，首先进行分析：

```
需要考虑：
1. 任务复杂度：是单一任务还是多步骤项目？
2. 时间跨度：短期（<1天）还是长期（>3天）？
3. 依赖关系：任务之间有无依赖？
4. 知识积累：是否需要记录关键信息？
5. 作用域：是项目特定还是跨项目通用？

决策逻辑：
- 单一简单任务 → 只创建 Todo
- 多步骤任务 → 创建 Plan + Todos
- 需要记录知识 → 添加 Memory
- 通用知识 → Memory 使用 global 作用域
```

### 步骤 2：提出建议方案

向用户展示格式化的建议，等待确认：

```markdown
📋 **工作流建议方案**

---

## Plan: <计划标题>

**Code:** `plan-xxx-xxx`
**Scope:** `personal` / `group`
**描述:** <简要说明计划目标>
**详细内容:**
```markdown
# 实施计划

## 阶段 1：...
- 步骤 1
- 步骤 2

## 阶段 2：...
- 步骤 1
- 步骤 2
```

---

## Todos: (共 N 个任务)

### 1️⃣ [Priority 4 🔴 紧急] <任务标题>
- **Code:** `todo-xxx-xxx`
- **Scope:** `personal` / `group`
- **描述:** <任务详情>
- **原因:** <为什么是紧急>

### 2️⃣ [Priority 3 🟠 高] <任务标题>
- **Code:** `todo-yyy-yyy`
- **描述:** <任务详情>

---

## Memory: (可选)

**Code:** `mem-xxx-xxx`
**标题:** <知识点标题>
**分类:** <分类名称>
**Scope:** `global` / `personal` / `group`
**标签:** tag1, tag2, tag3
**内容:**
```
<要记录的知识内容>
```

---

**是否确认创建？**
如需调整（修改优先级、code、作用域、增删任务），请告诉我~ (｡･ω･｡)ﾉ゛
```

### 步骤 3：等待用户确认

用户可以：
- 直接确认 → 执行步骤 4
- 修改建议 → 调整后重新展示
- 取消操作 → 结束流程

### 步骤 4：执行 MCP 工具

用户确认后，依次调用 MCP 工具创建：

```javascript
// 1. 创建 Plan（如果需要）
plan_create({
  code: "plan-user-auth",
  title: "用户认证系统重构",
  description: "将 Session 认证迁移到 JWT 机制",
  content: "# 实施计划\n\n## 阶段1...",
  scope: "personal"  // 可选: personal, group
})

// 2. 批量创建 Todos
todo_batch_create({
  items: [
    {
      code: "todo-design-db",
      title: "设计认证数据库架构",
      description: "设计 Token 存储表结构",
      priority: 3
    },
    {
      code: "todo-impl-jwt",
      title: "实现 JWT 令牌机制",
      description: "实现签发、验证、刷新逻辑",
      priority: 4
    }
  ],
  scope: "personal"  // 可选: personal, group
})

// 3. 创建 Memory（如果需要）
memory_create({
  code: "mem-jwt-decision",
  title: "JWT vs Session 选型分析",
  content: "# 技术选型\n\n## 为什么选择 JWT...",
  category: "架构决策",
  tags: ["auth", "jwt", "decision"],
  global: false,     // 可选: true=全局, false=当前作用域
  scope: "personal"  // 可选: personal, group
})
```

### 步骤 5：确认结果

检查 MCP 工具的返回结果：

```
✅ 成功输出示例：
- Plan 创建成功! code=plan-user-auth [私有]
- 批量创建成功! 共处理 2 个待办事项
- Memory 创建成功! code=mem-jwt-decision [私有]

❌ 错误输出示例：
- code 格式错误: 全小写字母，可含连字符
- 活跃状态中已存在相同的 code
- 无权限操作此记录

⚠️ 部分成功示例：
- 批量创建部分完成! 成功 5 个，失败 2 个
  失败详情:
  - todo-001: code 格式错误
  - todo-002: 活跃状态中已存在
```

如有错误，立即向用户报告并提供解决方案。

## 📝 MCP 工具完整清单

### Memory 工具（6个）

#### `memory_list` - 列出记忆

```javascript
memory_list({
  scope: "personal" | "group" | "global" | "all"  // 可选，默认 all
})

// 返回格式：
// - code: xxx [私有/小组/全局]
// - title: xxx
// - category: xxx
```

#### `memory_create` - 创建记忆

```javascript
memory_create({
  code: "mem-xxx",           // 必填，全局唯一
  title: "标题",             // 必填
  content: "内容",           // 必填
  category: "分类",          // 可选，默认"默认"
  tags: ["tag1", "tag2"],    // 可选
  priority: 2,               // 可选，1-4，默认2
  global: false,             // 可选，true=全局可见
  scope: "personal"          // 可选: personal, group
})
```

#### `memory_get` - 获取记忆详情

```javascript
memory_get({
  code: "mem-xxx"  // 必填
})

// 返回完整信息：code, title, content, category, tags,
// priority, scope标签, 创建/更新时间
```

#### `memory_update` - 更新记忆

```javascript
memory_update({
  code: "mem-xxx",           // 必填
  title: "新标题",           // 可选
  content: "新内容",         // 可选
  category: "新分类",        // 可选
  tags: ["new", "tags"],     // 可选
  priority: 3                // 可选
})
```

#### `memory_delete` - 删除记忆

```javascript
memory_delete({
  code: "mem-xxx"  // 必填
})
```

#### `memory_search` - 搜索记忆

```javascript
memory_search({
  keyword: "关键词",         // 必填
  scope: "all"               // 可选: personal, group, global, all
})

// 在 title 和 content 中模糊搜索
```

### Plan 工具（4个）

#### `plan_list` - 列出计划

```javascript
plan_list({
  scope: "personal" | "group" | "all"  // 可选，默认 all
})

// 返回格式：
// - code: xxx [私有/小组]
// - title: xxx
// - status: pending/in_progress/completed/cancelled
// - progress: 0-100
```

#### `plan_create` - 创建计划

```javascript
plan_create({
  code: "plan-xxx",          // 必填，活跃状态唯一
  title: "标题",             // 必填
  description: "摘要",       // 必填
  content: "详细内容",       // 必填，支持 Markdown
  scope: "personal"          // 可选: personal, group
})
```

#### `plan_get` - 获取计划详情

```javascript
plan_get({
  code: "plan-xxx"  // 必填
})

// 返回完整信息：code, title, description, content,
// status, progress, scope标签, 子任务列表, 创建/更新时间
```

#### `plan_update` - 更新计划

```javascript
plan_update({
  code: "plan-xxx",          // 必填
  title: "新标题",           // 可选
  description: "新描述",     // 可选
  content: "新内容",         // 可选
  progress: 50               // 可选，0-100
})

// 注意：
// - progress=0 自动设为 pending
// - progress=1-99 自动设为 in_progress
// - progress=100 自动设为 completed
```

### Todo 工具（6个）

#### `todo_list` - 列出待办

```javascript
todo_list({
  scope: "personal" | "group" | "all"  // 可选，默认 all
})

// 返回格式：
// - code: xxx [私有/小组]
// - title: xxx
// - status: pending/in_progress/completed/cancelled
// - priority: 1-4
// - due_date: 截止日期（可能为空）
```

#### `todo_batch_create` - 批量创建待办

```javascript
todo_batch_create({
  items: [
    {
      code: "todo-xxx",      // 必填，活跃状态唯一
      title: "标题",         // 必填
      description: "描述",   // 可选
      priority: 3,           // 可选，1-4，默认2
      due_date: "2024-12-31T23:59:59Z"  // 可选，ISO 8601
    }
    // ... 最多 100 个
  ],
  scope: "personal"          // 可选: personal, group
})

// 返回混合模式结果：
// - 成功数量
// - 失败数量
// - 失败详情（code + 错误信息）
```

#### `todo_batch_complete` - 批量完成待办

```javascript
todo_batch_complete({
  codes: ["todo-1", "todo-2"]  // 必填，最多 100 个
})

// 返回混合模式结果
```

#### `todo_batch_start` - 批量开始待办

```javascript
todo_batch_start({
  codes: ["todo-1", "todo-2"]  // 必填，最多 100 个
})

// 返回混合模式结果
```

#### `todo_batch_update` - 批量更新待办

```javascript
todo_batch_update({
  items: [
    {
      code: "todo-1",        // 必填
      title: "新标题",       // 可选
      description: "新描述", // 可选
      priority: 4,           // 可选
      status: 2              // 可选，0=pending, 1=in_progress, 2=completed, 3=cancelled
    }
    // ... 最多 100 个
  ]
})

// 返回混合模式结果
```

#### `todo_final` - 清空所有待办

```javascript
todo_final({
  scope: "personal"  // 可选: personal, group
})

// 警告：不可恢复！
// - 未加入组时删除私有待办
// - 已加入组时删除组内所有待办
```

### Group 工具（1个）

#### `group_add_path` - 添加路径到组

```javascript
group_add_path({
  group_name: "my-project",  // 必填
  path: "/path/to/dir"       // 可选，留空则用当前路径
})

// 注意：只能操作当前路径
// 添加后，当前路径的数据会在组内共享
```

## 💡 Memory 使用指南

### 何时创建 Memory

**必须创建的场景：**

```
1. 架构决策
   - 为什么选择某个技术方案
   - 设计模式的选择理由
   - API 设计约定

2. 问题解决方案
   - 复杂 Bug 的排查过程
   - 性能优化的关键点
   - 踩坑经验和解决办法

3. 代码示例
   - 常用代码片段
   - API 使用示例
   - 配置模板

4. 项目规范
   - 代码风格约定
   - 命名规范
   - Git 工作流
```

**可选创建的场景：**

```
1. 调试发现
   - 有价值的调试技巧
   - 工具使用心得

2. 第三方库
   - 库的使用技巧
   - 常见问题解决

3. 环境配置
   - 开发环境设置
   - 部署流程说明
```

### Memory 内容格式建议

使用清晰的结构编写，增强可读性：

```markdown
# <知识点标题>

## 背景
<为什么需要这个知识点>

## 核心内容
<详细说明>

## 代码示例
```<language>
<code here>
```

## 参考链接
- [Link 1](URL)
- [Link 2](URL)

## 注意事项
- 注意点 1
- 注意点 2
```

### Memory 作用域选择

```
✅ 使用 global=true：
- 通用知识和最佳实践
- 跨项目共享的配置
- 用户全局偏好

✅ 使用 scope="personal"：
- 项目特定的记录
- 临时笔记和想法
- 本地实验数据

✅ 使用 scope="group"：
- 团队协作项目
- 多仓库管理
- 共享的开发环境
```

## 🎯 完整示例场景

我们提供了两个完整的示例场景，展示如何在真实项目中使用工作流管理：

### 示例索引

#### [示例 1: 用户认证系统重构](./examples/auth-system-refactor.md)
- **难度**: 高
- **特点**: 复杂的长期项目，展示 Plan + Todos + Memory 完整流程
- **学习要点**: 任务拆解、优先级判断、架构决策记录

#### [示例 2: 修复登录 Bug](./examples/fix-login-bug.md)
- **难度**: 低
- **特点**: 简单的单任务示例，快速处理
- **学习要点**: 紧急任务处理、简单流程

查看 [examples/README.md](./examples/README.md) 了解更多示例和使用指南。

---

## 🔧 进度跟踪

完成任务后，及时更新状态：

```javascript
// 开始任务
todo_batch_start({ codes: ["todo-design-db"] })

// 完成任务
todo_batch_complete({ codes: ["todo-design-db"] })

// 查看所有待办
todo_list({ scope: "all" })

// 更新计划进度
plan_update({ code: "plan-user-auth", progress: 50 })
```

### 进度跟踪最佳实践

```
1. 开始任务前：
   ✅ 执行 batch_start 标记为进行中
   ✅ 确认没有依赖任务未完成

2. 任务完成后：
   ✅ 立即执行 batch_complete
   ✅ 如果有新发现，创建 Memory 记录
   ✅ 如果有新任务，创建新 Todo

3. 定期检查：
   ✅ 每天执行 todo_list 查看未完成任务
   ✅ 重新评估优先级
   ✅ 识别阻塞任务
```

## ❌ 错误处理

### 常见错误 1：Code 格式错误

```
错误信息：
code 格式错误: 全小写字母，可含连字符，开头末尾必须是字母

原因：
使用了大写字母、下划线、数字开头等不符合规则的字符

解决方案：
1. 检查 code 是否全小写
2. 将下划线（_）替换为连字符（-）
3. 确保开头和末尾是字母
4. 确保长度 >= 3

示例修正：
❌ test_plan_001 → ✅ test-plan-one
❌ Task-001      → ✅ task-one
❌ -my-task      → ✅ my-task
```

### 常见错误 2：Code 重复

```
错误信息：
活跃状态中已存在相同的 code，请使用不同的标识码

原因：
- Memory: code 全局唯一冲突
- Plan/Todo: 活跃状态（pending/in_progress）中 code 冲突

解决方案：
1. 执行 list 命令查看已存在的 code
2. 使用更具体的命名
3. 或者先完成/取消旧记录

示例修正：
❌ todo-fix-bug → ✅ todo-fix-login-bug
❌ mem-notes    → ✅ mem-api-design-notes
```

### 常见错误 3：找不到记录

```
错误信息：
记录不存在或无权限访问

原因：
1. Code 拼写错误
2. 作用域不匹配（记录在其他路径/组）
3. 记录已删除或已完成

解决方案：
1. 执行 list 命令确认 code
2. 检查作用域（personal/group/global）
3. 确认记录状态（已完成的 Plan/Todo 不显示）
```

### 常见错误 4：批量操作部分失败

```
错误信息：
批量创建部分完成! 成功 8 个，失败 2 个

原因：
- 部分 code 格式错误
- 部分 code 已存在
- 部分记录无权限操作

解决方案：
1. 查看 failures 列表获取失败详情
2. 修正失败的项
3. 重新提交失败的项（不需要重复成功的）

示例：
failures: [
  { code: "todo-001", error: "code 格式错误" },
  { code: "todo-002", error: "活跃状态中已存在" }
]
```

## ✅ 最佳实践

### 1. Code 命名规范

```
✅ 推荐做法：
- 使用语义化名称：plan-api-redesign
- 简洁但清晰：todo-fix-login
- 包含动作词：todo-add-tests, todo-update-docs
- 体现层级关系：todo-auth-jwt-impl, todo-auth-middleware

❌ 避免做法：
- 过长：plan-this-is-a-very-long-description-of-the-task
- 过短：abc, tsk
- 数字编号：task001, todo-123
- 无意义：temp, test, xxx
```

### 2. 优先级设置

```
✅ 推荐做法：
- 基于影响范围和紧急程度
- 阻塞性任务设为高优先级
- 不确定时使用默认（中）
- 定期重新评估

❌ 避免做法：
- 所有任务都是紧急（失去意义）
- 从不更新优先级
- 忽略依赖关系
```

### 3. Plan vs Todo 选择

```
使用 Plan：
✅ 多步骤复杂任务（>3步）
✅ 长期目标（>3天）
✅ 需要跟踪整体进度
✅ 有子任务依赖关系

使用 Todo：
✅ 单一任务
✅ 短期行动项（<1天）
✅ 独立任务（无依赖）
✅ 简单的提醒事项
```

### 4. 作用域选择

```
✅ 使用 personal：
- 项目特定的计划和任务
- 临时笔记和想法
- 本地实验数据

✅ 使用 group：
- 团队协作项目
- 多仓库管理
- 共享的开发环境

✅ 使用 global（Memory）：
- 通用知识和最佳实践
- 跨项目共享的配置
- 用户全局偏好
```

### 5. 批量操作

```
✅ 推荐做法：
- 先查询 list 获取所有项
- 筛选需要操作的 codes
- 使用 batch_* 工具批量处理
- 检查返回结果处理失败项

❌ 避免做法：
- 循环调用单个创建（效率低）
- 不检查批量操作结果
- 超过 100 个不分批
```

### 6. 工作流程

```
完整流程：
1. 需求分析 → 设计方案
2. 用户确认 → 批量创建
3. 执行任务 → 更新状态
4. 完成任务 → 记录知识
5. 回顾总结 → 改进流程

日常习惯：
- 每天开始：todo_list 查看待办
- 任务开始：batch_start 标记状态
- 任务完成：batch_complete 标记完成
- 新发现：memory_create 记录知识
- 每周回顾：评估进度和优先级
```

## 🚨 当前已知限制

### 限制 1：作用域隔离

**行为**：所有数据默认作用于当前路径

**影响**：
- 切换目录后看不到之前的记录
- Plan 和 Todo 不支持 global

**建议**：
- 在项目根目录执行命令
- 重要的通用知识使用 Memory + global
- 多项目协作使用 Group

### 限制 2：SubTask 管理

**问题**：MCP 工具尚未支持 Plan 的子任务管理

**影响**：无法通过 MCP 添加/更新子任务

**临时方案**：使用 TUI 或直接操作数据库

### 限制 3：批量操作限制

**限制**：最多 100 个项

**影响**：大量数据需要分批处理

**建议**：
- 分批处理（每批 100 个）
- 检查每批的返回结果
- 处理失败项后再继续

## 📚 快速参考

### 常用操作速查

```javascript
// 创建待办（最常用）
todo_batch_create({
  items: [{
    code: "todo-xxx",
    title: "任务标题",
    description: "任务描述",
    priority: 2
  }],
  scope: "personal"
})

// 查看所有待办
todo_list({ scope: "all" })

// 开始任务
todo_batch_start({ codes: ["todo-xxx"] })

// 完成任务
todo_batch_complete({ codes: ["todo-xxx"] })

// 创建记忆
memory_create({
  code: "mem-xxx",
  title: "知识标题",
  content: "知识内容",
  category: "分类",
  tags: ["tag1", "tag2"],
  global: false
})

// 搜索记忆
memory_search({ keyword: "关键词", scope: "all" })

// 创建计划
plan_create({
  code: "plan-xxx",
  title: "计划标题",
  description: "计划描述",
  content: "# 详细内容\n\n..."
})

// 更新进度
plan_update({ code: "plan-xxx", progress: 50 })
```

### 状态速查

**Plan 状态：**
```
pending     - 待开始（progress=0）
in_progress - 进行中（progress=1-99）
completed   - 已完成（progress=100）
cancelled   - 已取消
```

**Todo 状态：**
```
0 - pending     （待处理）
1 - in_progress （进行中）
2 - completed   （已完成）
3 - cancelled   （已取消）
```

### 优先级速查

```
1 = 低   🟢 可选改进
2 = 中   🟡 常规任务（默认）
3 = 高   🟠 重要功能
4 = 紧急 🔴 Bug/阻塞
```

### 作用域速查

```
personal - 当前路径私有
group    - 组内共享
global   - 全局可见（仅 Memory）
all      - 查询所有可见（默认）
```
