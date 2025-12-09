---
name: manage-project
description: "项目计划管理器 - 管理复杂的多步骤项目。适用：任务复杂(>3步骤)、时间跨度长(>3天)、需要跟踪整体进度。不适用：单一简单任务用 manage-tasks。"
---

# Manage Project

管理复杂的多步骤项目，跟踪长期目标和整体进度。

## 触发条件

- 任务复杂（>3 步骤）
- 时间跨度长（>3 天）
- 需要跟踪整体进度（0-100%）
- 用户说"帮我规划"、"制定方案"、"创建计划"
- 有子任务依赖关系

## 不触发条件

- 单一简单任务 → 使用 manage-tasks
- 短期行动项（<1天）→ 使用 manage-tasks
- 独立任务（无依赖）→ 使用 manage-tasks

## Plan vs Todo 选择

| 特性 | Plan | Todo |
|------|------|------|
| 复杂度 | >3 步骤 | 单一任务 |
| 时间跨度 | >3 天 | <1 天 |
| 进度跟踪 | 0-100% | 完成/未完成 |
| 适用场景 | 项目/功能 | 具体行动 |

## 操作指南

### 创建计划

```javascript
plan_create({
  code: "plan-user-auth",
  title: "用户认证系统重构",
  description: "重构现有认证系统，支持 OAuth 和 JWT",
  content: `## 目标
实现安全、可扩展的认证系统

## 步骤
1. 技术选型调研
2. 设计认证流程
3. 实现核心模块
4. 集成测试
5. 文档和上线

## 验收标准
- 支持 JWT 和 OAuth 2.0
- 通过安全审计
- 性能满足要求`,
  scope: "personal"
})
```

### 查看计划

```javascript
// 列出所有计划
plan_list({ scope: "all" })

// 获取计划详情
plan_get({ code: "plan-user-auth" })
```

### 更新进度

```javascript
// 进度自动转换状态
plan_update({
  code: "plan-user-auth",
  progress: 45  // 0=待开始, 1-99=进行中, 100=已完成
})
```

### 更新内容

```javascript
plan_update({
  code: "plan-user-auth",
  title: "新标题",
  description: "新描述",
  content: "更新的内容..."
})
```

## Code 格式

```
格式：plan-<描述>
规则：全小写 + 连字符，≥3字符

示例：
  plan-user-auth        ✅
  plan-api-v2           ✅
  plan-refactor-db      ✅
  Plan_001              ❌
```

## 进度与状态

| 进度 | 状态 | 说明 |
|-----|------|------|
| 0 | pending | 待开始 |
| 1-99 | in_progress | 进行中 |
| 100 | completed | 已完成 |

## MCP 工具清单

- `plan_list` - 列出计划
- `plan_create` - 创建计划
- `plan_get` - 获取详情
- `plan_update` - 更新计划（含进度）

详见：
- [工具详解](./references/tools.md)
- [使用示例](./references/examples.md)
