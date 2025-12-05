# Plan MCP 工具参考

本文档详细说明 Plan MCP 提供的所有工具，包括参数、返回格式、示例和错误处理。

## 工具清单

| 工具 | 功能 | 用途 |
|------|------|------|
| `plan_list` | 列出计划 | 查看所有或特定作用域的计划 |
| `plan_create` | 创建计划 | 创建新的计划项目 |
| `plan_get` | 获取计划详情 | 查看单个计划的完整内容 |
| `plan_update` | 更新计划 | 修改标题、内容、进度等 |

---

## 1. plan_list - 列出计划

### 功能描述

列出符合条件的计划列表，支持按作用域过滤。

### 参数

| 参数 | 类型 | 必需 | 说明 | 示例 |
|------|------|------|------|------|
| `scope` | string | 否 | 作用域过滤：`personal`/`group`/`all` | `"all"` |
| `status` | string | 否 | 状态过滤：`pending`/`in_progress`/`completed` | `"in_progress"` |

### 返回格式

```javascript
{
  "success": true,
  "data": [
    {
      "id": "plan-001",
      "code": "plan-user-auth",
      "title": "用户认证系统重构",
      "description": "采用 JWT 机制，支持 refresh token",
      "status": "in_progress",
      "progress": 50,
      "scope": "personal",
      "created_at": "2025-12-01T10:30:00Z",
      "updated_at": "2025-12-04T15:45:00Z"
    }
  ],
  "total": 1
}
```

### 使用示例

```javascript
// 获取所有计划
plan_list({ scope: "all" })

// 获取个人进行中的计划
plan_list({ scope: "personal", status: "in_progress" })

// 获取已完成的计划
plan_list({ status: "completed" })
```

### 错误处理

```javascript
// 无效的作用域
{ "success": false, "error": "Invalid scope: must be 'personal', 'group', or 'all'" }

// 无效的状态
{ "success": false, "error": "Invalid status: must be 'pending', 'in_progress', or 'completed'" }
```

---

## 2. plan_create - 创建计划

### 功能描述

创建新的计划，包括标题、描述和详细内容。

### 参数

| 参数 | 类型 | 必需 | 说明 | 示例 |
|------|------|------|------|------|
| `code` | string | 是 | 计划标识符，全小写+连字符，≥3字符 | `"plan-auth-refactor"` |
| `title` | string | 是 | 计划标题 | `"用户认证系统重构"` |
| `description` | string | 是 | 简要描述（摘要） | `"采用 JWT 机制，支持 refresh token"` |
| `content` | string | 是 | 详细内容（支持 Markdown） | `"# 详细计划\n\n- 步骤1\n- 步骤2"` |
| `scope` | string | 否 | 作用域：`personal`（默认）或 `group` | `"personal"` |

### 返回格式

```javascript
{
  "success": true,
  "data": {
    "id": "plan-001",
    "code": "plan-auth-refactor",
    "title": "用户认证系统重构",
    "description": "采用 JWT 机制，支持 refresh token",
    "content": "# 详细计划\n\n...",
    "status": "pending",
    "progress": 0,
    "scope": "personal",
    "created_at": "2025-12-05T10:30:00Z",
    "updated_at": "2025-12-05T10:30:00Z"
  }
}
```

### 使用示例

```javascript
// 创建认证系统重构计划
plan_create({
  code: "plan-auth-refactor",
  title: "用户认证系统重构",
  description: "采用 JWT 机制，支持 refresh token，提升安全性",
  content: `# 用户认证系统重构计划

## 阶段 1: 数据库设计 (Day 1-2)
- 设计 users 表结构
- 设计 refresh_tokens 表结构

## 阶段 2: JWT 实现 (Day 2-3)
- 实现 JWT 生成逻辑
- 实现 JWT 验证逻辑

## 阶段 3: API 开发 (Day 3-4)
- POST /api/auth/register
- POST /api/auth/login
- POST /api/auth/refresh`,
  scope: "personal"
})

// 创建团队共享的计划
plan_create({
  code: "plan-database-migration",
  title: "数据库迁移计划",
  description: "迁移到 PostgreSQL v14 并优化查询性能",
  content: "...",
  scope: "group"
})
```

### Code 格式规则

**必须遵循的规则：**
- 全小写字母（a-z）
- 可包含连字符（-）
- 开头和末尾必须是字母
- 最少 3 个字符

**有效示例：**
- `plan-user-auth`
- `plan-database-migration`
- `plan-api-redesign`

**无效示例：**
- `Plan_001` ❌（大写）
- `plan_` ❌（末尾不是字母）
- `-plan` ❌（开头不是字母）
- `pl` ❌（少于 3 字符）

### 错误处理

```javascript
// Code 格式错误
{ "success": false, "error": "Invalid code format: must be lowercase, contain only letters and hyphens, start and end with a letter, and be at least 3 characters" }

// Code 重复
{ "success": false, "error": "Code already exists: plan-auth-refactor" }

// 缺少必需参数
{ "success": false, "error": "Missing required parameter: content" }

// 无效作用域
{ "success": false, "error": "Invalid scope: must be 'personal' or 'group'" }
```

