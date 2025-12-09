---
name: workflow-orchestrator
description: "Workflow orchestrator - Assign agent responsibilities when planning tasks, guide Task Agent collaboration. Use when: complex tasks need multi-agent parallel execution, planning phase task assignment. Auto-annotates responsible agent in todo description."
---

# Workflow Orchestrator

工作流编排器，引导 AI 在规划和执行时正确使用 Task Agent 协同工作。

## 触发条件

- 复杂任务需要多 Agent 并行处理
- 规划阶段需要分配任务给不同 Agent
- 用户说"并行处理"、"分配任务"、"多个 Agent"
- 任务可以拆分为独立的并行单元

## 不触发条件

- 简单任务单 Agent 可完成
- 已经在执行阶段（用 task-start/complete/add）
- 只是查看状态（用 load-context）

## 核心职责

### 1. 任务依赖分析

识别任务间的依赖关系，确定哪些可以并行：

```
任务分析:
├── todo-auth-login      (无依赖) → 可并行
├── todo-auth-register   (无依赖) → 可并行
├── todo-auth-jwt        (依赖 login) → 串行
└── todo-auth-test       (依赖全部) → 最后执行
```

### 2. Agent 分配策略

在 todo 的 description 中标注负责的 Agent：

```
格式: [Agent-X] 任务描述
示例: [Task-A] 实现用户登录 API
```

### 3. 文件隔离原则

确保不同 Agent 处理不同文件，避免冲突：

```
Agent-A: src/auth/login.ts, src/auth/jwt.ts
Agent-B: src/auth/register.ts, src/auth/validate.ts
Main:    src/auth/index.ts (汇总)
```

## 操作流程

### Step 1: 分析任务复杂度

判断是否需要多 Agent 协同。

### Step 2: 创建带 Agent 标注的 Todos

```bash
# 批量创建任务
llm-memory todo add todo-auth-login "实现登录功能" --description "[Task-A] 实现用户登录 API"
llm-memory todo add todo-auth-register "实现注册功能" --description "[Task-B] 实现用户注册流程"
llm-memory todo add todo-auth-test "集成测试" --description "[Main] 等待 Task-A/B 完成后执行"
```

### Step 3: 生成 Task Agent Prompt

为每个 Task Agent 生成标准化的 prompt：

```markdown
你负责完成以下任务：

**任务**: 实现用户登录功能
**Todo Code**: todo-auth-login
**负责文件**: src/auth/login.ts

完成要求：
1. [具体要求列表]

**重要指令**:
- 开始前调用 `task-start` skill，参数: todo-auth-login
- 完成后调用 `task-complete` skill，参数: todo-auth-login
- 如发现需要额外任务，调用 `task-add` skill 追加
- 只修改你负责的文件，避免冲突
```

## 并发 Agent 冲突处理

### 原则

1. **文件隔离**: 不同 Agent 处理不同文件
2. **状态隔离**: 每个 Agent 只管理自己的 todo
3. **遇到冲突**: 跳过，在返回结果中说明

### Task Agent 冲突处理指令

在 Task Agent prompt 中包含：

```markdown
**冲突处理**:
如果遇到文件被其他 Agent 修改导致无法编辑：
1. 跳过该文件的修改
2. 在返回结果中说明: "文件 xxx 需要后续处理"
3. 继续完成其他可完成的工作
4. 仍然调用 task-complete 标记任务完成
```

## 输出示例

```
🎯 工作流编排完成

📋 任务分配:
┌─────────────────────────────────────────────┐
│ Plan: plan-user-auth                        │
├─────────────────────────────────────────────┤
│ [Task-A] todo-auth-login     → 登录功能     │
│ [Task-A] todo-auth-jwt       → JWT 集成     │
│ [Task-B] todo-auth-register  → 注册功能     │
│ [Main]   todo-auth-test      → 集成测试     │
└─────────────────────────────────────────────┘

🚀 执行顺序:
  1. 并行: Task-A (login) + Task-B (register)
  2. 串行: Task-A (jwt) - 依赖 login
  3. 最后: Main (test) - 依赖全部
```

## CLI 命令

- `llm-memory todo add` - 创建任务
- `llm-memory todo list` - 查看任务列表
- `llm-memory plan add` - 创建计划

详见：
- [Agent 分配指南](./references/agent-assignment.md)
- [Prompt 模板](./references/prompt-templates.md)
