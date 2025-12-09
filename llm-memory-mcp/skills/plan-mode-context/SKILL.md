---
name: plan-mode-context
description: "Plan 模式上下文加载器 - 进入 Plan 模式时自动加载。读取 memory_list/plan_list/todo_list 检查未完成任务和相关记忆，对重要项调用 get 获取详情，帮助制定更好的计划。"
---

# Plan Mode Context Loader

进入 Plan 模式时自动加载项目上下文，帮助制定更好的计划。

## 触发条件

- 进入 Plan 模式
- 用户说"开始规划"、"制定计划"
- 系统提示 `Plan mode is active`

## 操作流程

### Step 1: 加载现有计划

```javascript
// 获取所有未完成的计划
const plans = await plan_list({ scope: "all" });
const activePlans = plans.filter(p => p.progress < 100);

// 对进行中的计划获取详情
for (const plan of activePlans.filter(p => p.progress > 0)) {
  const detail = await plan_get({ code: plan.code });
  // 展示计划详情
}
```

### Step 2: 加载待办任务

```javascript
// 获取所有未完成的任务
const todos = await todo_list({ scope: "all" });
const pendingTodos = todos.filter(t => t.status !== 2);

// 按优先级排序展示
const sorted = pendingTodos.sort((a, b) => b.priority - a.priority);
```

### Step 3: 搜索相关记忆

```javascript
// 根据用户请求提取关键词
const keywords = extractKeywordsFromUserRequest();

// 搜索相关记忆
const memories = await memory_search({
  keyword: keywords,
  scope: "all"
});

// 对最相关的记忆获取详情
for (const mem of memories.slice(0, 3)) {
  const detail = await memory_get({ code: mem.code });
  // 展示记忆详情
}
```

### Step 4: 汇总展示

输出格式示例：

```
📋 当前项目上下文

🎯 进行中的计划 (2个)
  1. [plan-user-auth] 用户认证系统重构 (进度: 45%)
  2. [plan-api-v2] API 2.0 版本开发 (进度: 20%)

✅ 待完成的任务 (5个)
  🔴 [todo-fix-login] 修复登录 Bug (优先级: 4)
  🟠 [todo-add-test] 添加单元测试 (优先级: 3)
  ...

📚 相关记忆 (3条)
  1. [mem-jwt-decision] JWT vs Session 技术选型
  2. [mem-api-design] API 设计规范
  ...
```

## MCP 工具使用

- `plan_list` - 列出计划
- `plan_get` - 获取计划详情
- `todo_list` - 列出待办
- `memory_search` - 搜索记忆
- `memory_get` - 获取记忆详情

详见：[工作流参考](./references/workflow.md)
