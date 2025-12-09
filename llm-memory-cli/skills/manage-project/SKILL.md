---
name: manage-project
description: "项目计划管理器 - 管理复杂的多步骤项目。适用：任务复杂(>3步骤)、时间跨度长(>3天)、需要跟踪整体进度。不适用：单一简单任务用 manage-tasks。"
---

# Manage Project

管理复杂的多步骤项目，跟踪长期目标和整体进度。

## 触发条件

- 任务复杂（>3 步骤）
- 时间跨度长（>3 天）
- 用户说"帮我规划"、"制定方案"

## 操作指南

### 创建计划

```bash
llm-memory plan create \
  --code "plan-user-auth" \
  --title "用户认证系统" \
  --description "重构认证系统" \
  --content "## 步骤\n1. 设计\n2. 实现\n3. 测试"
```

### 查看计划

```bash
# 列出所有计划
llm-memory plan list

# 获取详情
llm-memory plan get --code plan-user-auth
```

### 更新进度

```bash
llm-memory plan update --code plan-user-auth --progress 45
```

## Code 格式

```
格式：plan-<描述>
示例：plan-user-auth ✅
```

## 进度与状态

| 进度 | 状态 |
|-----|------|
| 0 | 待开始 |
| 1-99 | 进行中 |
| 100 | 已完成 |

## CLI 命令清单

- `llm-memory plan list` - 列出计划
- `llm-memory plan create` - 创建计划
- `llm-memory plan get` - 获取详情
- `llm-memory plan update` - 更新计划

详见：
- [命令详解](./references/commands.md)
- [使用示例](./references/examples.md)
