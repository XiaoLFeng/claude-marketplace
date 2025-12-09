# Agent 分配指南

## 分配原则

### 1. 按功能模块分配

```
模块 A → Task-A
模块 B → Task-B
汇总   → Main
```

### 2. 按依赖关系分配

```
无依赖任务 → 分配给不同 Agent 并行
有依赖任务 → 分配给同一 Agent 串行
汇总任务   → Main Agent
```

## Description 标注格式

```
格式: [Agent-标识] 任务描述，依赖: xxx

示例:
  [Task-A] 实现登录 API
  [Task-B] 实现注册流程
  [Main] 集成测试，依赖: 全部完成后
```

## CLI 创建带标注的任务

```bash
llm-memory todo add todo-auth-login "实现登录功能" \
  --description "[Task-A] 实现用户登录 API"

llm-memory todo add todo-auth-register "实现注册功能" \
  --description "[Task-B] 实现用户注册流程"

llm-memory todo add todo-auth-test "集成测试" \
  --description "[Main] 依赖全部完成"
```

## 避免冲突

1. **文件隔离**: 不同 Agent 处理不同文件
2. **状态隔离**: 每个 Agent 只管理自己的 todo
3. **共享文件**: 交给 Main Agent 处理
