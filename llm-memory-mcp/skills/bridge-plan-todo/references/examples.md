# Bridge Plan Todo - 使用示例

## 场景 1：创建计划并生成任务

**用户需求**：创建用户认证重构计划并自动生成关联任务

### 步骤 1：创建 Plan

```javascript
await plan_create({
  code: "plan-auth-refactor",
  title: "用户认证系统重构",
  description: "采用 JWT 机制，支持 refresh token，提升安全性",
  content: `
# 用户认证系统重构实施计划

## 阶段 1: 数据库设计 (Day 1-2)
- 设计 users 表结构
- 设计 refresh_tokens 表结构
- 添加必要的索引和约束

## 阶段 2: JWT 核心实现 (Day 2-3)
- 实现 JWT 生成逻辑
- 实现 JWT 验证逻辑
- 实现 refresh token 机制

## 阶段 3: API 端点开发 (Day 3-4)
- POST /api/auth/register
- POST /api/auth/login
- POST /api/auth/refresh
- POST /api/auth/logout
  `,
  scope: "personal"
});
```

### 步骤 2：调用 bridge-plan-todo 生成任务

```javascript
// 解析 Plan 内容
const plan = await plan_get({ code: "plan-auth-refactor" });
const phases = parsePlanContent(plan.content);

// 生成 Todos
const todos = [];
phases.forEach((phase, phaseIndex) => {
  phase.steps.forEach((step, stepIndex) => {
    todos.push({
      code: `todo-auth-refactor-${phaseIndex + 1}-${stepIndex + 1}`,
      title: step.title,
      description: `${plan.title} - Phase ${phaseIndex + 1}`,
      priority: phaseIndex === 0 ? 4 : phaseIndex === 1 ? 3 : 2
    });
  });
});

// 批量创建
await todo_batch_create({ items: todos, scope: "personal" });
```

### 结果

**生成的 Todos**：

| Code | Title | Priority |
|------|-------|----------|
| todo-auth-refactor-1-1 | 设计 users 表结构 | 4 (🔴) |
| todo-auth-refactor-1-2 | 设计 refresh_tokens 表结构 | 4 (🔴) |
| todo-auth-refactor-1-3 | 添加必要的索引和约束 | 4 (🔴) |
| todo-auth-refactor-2-1 | 实现 JWT 生成逻辑 | 3 (🟠) |
| todo-auth-refactor-2-2 | 实现 JWT 验证逻辑 | 3 (🟠) |
| todo-auth-refactor-2-3 | 实现 refresh token 机制 | 3 (🟠) |
| todo-auth-refactor-3-1 | POST /api/auth/register | 2 (🟡) |
| todo-auth-refactor-3-2 | POST /api/auth/login | 2 (🟡) |
| todo-auth-refactor-3-3 | POST /api/auth/refresh | 2 (🟡) |
| todo-auth-refactor-3-4 | POST /api/auth/logout | 2 (🟡) |

---

## 场景 2：完成任务并同步进度

**用户需求**：完成 Phase 1 的任务，并自动更新 Plan 进度

### 步骤 1：完成 Phase 1 任务

```javascript
await todo_batch_complete({
  codes: [
    "todo-auth-refactor-1-1",
    "todo-auth-refactor-1-2",
    "todo-auth-refactor-1-3"
  ]
});
```

### 步骤 2：调用 bridge-plan-todo 同步进度

```javascript
// 获取所有关联 todos
const allTodos = await todo_list({ scope: "all" });
const relatedTodos = allTodos.filter(t =>
  t.code.startsWith("todo-auth-refactor-")
);

// 计算进度
const completedCount = relatedTodos.filter(t => t.status === 2).length;
const progress = Math.round((completedCount / relatedTodos.length) * 100);
// 3 / 10 = 30%

// 更新 Plan
await plan_update({
  code: "plan-auth-refactor",
  progress: 30
});
```

### 结果

**Plan 状态变化**：

| 字段 | 更新前 | 更新后 |
|------|--------|--------|
| progress | 0 | 30 |
| status | pending | in_progress |

---

## 场景 3：分阶段推进项目

**用户需求**：逐步完成各阶段任务，跟踪整体进度

### 完成 Phase 1（3/10 = 30%）

