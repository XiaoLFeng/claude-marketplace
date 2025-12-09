# 同步进度工作流

## 完整工作流

```
触发同步
    ↓
llm-memory todo list → 所有任务
llm-memory plan list → 所有计划
    ↓
识别 Plan-Todo 关联关系
    ↓
计算每个 Plan 的新进度
    ↓
llm-memory plan update 更新进度
    ↓
输出同步结果
```

## 关联识别策略

```
plan-auth           →  todo-auth-*
plan-api-v2         →  todo-api-v2-*
```

## 进度计算示例

### 示例 1: 基础计算

```
Plan: plan-user-auth
Todos:
  - todo-auth-design     ✓ (completed)
  - todo-auth-login      ✓ (completed)
  - todo-auth-register   ○ (pending)
  - todo-auth-test       ○ (pending)

计算: 2/4 × 100 = 50%

命令: llm-memory plan update plan-user-auth --progress 50
```

### 示例 2: 含进行中状态

```
Plan: plan-api-refactor
Todos:
  - todo-api-design      ✓ (completed)
  - todo-api-endpoints   → (in_progress)
  - todo-api-test        ○ (pending)

计算: (1 + 0.5) / 3 × 100 = 50%

命令: llm-memory plan update plan-api-refactor --progress 50
```

## 同步时机建议

| 场景 | 建议 |
|------|------|
| 完成单个任务 | 可选同步 |
| 完成多个任务 | 建议同步 |
| 结束工作日 | 建议同步 |

## CLI 命令序列

```bash
# 1. 获取任务状态
llm-memory todo list

# 2. 获取计划列表
llm-memory plan list

# 3. 计算并更新进度
llm-memory plan update plan-xxx --progress 60
```
