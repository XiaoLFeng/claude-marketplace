# 优先级指南

## 优先级定义

| 级别 | 值 | 名称 | 说明 | 处理时机 |
|------|-----|------|------|----------|
| 低 | 1 | Low | 不紧急，可以稍后处理 | 空闲时处理 |
| 中 | 2 | Medium | 正常优先级 | 按顺序处理 |
| 高 | 3 | High | 重要，尽快处理 | 优先处理 |
| 紧急 | 4 | Urgent | 阻塞其他任务 | 立即处理 |

## 选择优先级

### 优先级 4 (紧急)

```
场景:
- 阻塞其他任务无法继续
- 线上故障需要立即修复
- 安全漏洞

示例:
  todo-fix-critical-bug     (4)
  todo-hotfix-security      (4)
```

### 优先级 3 (高)

```
场景:
- 当前迭代必须完成
- 影响主要功能
- 多个任务依赖

示例:
  todo-auth-login           (3)
  todo-api-core-feature     (3)
```

### 优先级 2 (中) - 默认

```
场景:
- 正常开发任务
- 无特殊紧急性
- 按计划执行

示例:
  todo-add-unit-tests       (2)
  todo-update-readme        (2)
```

### 优先级 1 (低)

```
场景:
- 优化改进
- 技术债务
- 可以延后

示例:
  todo-refactor-legacy      (1)
  todo-improve-performance  (1)
```

## 优先级调整

执行过程中可能需要调整优先级：

```javascript
// 使用 todo_batch_update 调整优先级
await todo_batch_update({
  items: [{
    code: "todo-auth-login",
    priority: 4  // 提升为紧急
  }]
});
```

## 处理顺序建议

```
1. 先处理优先级 4 (紧急)
    ↓
2. 再处理优先级 3 (高)
    ↓
3. 按顺序处理优先级 2 (中)
    ↓
4. 空闲时处理优先级 1 (低)
```
