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

## 不触发条件

- 单独创建/管理 todo → 使用 task-* 系列
- 单独创建/管理 plan → 使用 manage-project

## 同步流程

### Step 1: 获取数据

```bash
llm-memory todo list
llm-memory plan list
```

### Step 2: 识别关联关系

```
plan-user-auth          ← 计划
  todo-auth-design      ← 关联任务
  todo-auth-login       ← 关联任务
  todo-auth-test        ← 关联任务
```

### Step 3: 计算新进度

```
进度 = (已完成数 / 总数) × 100%
```

### Step 4: 更新计划进度

```bash
llm-memory plan update plan-user-auth --progress 60
```

## 进度计算规则

### 基础计算

```
进度 = (已完成数 / 总数) × 100%

示例:
  总任务: 4
  已完成: 2
  进度: 50%
```

### 含进行中状态

```
进度 = ((已完成数 + 进行中数 × 0.5) / 总数) × 100%

示例:
  总任务: 4
  已完成: 1
  进行中: 2
  进度: (1 + 1) / 4 × 100 = 50%
```

## 关联识别策略

```
plan-auth           →  todo-auth-*
plan-api-v2         →  todo-api-v2-*
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

## CLI 命令

- `llm-memory todo list` - 获取任务列表
- `llm-memory plan list` - 获取计划列表
- `llm-memory plan update <code> --progress <n>` - 更新进度

详见：[同步工作流](./references/workflow.md)
