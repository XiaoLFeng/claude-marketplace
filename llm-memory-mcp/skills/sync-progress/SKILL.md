---
name: sync-progress
description: "Progress syncer - Sync task completion to project progress. Calculates and updates plan progress based on todo completion. Use when: batch todos completed, update associated plan progress. Not for: managing todo/plan separately."
---

# Sync Progress

任务完成后同步到项目进度，保持 Plan 和 Todo 的一致性。

## 触发条件

- 完成一批 todo 后需要更新 plan 进度
- 批量完成任务后计算整体进度
- 用户说"同步进度"、"更新计划进度"
- Plan 下的子任务完成时

## 不触发条件

- 单独创建/管理 todo → 使用 task-* 系列
- 单独创建/管理 plan → 使用 manage-project
- 不涉及 plan-todo 关联的操作

## 同步流程

### Step 1: 获取数据

```javascript
const todos = await todo_list({ scope: "all" });
const plans = await plan_list({ scope: "all" });
```

### Step 2: 识别关联关系

根据 code 命名约定识别关联：

```
plan-user-auth          ← 计划
  todo-auth-design      ← 关联任务
  todo-auth-login       ← 关联任务
  todo-auth-test        ← 关联任务
```

关联规则：
- `plan-<项目>` 关联 `todo-<项目>-*`

### Step 3: 计算新进度

```javascript
// 获取计划下的所有任务
const planTodos = todos.filter(t => t.code.startsWith('todo-auth'));
const completedCount = planTodos.filter(t => t.status === 2).length;
const inProgressCount = planTodos.filter(t => t.status === 1).length;
const totalCount = planTodos.length;

// 计算进度（进行中算 50%）
const progress = Math.round(
  ((completedCount + inProgressCount * 0.5) / totalCount) * 100
);
```

### Step 4: 更新计划进度

```javascript
await plan_update({
  code: "plan-user-auth",
  progress: progress
});
```

## 进度计算规则

### 基础计算

```
进度 = (已完成数 / 总数) × 100%

示例:
  总任务: 4
  已完成: 2
  进度: 2/4 × 100 = 50%
```

### 含进行中状态

```
进度 = ((已完成数 + 进行中数 × 0.5) / 总数) × 100%

示例:
  总任务: 4
  已完成: 1
  进行中: 2
  进度: (1 + 2×0.5) / 4 × 100 = 50%
```

## 关联识别策略

### 策略 1: Code 前缀匹配

```
plan-auth           →  todo-auth-*
plan-api-v2         →  todo-api-v2-*
plan-refactor       →  todo-refactor-*
```

### 策略 2: 提取项目名

```javascript
// 从 plan code 提取项目名
const planCode = "plan-user-auth";
const project = planCode.replace("plan-", "");  // "user-auth"

// 匹配 todo
const relatedTodos = todos.filter(t =>
  t.code.startsWith(`todo-${project.split("-")[0]}`)
);
```

## 边界情况处理

### 无关联任务

```
Plan: plan-xxx 没有关联的 todo

处理: 跳过，不修改进度
```

### 所有任务完成

```
Plan: plan-xxx
所有 todo 都完成

处理: 进度设为 100%，状态变为 completed
```

### 没有任务

```
Plan: plan-xxx
没有相关 todo

处理: 进度保持不变
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
  → [todo-auth-jwt] JWT 集成 (进行中)
  ○ [todo-auth-test] 集成测试 (待处理)
```

## MCP 工具

- `todo_list` - 获取任务列表
- `plan_list` - 获取计划列表
- `plan_update` - 更新计划进度

详见：[同步工作流](./references/workflow.md)
