# Agent 分配指南

## 分配原则

### 1. 按功能模块分配

```
模块 A (用户相关) → Task-A
  - 登录
  - 注册
  - 个人信息

模块 B (订单相关) → Task-B
  - 创建订单
  - 订单列表
  - 订单详情
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

## Todo 命名规范

### Code 格式

```
格式: todo-<项目>-<任务>
正则: ^[a-z][a-z\-]*[a-z]$

示例:
  todo-auth-login       ✅
  todo-auth-register    ✅
  todo-api-refactor     ✅
  TODO-Auth-Login       ❌ (大写)
  todo_auth_login       ❌ (下划线)
```

### Description 中的 Agent 标注

```
格式: [Agent-标识] 任务描述

示例:
  [Task-A] 实现用户登录 API
  [Task-B] 实现订单创建流程
  [Main] 执行集成测试
```

## 分配决策流程

```
任务列表
    ↓
分析依赖关系
    ↓
┌─────────────────────────────────────┐
│ 可并行？                            │
├─────────┬───────────────────────────┤
│   是    │          否              │
│   ↓     │          ↓               │
│ 分配给   │    分配给同一            │
│ 不同Agent│    Agent 串行           │
└─────────┴───────────────────────────┘
    ↓
标注 Agent 到 description
    ↓
创建 todos
```

## 常见场景示例

### 场景 1: 前后端分离

```
[Task-A] 后端 API 开发
  - todo-api-user-crud
  - todo-api-auth

[Task-B] 前端页面开发
  - todo-ui-login-page
  - todo-ui-user-list

[Main] 联调测试
  - todo-integration-test
```

### 场景 2: 微服务拆分

```
[Task-A] 用户服务
  - todo-svc-user-api
  - todo-svc-user-db

[Task-B] 订单服务
  - todo-svc-order-api
  - todo-svc-order-db

[Main] 网关配置
  - todo-gateway-routes
```

### 场景 3: 重构任务

```
[Task-A] 旧代码清理
  - todo-refactor-remove-legacy

[Task-B] 新代码编写
  - todo-refactor-new-impl

[Main] 迁移验证
  - todo-refactor-verify
```
