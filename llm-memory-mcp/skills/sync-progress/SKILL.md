---
name: sync-progress
description: "进度同步器 - 任务完成后同步到项目进度。适用：完成 todo 后需要更新关联 plan 的进度、批量完成任务后计算整体进度。不适用：单独管理 todo/plan 用各自 skill。"
---

# Sync Progress

任务完成后同步到项目进度，保持 Plan 和 Todo 的一致性。

## 触发条件

- 完成一批 todo 后需要更新 plan 进度
- 批量完成任务后计算整体进度
- 用户说"同步进度"、"更新计划进度"
- Plan 下的子任务完成时

## 不触发条件

- 单独创建/管理 todo → 使用 manage-tasks
- 单独创建/管理 plan → 使用 manage-project
- 不涉及 plan-todo 关联的操作

## 同步流程

### Step 1: 获取已完成的任务

```javascript
const todos = await todo_list({ scope: "all" });
const completed = todos.filter(t => t.status === 2);
```

### Step 2: 识别关联的计划

根据 code 命名约定识别关联：

```
plan-user-auth          ← 计划
  todo-auth-design      ← 关联任务
  todo-auth-login       ← 关联任务
  todo-auth-test        ← 关联任务
```

### Step 3: 计算新进度

```javascript
// 获取计划下的所有任务
const planTodos = todos.filter(t => t.code.includes('auth'));
const completedCount = planTodos.filter(t => t.status === 2).length;
const totalCount = planTodos.length;

// 计算进度百分比
const progress = Math.round((completedCount / totalCount) * 100);
```

### Step 4: 更新计划进度

```javascript
await plan_update({
  code: "plan-user-auth",
  progress: progress
});
```

## 同步策略

### 自动关联规则

```
plan-<项目>
  └─ todo-<项目>-xxx    (匹配 <项目> 部分)

示例：
plan-user-auth
  └─ todo-auth-design
  └─ todo-auth-login
  └─ todo-auth-register
```

### 进度计算公式

```
进度 = (已完成任务数 / 总任务数) × 100

示例：
  总任务: 5
  已完成: 3
  进度: 3/5 × 100 = 60%
```

## 输出示例

```
🔄 进度同步完成

📋 [plan-user-auth] 用户认证系统
   任务进度: 3/5 完成
   计划进度: 60% (↑15%)
   ████████████░░░░░░░░

已同步的任务：
  ✓ [todo-auth-design] 设计认证流程
  ✓ [todo-auth-login] 实现登录
  ✓ [todo-auth-register] 实现注册
```

## MCP 工具使用

- `todo_list` - 获取任务列表
- `plan_get` - 获取计划详情
- `plan_update` - 更新计划进度

详见：[工作流参考](./references/workflow.md)
