---
name: plan-mode-context
description: "Plan 模式上下文加载器 - 进入 Plan 模式时自动加载。运行 llm-memory 命令读取 plan/todo/memory 检查未完成任务和相关记忆，帮助制定更好的计划。"
---

# Plan Mode Context Loader

进入 Plan 模式时自动加载项目上下文，帮助制定更好的计划。

## 触发条件

- 进入 Plan 模式
- 用户说"开始规划"、"制定计划"
- 系统提示 `Plan mode is active`

## 操作流程

### Step 1: 加载现有计划

```bash
# 获取所有计划
llm-memory plan list

# 获取计划详情
llm-memory plan get --code plan-xxx
```

### Step 2: 加载待办任务

```bash
# 获取所有待办
llm-memory todo list
```

### Step 3: 搜索相关记忆

```bash
# 搜索记忆
llm-memory memory search --keyword "关键词"

# 获取记忆详情
llm-memory memory get --code mem-xxx
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
  ...

📚 相关记忆 (3条)
  1. [mem-jwt-decision] JWT vs Session 技术选型
  ...
```

## CLI 命令清单

- `llm-memory plan list` - 列出计划
- `llm-memory plan get --code xxx` - 获取计划详情
- `llm-memory todo list` - 列出待办
- `llm-memory memory search --keyword xxx` - 搜索记忆
- `llm-memory memory get --code xxx` - 获取记忆详情

详见：[工作流参考](./references/workflow.md)