```javascript
await todo_batch_complete({
  codes: [
    "todo-auth-refactor-1-1",
    "todo-auth-refactor-1-2",
    "todo-auth-refactor-1-3"
  ]
});
await syncProgress("auth-refactor");  // progress: 30%
```

### 完成 Phase 2（6/10 = 60%）

```javascript
await todo_batch_complete({
  codes: [
    "todo-auth-refactor-2-1",
    "todo-auth-refactor-2-2",
    "todo-auth-refactor-2-3"
  ]
});
await syncProgress("auth-refactor");  // progress: 60%
```

### 完成 Phase 3（10/10 = 100%）

```javascript
await todo_batch_complete({
  codes: [
    "todo-auth-refactor-3-1",
    "todo-auth-refactor-3-2",
    "todo-auth-refactor-3-3",
    "todo-auth-refactor-3-4"
  ]
});
await syncProgress("auth-refactor");  // progress: 100%
```

### 进度追踪

| 阶段 | 完成后进度 | Plan 状态 |
|------|-----------|-----------|
| Phase 1 完成 | 30% | in_progress |
| Phase 2 完成 | 60% | in_progress |
| Phase 3 完成 | 100% | completed |

---

## 场景 4：处理任务取消

**用户需求**：取消部分任务后重新计算进度

### 取消 Phase 3 的两个任务

```javascript
await todo_batch_cancel({
  codes: [
    "todo-auth-refactor-3-3",
    "todo-auth-refactor-3-4"
  ]
});
```

### 重新计算进度

```javascript
// 获取所有 todos
const allTodos = await todo_list({ scope: "all" });
const relatedTodos = allTodos.filter(t =>
  t.code.startsWith("todo-auth-refactor-")
);

// 排除已取消的
const validTodos = relatedTodos.filter(t => t.status !== 3);
const completedTodos = validTodos.filter(t => t.status === 2);

// 重新计算：6 completed / 8 valid = 75%
const progress = Math.round((completedTodos.length / validTodos.length) * 100);

await plan_update({ code: "plan-auth-refactor", progress: 75 });
```

---

## 辅助函数

### syncProgress 完整实现

```javascript
async function syncProgress(planCodeSuffix) {
  const planCode = `plan-${planCodeSuffix}`;

  // 获取所有 todos
  const allTodos = await todo_list({ scope: "all" });

  // 筛选关联的 todos
  const relatedTodos = allTodos.filter(t =>
    t.code.startsWith(`todo-${planCodeSuffix}-`)
  );

  if (relatedTodos.length === 0) {
    console.log(`No todos found for plan: ${planCode}`);
    return;
  }

  // 排除已取消的
  const validTodos = relatedTodos.filter(t => t.status !== 3);
  const completedTodos = validTodos.filter(t => t.status === 2);

  // 计算进度
  const progress = validTodos.length > 0
    ? Math.round((completedTodos.length / validTodos.length) * 100)
    : 0;

  // 更新 Plan
  await plan_update({ code: planCode, progress });

  console.log(`Plan ${planCode} progress updated to ${progress}%`);
}
```

### parsePlanContent 完整实现

```javascript
function parsePlanContent(content) {
  const phases = [];
  const lines = content.split('\n');
  let currentPhase = null;

  for (const line of lines) {
    // 匹配阶段标题
    const phaseMatch = line.match(/^##\s*阶段\s*(\d+)[:\s]*(.+)?/i) ||
                       line.match(/^##\s*Phase\s*(\d+)[:\s]*(.+)?/i);

    if (phaseMatch) {
      if (currentPhase && currentPhase.steps.length > 0) {
        phases.push(currentPhase);
      }
      currentPhase = {
        number: parseInt(phaseMatch[1]),
        title: phaseMatch[2]?.trim() || `阶段 ${phaseMatch[1]}`,
        steps: []
      };
      continue;
    }

    // 匹配步骤
    const stepMatch = line.match(/^[-*]\s+(.+)/);
    if (stepMatch && currentPhase) {
      currentPhase.steps.push({
        title: stepMatch[1].trim()
      });
    }
  }

  if (currentPhase && currentPhase.steps.length > 0) {
    phases.push(currentPhase);
  }

  return phases;
}
```