---

## 3. plan_get - 获取计划详情

### 功能描述

获取指定计划的完整信息，包括所有字段和时间戳。

### 参数

| 参数 | 类型 | 必需 | 说明 | 示例 |
|------|------|------|------|------|
| `code` | string | 是 | 计划标识符 | `"plan-auth-refactor"` |

### 返回格式

```javascript
{
  "success": true,
  "data": {
    "id": "plan-001",
    "code": "plan-auth-refactor",
    "title": "用户认证系统重构",
    "description": "采用 JWT 机制，支持 refresh token",
    "content": "# 用户认证系统重构计划\n\n## 阶段 1...",
    "status": "in_progress",
    "progress": 50,
    "scope": "personal",
    "created_at": "2025-12-01T10:30:00Z",
    "updated_at": "2025-12-04T15:45:00Z",
    "completed_at": null
  }
}
```

### 使用示例

```javascript
// 获取认证系统计划的详情
plan_get({ code: "plan-auth-refactor" })

// 在更新前获取当前状态
const planInfo = plan_get({ code: "plan-database-migration" })
console.log(`当前进度: ${planInfo.data.progress}%`)
console.log(`状态: ${planInfo.data.status}`)
```

### 错误处理

```javascript
// 计划不存在
{ "success": false, "error": "Plan not found: plan-nonexistent" }

// Code 格式错误
{ "success": false, "error": "Invalid code format" }
```

---

## 4. plan_update - 更新计划

### 功能描述

更新计划的各个字段，包括进度、状态、标题、内容等。

### 参数

| 参数 | 类型 | 必需 | 说明 | 示例 |
|------|------|------|------|------|
| `code` | string | 是 | 计划标识符 | `"plan-auth-refactor"` |
| `title` | string | 否 | 新标题 | `"用户认证系统重构 v2"` |
| `description` | string | 否 | 新描述 | `"新的实现方案"` |
| `content` | string | 否 | 新内容（Markdown） | `"# 更新的计划..."` |
| `progress` | number | 否 | 进度百分比（0-100） | `75` |

### 返回格式

```javascript
{
  "success": true,
  "data": {
    "id": "plan-001",
    "code": "plan-auth-refactor",
    "title": "用户认证系统重构",
    "status": "in_progress",
    "progress": 75,
    "updated_at": "2025-12-05T16:20:00Z"
  }
}
```

### 进度和状态管理

系统自动根据 `progress` 值管理状态：

```javascript
progress = 0        → status = "pending"    （待开始）
progress = 1-99     → status = "in_progress"（进行中）
progress = 100      → status = "completed"  （已完成）
```

### 使用示例

```javascript
// 更新进度为 50%
plan_update({
  code: "plan-auth-refactor",
  progress: 50
})
// 自动转换为 in_progress 状态

// 更新进度为 100%（完成）
plan_update({
  code: "plan-auth-refactor",
  progress: 100
})
// 自动转换为 completed 状态

// 更新标题和描述
plan_update({
  code: "plan-auth-refactor",
  title: "用户认证系统重构（优化版）",
  description: "改进的 JWT 实现方案"
})

// 更新内容
plan_update({
  code: "plan-auth-refactor",
  content: `# 更新的实施计划

## 新增内容
- 支持 OAuth2 集成
- 多因素认证
`
})

// 组合更新
plan_update({
  code: "plan-auth-refactor",
  progress: 75,
  title: "用户认证系统重构（最终阶段）",
  description: "最后的优化和测试"
})
```

### 错误处理

```javascript
// 计划不存在
{ "success": false, "error": "Plan not found: plan-nonexistent" }

// 进度值无效
{ "success": false, "error": "Progress must be between 0 and 100" }

// 已完成计划不可更新
{ "success": false, "error": "Cannot update completed plan" }

// 无有效的更新字段
{ "success": false, "error": "No fields to update" }
```

---

## 进度和状态管理详解

### 状态转换规则

```
pending (进度=0)
    ↓
in_progress (进度=1-99)
    ↓
completed (进度=100)
```

### 最佳实践

```javascript
// 创建时默认为 pending（进度 0）
plan_create({
  code: "plan-task",
  title: "任务",
  description: "描述",
  content: "内容"
})

// 开始执行时，更新进度为 1
plan_update({
  code: "plan-task",
  progress: 1  // 转为 in_progress
})

// 执行中定期更新进度
plan_update({ code: "plan-task", progress: 25 })
plan_update({ code: "plan-task", progress: 50 })
plan_update({ code: "plan-task", progress: 75 })

// 完成时，进度设为 100
plan_update({
  code: "plan-task",
  progress: 100  // 自动转为 completed
})
```

---

## 作用域系统

### 作用域概念

