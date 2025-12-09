---
name: sync-progress
description: "进度同步器 - 任务完成后同步到项目进度。适用：完成 todo 后需要更新关联 plan 的进度、批量完成任务后计算整体进度。不适用：单独管理 todo/plan 用各自 skill。"
---

# Sync Progress

任务完成后同步到项目进度,保持 Plan 和 Todo 的一致性。

## 触发条件

- 完成一批 todo 后需要更新 plan 进度
- 用户说"同步进度"、"更新计划进度"

## 同步流程

### Step 1: 获取已完成的任务

```bash
llm-memory todo list
```

### Step 2: 计算新进度

根据完成的任务计算 plan 进度百分比。

### Step 3: 更新计划进度

```bash
llm-memory plan update --code plan-xxx --progress 60
```

## 输出示例

```
🔄 进度同步完成

📋 [plan-user-auth] 用户认证系统
   任务进度: 3/5 完成
   计划进度: 60%
```

## CLI 命令清单

- `llm-memory todo list` - 获取任务列表
- `llm-memory plan get` - 获取计划详情
- `llm-memory plan update` - 更新计划进度

详见：[工作流参考](./references/workflow.md)
