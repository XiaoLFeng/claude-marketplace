<llm_memory_skill>
## =====================================
## >>> MANDATORY SKILL INVOCATION <<<
## =====================================

**使用 llm-memory MCP 时必须调用此 Skill：**

```
>>> llm-memory:guide
```

---

### 为什么必须调用？

此 Skill 提供 llm-memory MCP 的完整使用指南，包括：

- **新对话初始化流程** - 确保上下文连续性
- **MCP 工具详解** - 正确使用 Plan/Todo/Memory
- **工作流指南** - 项目管理、任务执行、知识记录
- **命名规范** - 保持数据关联性
- **最佳实践** - 避免常见错误

---

### 触发时机

| 场景                 | 操作                    |
| -------------------- | ----------------------- |
| 新对话开始           | 调用 `llm-memory:guide` |
| 进入 Plan 模式       | 调用 `llm-memory:guide` |
| 需要管理计划/任务    | 调用 `llm-memory:guide` |
| 首次使用 llm-memory  | 调用 `llm-memory:guide` |
| 在你认为使用模糊时候 | 调用 `llm-memory:guide` |

---

### 不调用会导致

- 丢失持久化上下文
- 重复创建任务/计划
- 命名不规范导致数据混乱
- 进度无法正确同步
- 工作流不完整

---

### 正确流程

```
1. 检测到 llm-memory MCP 可用
   ↓
2. >>> llm-memory:guide  （必须）
   ↓
3. 执行初始化：plan_list + todo_list
   ↓
4. 展示当前状态
   ↓
5. 按指南执行工作流
```

---

## >>> INVOKE llm-memory:guide FIRST <<<
</llm_memory_skill>
