# Sync Progress 工作流参考

## 完整工作流

```
触发同步
    ↓
┌─────────────────────────────────┐
│         获取数据                │
├─────────────────────────────────┤
│ todo_list()  →  所有任务       │
│ plan_list()  →  所有计划       │
└─────────────────────────────────┘
    ↓
识别 Plan-Todo 关联关系
    ↓
计算每个 Plan 的新进度
    ↓
调用 plan_update 更新进度
    ↓
输出同步结果
```

## 关联识别策略

### 策略 1: Code 前缀匹配

```
plan-auth           →  todo-auth-*
plan-api-v2         →  todo-api-v2-*
plan-refactor       →  todo-refactor-*
```

### 策略 2: 显式关联（未来）

```javascript
// 未来可能支持的显式关联
todo_create({
  code: "todo-xxx",
  plan_code: "plan-auth"  // 显式指定
})
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
```

### 示例 2: 含进行中状态

```
Plan: plan-api-refactor
Todos:
  - todo-api-design      ✓ (completed)
  - todo-api-endpoints   → (in_progress) ← 算 50%
  - todo-api-test        ○ (pending)

计算: (1 + 0.5 + 0) / 3 × 100 = 50%
```

## 同步时机建议

| 场景 | 建议 |
|------|------|
| 完成单个任务 | 可选同步 |
| 完成多个任务 | 建议同步 |
| 结束工作日 | 建议同步 |
| 查看进度时 | 自动同步 |

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

## 最佳实践

1. **命名一致**：Plan 和 Todo 使用相同的项目前缀
2. **定期同步**：完成一批任务后同步一次
3. **验证结果**：同步后检查进度是否合理
4. **记录变更**：大幅进度变化时记录原因
