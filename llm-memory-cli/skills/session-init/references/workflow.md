# 工作流参考

## 新对话初始化流程

当开始新对话时，按照以下流程快速了解项目状态：

### 1. 获取计划概览

```bash
llm-memory plan list
```

显示当前项目中所有计划的状态：
- 进行中的计划
- 待开始的计划
- 已完成的计划

### 2. 获取待办事项

```bash
llm-memory todo list
```

显示当前待办事项：
- 按优先级分组
- 显示紧急程度
- 关联的计划信息

### 3. 查看详细信息（可选）

如需深入了解特定计划或待办：

```bash
# 查看特定计划详情
llm-memory plan view <plan-id>

# 查看特定待办详情
llm-memory todo view <todo-id>
```

## 常见场景

### 场景 1: 完全新对话

用户刚打开 Claude，需要快速了解项目状态。

**操作**:
1. 执行 `llm-memory plan list`
2. 执行 `llm-memory todo list`
3. 总结当前状态，告知用户

### 场景 2: 用户询问进展

用户问："现在做到哪了？"

**操作**:
1. 查看计划列表
2. 查看待办列表
3. 重点说明进行中的任务
4. 提示下一步行动

### 场景 3: 用户询问历史

用户问："之前怎么做的？"

**操作**:
1. 查看历史记忆 `llm-memory memory search <关键词>`
2. 查看相关计划的历史状态
3. 总结之前的决策和方法

## 最佳实践

1. **主动加载**: 新对话开始时主动加载状态，不要等用户问
2. **简洁展示**: 首次展示用概览形式，不要过于详细
3. **智能过滤**: 只展示活跃的计划和待办，避免信息过载
4. **提供建议**: 根据状态给出下一步建议

## 相关技能

- [workflow-orchestrator](../../workflow-orchestrator/SKILL.md) - 工作流编排器
- [manage-tasks](../../manage-tasks/SKILL.md) - 任务管理
- [search-history](../../search-history/SKILL.md) - 历史检索
