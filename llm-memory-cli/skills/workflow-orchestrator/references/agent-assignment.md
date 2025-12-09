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

## Todo 命名规范

### Code 格式

```
格式: todo-<项目>-<任务>
正则: ^[a-z][a-z\-]*[a-z]$

示例:
  todo-auth-login       ✅
  todo-auth-register    ✅
  todo-api-refactor     ✅
```

### Description 中的 Agent 标注

```
格式: [Agent-标识] 任务描述

示例:
  [Task-A] 实现用户登录 API
  [Task-B] 实现订单创建流程
  [Main] 执行集成测试
```

## CLI 创建带标注的任务

```bash
# 创建任务并标注 Agent
llm-memory todo add todo-auth-login "实现登录功能" \
  --description "[Task-A] 实现用户登录 API 和前端表单"

llm-memory todo add todo-auth-register "实现注册功能" \
  --description "[Task-B] 实现用户注册流程"

llm-memory todo add todo-auth-test "集成测试" \
  --description "[Main] 等待全部完成后执行"
```

## 常见场景示例

### 前后端分离

```bash
# Task-A: 后端
llm-memory todo add todo-api-login "登录API" --description "[Task-A] 后端"
llm-memory todo add todo-api-register "注册API" --description "[Task-A] 后端"

# Task-B: 前端
llm-memory todo add todo-ui-login "登录页面" --description "[Task-B] 前端"
llm-memory todo add todo-ui-register "注册页面" --description "[Task-B] 前端"

# Main: 联调
llm-memory todo add todo-integration "联调测试" --description "[Main] 联调"
```