Plan MCP 支持两种作用域，控制计划的可见性和访问权限：

#### personal（个人）

- **可见范围**：仅对创建者可见
- **使用场景**：
  - 个人项目的计划
  - 个人学习任务
  - 私密项目规划
- **默认作用域**
- **示例**：个人开发计划、个人学习路线图

#### group（团队）

- **可见范围**：对所有团队成员可见
- **使用场景**：
  - 团队项目的计划
  - 团队目标和里程碑
  - 跨部门协作项目
- **示例**：产品发布计划、系统架构重构计划

### 作用域选择建议

```javascript
// 个人项目 → personal
plan_create({
  code: "plan-blog-migration",
  title: "个人博客迁移",
  scope: "personal"
})

// 团队项目 → group
plan_create({
  code: "plan-api-v2",
  title: "API v2 重新设计",
  scope: "group"
})
```

---

## 常见使用场景

### 场景 1: 简单的单步骤任务跟踪

```javascript
// 创建计划
plan_create({
  code: "plan-doc-update",
  title: "文档更新",
  description: "更新 API 文档至最新版本",
  content: "# 文档更新计划\n\n- 更新 API 参考\n- 添加新示例\n- 修复错误"
})

// 开始执行
plan_update({ code: "plan-doc-update", progress: 1 })

// 完成
plan_update({ code: "plan-doc-update", progress: 100 })
```

### 场景 2: 复杂的多阶段项目

```javascript
// 创建计划
plan_create({
  code: "plan-system-redesign",
  title: "系统架构重新设计",
  description: "从单体架构迁移到微服务架构",
  content: `# 系统架构重新设计

## 第 1 阶段：调研与设计 (Week 1)
- 技术调研
- 架构设计
- 成本评估

## 第 2 阶段：基础设施 (Week 2-3)
- Kubernetes 部署
- 服务网格设置
- 监控和日志

## 第 3 阶段：服务迁移 (Week 4-6)
- 拆分服务
- 数据迁移
- 功能测试

## 第 4 阶段：上线和优化 (Week 7)
- 灰度发布
- 性能优化
- 文档完善`
})

// 阶段 1 完成
plan_update({ code: "plan-system-redesign", progress: 25 })

// 阶段 2 完成
plan_update({ code: "plan-system-redesign", progress: 50 })

// 阶段 3 完成
plan_update({ code: "plan-system-redesign", progress: 75 })

// 完成
plan_update({ code: "plan-system-redesign", progress: 100 })
```

### 场景 3: 查询和监控

```javascript
// 获取所有进行中的计划
const planList = plan_list({ status: "in_progress" })

// 查看具体计划
planList.data.forEach(plan => {
  const details = plan_get({ code: plan.code })
  console.log(`${plan.title}: ${details.data.progress}%`)
})
```

---

## 交互流程示例

### 完整的计划生命周期

```javascript
// 1. 创建计划
const createResult = plan_create({
  code: "plan-feature-x",
  title: "Feature X 开发",
  description: "实现新的用户预览功能",
  content: "# Feature X 开发计划\n\n..."
})

// 2. 查看列表
plan_list({ scope: "personal", status: "pending" })

// 3. 获取详情
plan_get({ code: "plan-feature-x" })

// 4. 开始执行
plan_update({ code: "plan-feature-x", progress: 1 })

// 5. 定期更新进度
plan_update({ code: "plan-feature-x", progress: 30 })
plan_update({ code: "plan-feature-x", progress: 60 })

// 6. 更新内容（发现新信息）
plan_update({
  code: "plan-feature-x",
  content: "# 更新的计划\n\n新增：性能优化部分\n..."
})

// 7. 完成计划
plan_update({ code: "plan-feature-x", progress: 100 })

// 8. 查看完成的计划
plan_get({ code: "plan-feature-x" })
```

---

## 错误处理最佳实践

### 统一的错误处理模式

```javascript
// 所有 MCP 工具调用都遵循相同的响应格式
// 成功：success = true
// 失败：success = false, error = 错误描述

// 推荐的处理方式
async function callPlanTool(toolName, params) {
  const result = eval(`${toolName}(${JSON.stringify(params)})`)

  if (!result.success) {
    console.error(`错误: ${result.error}`)
    // 根据错误类型进行处理
    if (result.error.includes("not found")) {
      // 处理未找到的情况
    } else if (result.error.includes("format")) {
      // 处理格式错误
    }
    return null
  }

  return result.data
}
```

---

## 总结

- **plan_list**: 浏览计划列表（支持按状态和作用域过滤）
- **plan_create**: 创建新计划（需要 code、title、description、content）
- **plan_get**: 查看计划详情（获取完整信息）
- **plan_update**: 更新计划（支持进度、标题、内容等）
- **进度管理**: 0=待开始，1-99=进行中，100=已完成
- **作用域系统**: personal（个人）或 group（团队）
- **Code 格式**: 全小写+连字符，≥3 字符，字母开头结尾
