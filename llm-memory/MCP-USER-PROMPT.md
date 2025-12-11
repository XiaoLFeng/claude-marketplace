<llm_memory_mcp>
## LLM Memory MCP 使用指南

智能工作流管理助手，实现跨对话的持久化数据管理。

> **适用场景**：此文档适用于不支持 Skill 的 AI 客户端（如 Cursor、Windsurf 等）。
> 如果你的客户端支持 Skill，请优先使用 `llm-memory:guide` Skill 获取完整指南。

---

### 快速开始

#### 新对话初始化（必须）

每次新对话开始时，必须执行以下步骤加载当前状态：

```javascript
// 1. 加载计划列表
const plans = await plan_list({ scope: "all" });

// 2. 加载任务列表
const todos = await todo_list({ scope: "all" });

// 3. 展示状态
// - 进行中的计划及进度
// - 待处理/进行中的任务
```

---

### 三层数据模型

| 类型 | 用途 | 命名格式 | 生命周期 |
|------|------|----------|----------|
| **Plan** | 长期项目（>3步骤） | `plan-<项目>` | 数天~数周 |
| **Todo** | 短期任务（<1天） | `todo-<项目>-<任务>` | 数小时~1天 |
| **Memory** | 知识/决策记录 | `mem-<主题>` | 永久 |

---

### MCP 工具清单

#### Plan 管理
```javascript
// 列出计划
plan_list({ scope: "all" })

// 创建计划
plan_create({
  code: "plan-user-auth",
  title: "用户认证系统",
  description: "实现完整认证功能",
  content: "## 目标\n详细内容..."
})

// 获取详情
plan_get({ code: "plan-user-auth" })

// 更新进度 (0-100)
plan_update({ code: "plan-user-auth", progress: 50 })
```

#### Todo 管理
```javascript
// 列出任务
todo_list({ scope: "all" })

// 批量创建
todo_batch_create({
  items: [
    { code: "todo-auth-login", title: "实现登录", priority: 3 },
    { code: "todo-auth-register", title: "实现注册", priority: 3 }
  ]
})

// 开始任务 (状态 0→1)
todo_batch_start({ codes: ["todo-auth-login"] })

// 完成任务 (状态→2)
todo_batch_complete({ codes: ["todo-auth-login"] })

// 取消任务 (状态→3)
todo_batch_cancel({ codes: ["todo-auth-deprecated"] })

// 更新任务
todo_batch_update({
  items: [{ code: "todo-auth-login", priority: 4 }]
})
```

#### Memory 管理
```javascript
// 搜索记忆
memory_search({ keyword: "jwt", scope: "all" })

// 创建记忆
memory_create({
  code: "mem-jwt-decision",
  title: "JWT 选型决策",
  content: "## 决策\n...",
  category: "技术决策",
  tags: ["auth", "jwt"]
})

// 获取详情
memory_get({ code: "mem-jwt-decision" })

// 列出所有
memory_list({ scope: "all" })
```

---

### 命名规范

```
Plan:   plan-<项目>           → plan-user-auth
Todo:   todo-<项目>-<任务>    → todo-auth-login
Memory: mem-<主题>            → mem-jwt-decision

规则：全小写 + 连字符，≥3字符
```

### 优先级

- `4` = 🔴 紧急（Bug/阻塞）
- `3` = 🟠 高（核心功能）
- `2` = 🟡 中（默认）
- `1` = 🟢 低（优化/文档）

### 状态定义

**Todo**: `0`=待处理 | `1`=进行中 | `2`=已完成 | `3`=已取消

**Plan**: 根据 progress 自动转换
- `0%` → pending
- `1-99%` → in_progress
- `100%` → completed

---

### 典型工作流

#### 新项目
```javascript
// 1. 创建计划
await plan_create({ code, title, description, content });

// 2. 创建任务
await todo_batch_create({ items: [...] });

// 3. 执行任务
await todo_batch_start({ codes: [...] });
// ... 工作 ...
await todo_batch_complete({ codes: [...] });

// 4. 更新进度
await plan_update({ code, progress });
```

#### 记录知识
```javascript
await memory_create({
  code: "mem-xxx",
  title: "标题",
  content: "内容",
  category: "分类",
  tags: ["标签"]
});
```

#### 查询历史
```javascript
const results = await memory_search({ keyword: "关键词" });
const detail = await memory_get({ code: "mem-xxx" });
```

---

### 最佳实践

1. **新对话必初始化**：始终先 `plan_list` + `todo_list`
2. **及时更新状态**：任务开始/完成时立即更新
3. **进度同步**：批量完成后更新 Plan 进度
4. **知识沉淀**：重要决策记录到 Memory
5. **命名一致**：遵循命名规范保持关联
</llm_memory_mcp>
