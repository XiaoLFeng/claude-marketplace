<llm_memory_tool>
## LLM Memory MCP 工具使用指南

使用 `llm-memory` MCP 工具管理跨对话的持久化数据（计划、待办、记忆）。

### 触发时机
- **新对话开始**：调用 `plan_list` + `todo_list` 加载当前状态
- **复杂任务(>3步)**：`plan_create` 创建计划 → `todo_batch_create` 拆解子任务
- **短期任务(<1天)**：`todo_batch_create` 创建待办
- **重要决策/经验**：`memory_create` 记录知识
- **查询历史**：`memory_search` 搜索 → `memory_get` 获取详情
- **任务完成后**：`todo_batch_complete` 完成 → `plan_update` 更新进度

### 工具清单
```
# Plan（长期计划，>3步骤）
plan_list({ scope: "all" })
plan_create({ code: "plan-xxx", title, description, content })
plan_get({ code })
plan_update({ code, progress: 0-100 })

# Todo（短期任务，<1天）
todo_list({ scope: "all" })
todo_batch_create({ items: [{ code: "todo-xxx", title, priority: 1-4 }] })
todo_batch_complete({ codes: [...] })
todo_batch_start({ codes: [...] })
todo_batch_cancel({ codes: [...] })

# Memory（知识库）
memory_search({ keyword, scope: "all" })
memory_create({ code: "mem-xxx", title, content, category, tags })
memory_get({ code })
memory_list({ scope: "all" })
```

### 命名规范
- Plan: `plan-<项目>` → `plan-user-auth`
- Todo: `todo-<项目>-<任务>` → `todo-auth-login`
- Memory: `mem-<主题>` → `mem-jwt-decision`
- 规则：全小写+连字符，≥3字符

### 优先级（priority）
- 4=🔴紧急(Bug/阻塞) | 3=🟠高(重要功能) | 2=🟡中(默认) | 1=🟢低(可选)

### 进度关联
- Plan 与 Todo 通过 code 前缀关联：`plan-auth` ↔ `todo-auth-*`
- 进度 = 已完成Todo数 / 总Todo数 × 100

### 作用域
- **创建时**：默认项目级别，不要使用 `global: true`
- **查询时**：使用 `scope: "all"` 获取当前可见的所有数据
</llm_memory_tool>
