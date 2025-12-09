# Manage Tasks 使用示例

## 示例 1: 创建一批任务

**场景**：开始新功能开发，需要创建多个子任务

```javascript
todo_batch_create({
  items: [
    {
      code: "todo-design-api",
      title: "设计 API 接口",
      description: "设计用户认证相关的 REST API",
      priority: 3
    },
    {
      code: "todo-impl-login",
      title: "实现登录接口",
      priority: 3
    },
    {
      code: "todo-impl-register",
      title: "实现注册接口",
      priority: 3
    },
    {
      code: "todo-add-tests",
      title: "添加单元测试",
      priority: 2
    },
    {
      code: "todo-write-docs",
      title: "编写 API 文档",
      priority: 1
    }
  ]
})
```

**输出**：

```
✅ 成功创建 5 个待办任务

📋 任务列表：
  🟠 [todo-design-api] 设计 API 接口
  🟠 [todo-impl-login] 实现登录接口
  🟠 [todo-impl-register] 实现注册接口
  🟡 [todo-add-tests] 添加单元测试
  🟢 [todo-write-docs] 编写 API 文档
```

## 示例 2: 开始和完成任务

**场景**：开始工作，完成后标记

```javascript
// 开始第一个任务
todo_batch_start({
  codes: ["todo-design-api"]
})

// ... 工作中 ...

// 完成任务
todo_batch_complete({
  codes: ["todo-design-api"]
})
```

**输出**：

```
✅ 任务已完成

  ✓ [todo-design-api] 设计 API 接口

剩余待办：4 个
```

## 示例 3: 紧急任务插入

**场景**：发现 Bug，需要紧急处理

```javascript
todo_batch_create({
  items: [
    {
      code: "todo-fix-login-bug",
      title: "修复登录失败 Bug",
      description: "用户反馈登录时偶发 500 错误",
      priority: 4  // 紧急
    }
  ]
})
```

**输出**：

```
🔴 紧急任务已创建

  [todo-fix-login-bug] 修复登录失败 Bug
  优先级: 紧急 | 状态: 待处理

建议立即处理！
```

## 示例 4: 批量更新优先级

**场景**：需求变更，调整任务优先级

```javascript
todo_batch_update({
  items: [
    { code: "todo-add-tests", priority: 3 },
    { code: "todo-write-docs", priority: 3 }
  ]
})
```

## 示例 5: 查看任务状态

**场景**：查看当前所有任务

```javascript
todo_list({ scope: "all" })
```

**输出**：

```
📋 待办任务列表

进行中 (1):
  🟠 [todo-impl-login] 实现登录接口

待处理 (3):
  🔴 [todo-fix-login-bug] 修复登录失败 Bug
  🟠 [todo-impl-register] 实现注册接口
  🟠 [todo-add-tests] 添加单元测试

已完成 (1):
  ✓ [todo-design-api] 设计 API 接口
```

## 示例 6: 批量完成多个任务

**场景**：一次性完成多个任务

```javascript
todo_batch_complete({
  codes: [
    "todo-impl-login",
    "todo-impl-register",
    "todo-add-tests"
  ]
})
```

**输出**：

```
✅ 3 个任务已完成

  ✓ [todo-impl-login] 实现登录接口
  ✓ [todo-impl-register] 实现注册接口
  ✓ [todo-add-tests] 添加单元测试

剩余待办：1 个
```
