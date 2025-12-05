---
name: plan-mcp
description: |
  LLM-Memory 计划管理工具 (MCP版本) - 使用 MCP 工具跟踪多步骤目标。

  **何时调用此 Skill：**
  - 用户说"帮我规划"、"制定计划"、"创建方案"
  - 任务复杂度高（>3步骤）且时间跨度长（>3天）
  - 用户需要"跟踪进度"、"更新计划"

  **不调用此 Skill：**
  - 单一简单任务（使用 todo-mcp）
  - 短期任务（<1天，使用 todo-mcp）
---

# Plan MCP 管理 Skill

使用 MCP 工具管理复杂的多步骤项目，跟踪长期目标和整体进度。

## ⚡ 快速参考

### Code 格式

```
全小写 + 连字符，字母开头/结尾，≥3 字符
推荐：plan-<简短描述>
✅ plan-user-auth  ❌ Plan_001
活跃状态唯一（pending/in_progress）
```

### 作用域选择

```
scope: "personal"  - 当前目录私有（默认）
scope: "group"     - 组内共享（团队项目）
```

### Plan vs Todo 选择

**使用 Plan：**
- 多步骤复杂任务（>3步）
- 长期目标（>3天）
- 需要跟踪整体进度（0-100%）
- 有子任务依赖关系

**使用 Todo：**
- 单一任务
- 短期行动项（<1天）
- 独立任务（无依赖）

### 进度和状态

```
progress=0     → pending（待开始）
progress=1-99  → in_progress（进行中）
progress=100   → completed（已完成）
```

---

## 🔧 核心操作

### 创建计划

```javascript
plan_create({
  code: "plan-xxx",
  title: "计划标题",
  description: "计划摘要",
  content: "# 详细内容\n\n- 步骤1\n- 步骤2",
  scope: "personal"
})
```

### 进度管理

```javascript
// 更新进度（状态自动转换）
plan_update({
  code: "plan-xxx",
  progress: 50  // 0=pending, 1-99=in_progress, 100=completed
})
```

### 查看和管理

```javascript
// 列出计划
plan_list({ scope: "all" })

// 获取详情
plan_get({ code: "plan-xxx" })

// 更新内容
plan_update({
  code: "plan-xxx",
  title: "新标题",
  content: "新内容"
})
```

---

## 📚 MCP 工具清单

- `plan_list` - 列出计划
- `plan_create` - 创建计划
- `plan_get` - 获取详情
- `plan_update` - 更新计划（含进度）

详见：[完整工具参考](./references/tools.md)

---

## 🔗 参考文档

- [完整工具参考](./references/tools.md) - 所有 MCP 工具详细说明
- [使用示例](./references/examples.md) - 真实场景案例
- [作用域系统](./references/tools.md#作用域系统) - 作用域详解
- [Code 格式](../shared-references/code-format.md) - 格式规则详解
- [故障排除](../shared-references/troubleshooting.md) - 错误处理
- [最佳实践](../shared-references/best-practices.md) - 优化建议
