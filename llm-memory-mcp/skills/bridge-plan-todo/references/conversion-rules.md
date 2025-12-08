# Plan → Todo 转换规则

## 内容解析规则

### Plan Content 格式要求

推荐使用以下 Markdown 格式编写 Plan 内容：

```markdown
# 计划标题

## 阶段 1: 阶段标题
- 步骤 1 描述
- 步骤 2 描述

## 阶段 2: 阶段标题
- 步骤 1 描述
- 步骤 2 描述

## 阶段 3: 阶段标题
- 步骤 1 描述
```

### 解析逻辑

```javascript
function parsePlanContent(content) {
  const phases = [];
  const lines = content.split('\n');

  let currentPhase = null;

  for (const line of lines) {
    // 匹配阶段标题：## 阶段 N: 标题
    const phaseMatch = line.match(/^##\s*阶段\s*(\d+)[:\s]*(.+)?/i);
    if (phaseMatch) {
      if (currentPhase) phases.push(currentPhase);
      currentPhase = {
        number: parseInt(phaseMatch[1]),
        title: phaseMatch[2]?.trim() || `阶段 ${phaseMatch[1]}`,
        steps: []
      };
      continue;
    }

    // 匹配步骤：- 步骤描述
    const stepMatch = line.match(/^[-*]\s+(.+)/);
    if (stepMatch && currentPhase) {
      currentPhase.steps.push({
        title: stepMatch[1].trim()
      });
    }
  }

  if (currentPhase) phases.push(currentPhase);
  return phases;
}
```

---

## Code 生成规则

### 命名格式

```
todo-<plan-code>-<phase>-<step>
```

### 生成逻辑

```javascript
function generateTodoCodes(planCode, phases) {
  const todos = [];

  phases.forEach((phase, phaseIndex) => {
    phase.steps.forEach((step, stepIndex) => {
      todos.push({
        code: `todo-${planCode}-${phaseIndex + 1}-${stepIndex + 1}`,
        title: step.title,
        description: `${phase.title} - Step ${stepIndex + 1}`,
        priority: getPriorityByPhase(phaseIndex + 1)
      });
    });
  });

  return todos;
}
```

### 示例

**Plan Code**: `plan-auth-refactor`

**生成的 Todo Codes**:
```
todo-auth-refactor-1-1  (Phase 1, Step 1)
todo-auth-refactor-1-2  (Phase 1, Step 2)
todo-auth-refactor-2-1  (Phase 2, Step 1)
todo-auth-refactor-2-2  (Phase 2, Step 2)
todo-auth-refactor-3-1  (Phase 3, Step 1)
```

---

## 优先级分配规则

### 默认规则

| 阶段 | 优先级 | 说明 |
|------|--------|------|
| Phase 1 | 4 (🔴 紧急) | 首要任务，需要立即开始 |
| Phase 2 | 3 (🟠 高) | 次要任务，等待 Phase 1 完成 |
| Phase 3+ | 2 (🟡 中) | 后续任务，默认优先级 |

### 实现代码

```javascript
function getPriorityByPhase(phaseNumber) {
  switch (phaseNumber) {
    case 1: return 4;  // 紧急
    case 2: return 3;  // 高
    default: return 2; // 中
  }
}
```

### 自定义优先级

也可以在 Plan Content 中指定优先级：

```markdown
## 阶段 1: 数据库设计 [priority: 4]
- 设计 users 表结构
- 设计 tokens 表结构

## 阶段 2: 核心实现 [priority: 3]
- 实现认证逻辑
```

---

## 完整转换示例

### 输入：Plan Content

```markdown
# 用户认证系统重构

## 阶段 1: 数据库设计
- 设计 users 表结构
- 设计 refresh_tokens 表结构
- 编写迁移脚本

## 阶段 2: JWT 核心实现
- 实现 JWT 生成逻辑
- 实现 JWT 验证逻辑
- 实现 refresh token 机制

## 阶段 3: API 开发
- POST /api/auth/register
- POST /api/auth/login
- POST /api/auth/refresh
```

### 输出：Todos

```javascript
[
  // Phase 1 - Priority 4
  { code: "todo-auth-refactor-1-1", title: "设计 users 表结构", priority: 4 },
  { code: "todo-auth-refactor-1-2", title: "设计 refresh_tokens 表结构", priority: 4 },
  { code: "todo-auth-refactor-1-3", title: "编写迁移脚本", priority: 4 },

  // Phase 2 - Priority 3
  { code: "todo-auth-refactor-2-1", title: "实现 JWT 生成逻辑", priority: 3 },
  { code: "todo-auth-refactor-2-2", title: "实现 JWT 验证逻辑", priority: 3 },
  { code: "todo-auth-refactor-2-3", title: "实现 refresh token 机制", priority: 3 },

  // Phase 3 - Priority 2
  { code: "todo-auth-refactor-3-1", title: "POST /api/auth/register", priority: 2 },
  { code: "todo-auth-refactor-3-2", title: "POST /api/auth/login", priority: 2 },
  { code: "todo-auth-refactor-3-3", title: "POST /api/auth/refresh", priority: 2 }
]
```

---

## 边界情况处理

### 空内容

```javascript
if (!content || content.trim() === '') {
  return [];  // 返回空数组
}
```

### 无阶段标记

如果 Plan 内容没有阶段标记，将所有列表项视为单个阶段：

```javascript
if (phases.length === 0) {
  // 解析所有列表项作为 Phase 1
  const steps = parseAllListItems(content);
  return [{
    number: 1,
    title: "默认阶段",
    steps: steps
  }];
}
```

### 步骤为空

```javascript
// 跳过没有步骤的阶段
const validPhases = phases.filter(p => p.steps.length > 0);
```
