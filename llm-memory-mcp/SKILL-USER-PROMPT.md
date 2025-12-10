<llm_memory_skill>
## LLM Memory Skill 使用指南（Claude Code 专属）

使用 `llm-memory-mcp` 系列 skill 实现跨对话的上下文持久化。

### =====================================
### >>> MANDATORY BOOTSTRAP <<<
### =====================================

**每次新对话/Plan 模式/状态查询时，必须首先执行：**

```
>>> llm-memory-mcp:bootstrap
```

**这会自动触发完整链条：**
1. `load-context` - 加载持久化上下文
2. `manage-project` - 获取项目计划
3. `plan-tasks` - 获取任务列表

**不执行 bootstrap 会导致：**
- 丢失持久化上下文
- 重复创建任务
- 状态不一致

**>>> INVOKE llm-memory-mcp:bootstrap FIRST <<<**

### =====================================

### Skill 触发规则

| 触发条件 | 调用 Skill |
|----------|-----------|
| **>>> 新对话/Plan 模式/状态查询 (MANDATORY) <<<** | `llm-memory-mcp:bootstrap` |
| bootstrap 后自动触发 | `llm-memory-mcp:load-context` |
| 创建项目计划（>3步骤） | `llm-memory-mcp:manage-project` |
| 批量创建任务列表 | `llm-memory-mcp:plan-tasks` |
| 复杂任务需要多 Agent 并行 | `llm-memory-mcp:workflow-orchestrator` |
| Agent 开始处理任务 | `llm-memory-mcp:task-start` |
| Agent 完成任务 | `llm-memory-mcp:task-complete` |
| 执行中发现新任务 | `llm-memory-mcp:task-add` |
| 完成一批任务后更新计划进度 | `llm-memory-mcp:sync-progress` |
| 技术决策 / Bug 修复 / 制定规范后 | `llm-memory-mcp:record-decision` |
| 用户问"之前怎么做的" | `llm-memory-mcp:search-history` |

### 典型工作流

**开始新对话 (MANDATORY)**：
```
>>> llm-memory-mcp:bootstrap          # 必须首先调用！
    ├── llm-memory-mcp:load-context   # 自动触发
    ├── llm-memory-mcp:manage-project # 自动触发
    └── llm-memory-mcp:plan-tasks     # 自动触发
```

**规划复杂项目**：
```
→ llm-memory-mcp:bootstrap            # 已加载上下文
→ llm-memory-mcp:manage-project       # 创建/更新计划
→ llm-memory-mcp:plan-tasks           # 创建任务列表
→ llm-memory-mcp:workflow-orchestrator  # 分配 Agent（如需并行）
```

**执行任务**：
```
→ llm-memory-mcp:task-start           # 开始任务
→ （执行代码编写）
→ llm-memory-mcp:task-add             # 发现新任务时追加
→ llm-memory-mcp:task-complete        # 完成任务
→ llm-memory-mcp:sync-progress        # 同步进度
```

**记录知识**：
```
→ llm-memory-mcp:record-decision      # 记录决策/经验
→ llm-memory-mcp:search-history       # 后续搜索
```

### 命名规范

- **Plan**: `plan-<项目>` → `plan-user-auth`
- **Todo**: `todo-<项目>-<任务>` → `todo-auth-login`
- **Memory**: `mem-<主题>` → `mem-jwt-decision`
- 规则：全小写+连字符，≥3字符

### 作用域

- **创建时**：默认项目级别，不要使用 `global: true`
- **查询时**：使用 `scope: "all"` 获取当前可见数据

### Agent 分配标注

在 todo description 中标注负责的 Agent：
```
[Task-A] 任务描述  → Task Agent A 负责
[Task-B] 任务描述  → Task Agent B 负责
[Main] 任务描述    → 主 Agent 负责
```

### 优先级

- 4=🔴紧急(Bug/阻塞) | 3=🟠高(重要功能) | 2=🟡中(默认) | 1=🟢低(可选)

### 进度关联

- Plan 与 Todo 通过 code 前缀关联：`plan-auth` ↔ `todo-auth-*`
- 进度 = 已完成Todo数 / 总Todo数 × 100
</llm_memory_skill>
