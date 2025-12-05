# LLM-Workflow-MCP 示例

嘿嘿~ 这里提供了完整的 MCP 版本工作流示例！(´∀`)💖

## 示例列表

### [示例 1: 用户认证系统重构](./auth-system-refactor.md)
- **难度**: 高
- **预计时间**: 1 周
- **特点**: 复杂的长期项目，展示完整 MCP 工作流程
- **学习要点**:
  - 如何使用 plan_create 创建长期计划
  - 如何使用 todo_batch_create 批量创建任务
  - 如何使用 memory_create 记录架构决策
  - 作用域选择和状态管理

### [示例 2: 修复登录 Bug](./fix-login-bug.md)
- **难度**: 低
- **预计时间**: 2 小时
- **特点**: 简单的单任务示例，快速处理
- **学习要点**:
  - 紧急任务的快速创建
  - 状态更新流程
  - 任务升级为计划的判断

## 示例使用方式

1. 阅读示例文档了解完整流程
2. 参考 MCP 工具调用格式
3. 根据实际需求调整参数
4. 在 Claude Code 中实践

## MCP vs CLI 对比

| 特性 | CLI 版本 | MCP 版本 |
|------|----------|----------|
| 调用方式 | `./llm-memory todo create ...` | `todo_batch_create({...})` |
| 集成度 | 需要 Bash 执行 | 原生 MCP 工具 |
| 批量操作 | 支持（JSON 格式） | 原生支持（最多 100 个） |
| 状态管理 | 字符串状态 | 整数状态（0-3） |
| 作用域 | personal/group | personal/group/global（Memory） |
| 返回格式 | CLI 文本输出 | 结构化 MCP 响应 |

## 快速开始

```javascript
// 1. 查看当前待办
todo_list({ scope: "all" })

// 2. 创建第一个任务
todo_batch_create({
  items: [{
    code: "todo-get-started",
    title: "开始使用 LLM-Memory MCP",
    description: "学习如何使用 MCP 版本的工作流管理",
    priority: 2
  }],
  scope: "personal"
})

// 3. 开始任务
todo_batch_start({ codes: ["todo-get-started"] })

// 4. 完成任务
todo_batch_complete({ codes: ["todo-get-started"] })
```

## 重要提示

### Code 格式规则
```
✅ 全小写字母 + 连字符
✅ 开头末尾必须是字母
✅ 最少 3 个字符

示例: todo-fix-bug, plan-user-auth, mem-api-design
```

### 作用域说明
```
personal - 当前目录私有
group    - 组内路径共享（需先加入组）
global   - 全局可见（仅 Memory 支持）
```

### 批量操作限制
```
⚠️ 最多 100 个项
⚠️ 检查返回结果的 success_count 和 fail_count
⚠️ 处理 failures 列表中的失败项
```

呀~ 希望这些示例能帮助你快速上手！(｡･ω･｡)ﾉ゛
