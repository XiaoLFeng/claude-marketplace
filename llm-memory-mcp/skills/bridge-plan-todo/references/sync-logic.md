# Todo → Plan 进度同步逻辑

## 同步机制

### 触发时机

当以下操作发生时，需要同步 Plan 进度：

1. `todo_batch_complete` - 批量完成待办
2. `todo_batch_start` - 批量开始待办
3. `todo_batch_update` - 批量更新待办状态
4. `todo_batch_cancel` - 批量取消待办

### 同步流程

```
Todo 状态变更
      ↓
从 Todo Code 解析 Plan Code
      ↓
获取所有关联 Todos
      ↓
计算完成比例
      ↓
更新 Plan Progress
      ↓
检查 Plan 状态变更
```

---

## 进度计算

### 基本公式

```
progress = (completedTodos / totalTodos) * 100
```

### 实现代码

```javascript
function calculateProgress(planCode, allTodos) {
  // 1. 筛选关联的 todos
  const relatedTodos = allTodos.filter(t =>
    t.code.startsWith(`todo-${planCode}-`)
  );

  // 2. 处理边界情况
  if (relatedTodos.length === 0) {
    return 0;
  }

  // 3. 统计已完成数量
  const completedTodos = relatedTodos.filter(t =>
    t.status === 2  // completed
  );

  // 4. 计算进度
  const progress = Math.round(
    (completedTodos.length / relatedTodos.length) * 100
  );

  return progress;
}
```

### 进度示例

| 总 Todos | 已完成 | 进度 |
|----------|--------|------|
| 10 | 0 | 0% |
| 10 | 3 | 30% |
| 10 | 5 | 50% |
| 10 | 8 | 80% |
| 10 | 10 | 100% |

---

## 状态映射

### Progress 到 Status 的映射

```javascript
function getStatusByProgress(progress) {
  if (progress === 0) return 'pending';
  if (progress === 100) return 'completed';
  return 'in_progress';
}
```

| Progress | Status |
|----------|--------|
| 0 | pending（待开始）|
| 1-99 | in_progress（进行中）|
| 100 | completed（已完成）|

### 状态自动转换

Plan 的状态会根据 progress 自动转换：

```javascript
async function syncProgress(planCode) {
  const allTodos = await todo_list({ scope: "all" });
  const progress = calculateProgress(planCode, allTodos);

  // 更新进度（状态自动转换）
  await plan_update({
    code: planCode,
    progress: progress
  });
}
```

---

## Plan Code 解析

### 从 Todo Code 提取 Plan Code

```javascript
function extractPlanCode(todoCode) {
  // todo-auth-refactor-1-1 → plan-auth-refactor
  const match = todoCode.match(/^todo-(.+)-\d+-\d+$/);
  if (match) {
    return `plan-${match[1]}`;
  }
  return null;
}
```

### 示例

| Todo Code | Plan Code |
|-----------|-----------|
| `todo-auth-refactor-1-1` | `plan-auth-refactor` |
| `todo-api-redesign-2-3` | `plan-api-redesign` |
| `todo-mobile-app-1-1` | `plan-mobile-app` |

---

## 完整同步示例

### 场景：完成 Phase 1 的任务

**初始状态**：

- Plan: `plan-auth-refactor`, progress: 0%
- Todos: 9 个，全部 pending

**操作**：完成 Phase 1 的 3 个 todos

```javascript
// 1. 批量完成
await todo_batch_complete({
  codes: [
    "todo-auth-refactor-1-1",
    "todo-auth-refactor-1-2",
    "todo-auth-refactor-1-3"
  ]
});

// 2. 同步进度
const allTodos = await todo_list({ scope: "all" });
const progress = calculateProgress("auth-refactor", allTodos);
// progress = (3/9) * 100 = 33%

// 3. 更新 Plan
await plan_update({
  code: "plan-auth-refactor",
  progress: 33
});
```

**结果状态**：

- Plan: `plan-auth-refactor`, progress: 33%, status: in_progress
- Todos: 3 个 completed, 6 个 pending

---

## 边界情况处理

### 无关联 Todos

```javascript
if (relatedTodos.length === 0) {
  console.log(`No todos found for plan: ${planCode}`);
  return;  // 不更新进度
}
```

### 已取消的 Todos

已取消的 todos (status: 3) 不计入总数：

```javascript
// 只统计有效的 todos
const validTodos = relatedTodos.filter(t =>
  t.status !== 3  // 排除已取消
);

const completedTodos = validTodos.filter(t =>
  t.status === 2
);

const progress = Math.round(
  (completedTodos.length / validTodos.length) * 100
);
```

### Plan 不存在

```javascript
try {
  await plan_update({ code: planCode, progress });
} catch (error) {
  if (error.message.includes('not found')) {
    console.log(`Plan not found: ${planCode}`);
    return;
  }
  throw error;
}
```

---

## 最佳实践

### 1. 批量操作后统一同步

```javascript
// 批量完成后只同步一次
await todo_batch_complete({ codes: [...] });
await syncProgress(planCode);  // 只调用一次
```

### 2. 事务性处理

```javascript
// 如果同步失败，记录日志但不回滚 todo 状态
try {
  await syncProgress(planCode);
} catch (error) {
  console.error(`Failed to sync progress: ${error.message}`);
  // 不抛出错误，todo 状态已经更新
}
```

### 3. 定期校准

```javascript
// 定期检查并校准所有进行中的 plan
async function calibrateAllPlans() {
  const plans = await plan_list({ scope: "all" });
  const inProgressPlans = plans.filter(p => p.status === 'in_progress');

  for (const plan of inProgressPlans) {
    await syncProgress(plan.code.replace('plan-', ''));
  }
}
```
