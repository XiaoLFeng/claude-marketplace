# Agent 分配指南

## 分配原则

### 1. 按功能模块分配

```
模块 A (用户相关) → Task-A
模块 B (订单相关) → Task-B
汇总/测试        → Main
```

### 2. 按文件目录分配

```
src/user/     → Task-A
src/order/    → Task-B
src/common/   → Main (避免冲突)
```

### 3. 按依赖关系分配

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
  [Task-A] JWT 集成，依赖: todo-auth-login
  [Main] 集成测试，依赖: 全部完成后
```

## 分配决策流程

```
任务列表
    ↓
分析依赖关系
    ↓
识别可并行任务
    ↓
分配 Agent
    ↓
标注到 description
```

## 常见场景

### 前后端分离

```
[Task-A] 后端 API
  - todo-api-login
  - todo-api-register

[Task-B] 前端页面
  - todo-ui-login
  - todo-ui-register

[Main] 联调
  - todo-integration
```

### 微服务开发

```
[Task-A] 服务 A
  - todo-svc-user

[Task-B] 服务 B
  - todo-svc-order

[Main] 网关
  - todo-gateway
```

### 重构任务

```
[Task-A] 旧代码
  - todo-remove-legacy

[Task-B] 新代码
  - todo-new-impl

[Main] 验证
  - todo-verify
```

## 避免冲突

1. **文件隔离**: 不同 Agent 处理不同文件
2. **状态隔离**: 每个 Agent 只管理自己的 todo
3. **共享文件**: 交给 Main Agent 处理
