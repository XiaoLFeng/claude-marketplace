# Manage Tasks 使用示例

## 示例 1: 创建一批任务

**场景**：开始新功能开发，需要创建多个子任务

```bash
llm-memory todo batch-create --json '[
  {
    "code": "todo-design-api",
    "title": "设计 API 接口",
    "description": "设计用户认证相关的 REST API",
    "priority": 3
  },
  {
    "code": "todo-impl-login",
    "title": "实现登录接口",
    "priority": 3
  },
  {
    "code": "todo-impl-register",
    "title": "实现注册接口",
    "priority": 3
  },
  {
    "code": "todo-add-tests",
    "title": "添加单元测试",
    "priority": 2
  },
  {
    "code": "todo-write-docs",
    "title": "编写 API 文档",
    "priority": 1
  }
]'
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

```bash
# 开始第一个任务
llm-memory todo start --code "todo-design-api"

# ... 工作中 ...

# 完成任务
llm-memory todo complete --code "todo-design-api"
```

**输出**：

```
✅ 任务已完成

  ✓ [todo-design-api] 设计 API 接口

剩余待办：4 个
```

## 示例 3: 紧急任务插入

**场景**：发现 Bug，需要紧急处理

```bash
llm-memory todo create \
  --code "todo-fix-login-bug" \
  --title "修复登录失败 Bug" \
  --description "用户反馈登录时偶发 500 错误" \
  --priority 4
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

```bash
llm-memory todo update --json '[
  {"code": "todo-add-tests", "priority": 3},
  {"code": "todo-write-docs", "priority": 3}
]'
```

**输出**：

```
✅ 2 个任务已更新

  🟠 [todo-add-tests] 添加单元测试
  🟠 [todo-write-docs] 编写 API 文档
```

## 示例 5: 查看任务状态

**场景**：查看当前所有任务

```bash
llm-memory todo list
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

```bash
llm-memory todo batch-complete --codes "todo-impl-login,todo-impl-register,todo-add-tests"
```

**输出**：

```
✅ 3 个任务已完成

  ✓ [todo-impl-login] 实现登录接口
  ✓ [todo-impl-register] 实现注册接口
  ✓ [todo-add-tests] 添加单元测试

剩余待办：1 个
```

## 示例 7: 完整工作流

**场景**：从创建到完成的完整流程

```bash
# 1. 创建任务
llm-memory todo create \
  --code "todo-feature-payment" \
  --title "实现支付功能" \
  --description "集成支付宝和微信支付" \
  --priority 3

# 2. 查看任务列表
llm-memory todo list

# 3. 开始任务
llm-memory todo start --code "todo-feature-payment"

# 4. 中途发现需要拆分子任务
llm-memory todo batch-create --json '[
  {"code":"todo-pay-alipay","title":"集成支付宝","priority":3},
  {"code":"todo-pay-wechat","title":"集成微信支付","priority":3},
  {"code":"todo-pay-test","title":"支付功能测试","priority":2}
]'

# 5. 完成子任务
llm-memory todo batch-complete --codes "todo-pay-alipay,todo-pay-wechat,todo-pay-test"

# 6. 完成主任务
llm-memory todo complete --code "todo-feature-payment"

# 7. 查看最终状态
llm-memory todo list
```

## 示例 8: 取消过时任务

**场景**：需求变更，某些任务不再需要

```bash
# 取消单个任务
llm-memory todo cancel --code "todo-deprecated-feature"

# 批量取消
llm-memory todo batch-cancel --codes "todo-old-1,todo-old-2,todo-old-3"
```

**输出**：

```
✅ 3 个任务已取消

  • [todo-old-1] 旧功能 1
  • [todo-old-2] 旧功能 2
  • [todo-old-3] 旧功能 3
```

## 示例 9: 紧急情况处理

**场景**：生产环境出现严重 Bug

```bash
# 1. 立即创建紧急任务
llm-memory todo create \
  --code "todo-critical-bug" \
  --title "修复生产环境崩溃" \
  --description "数据库连接池耗尽导致服务不可用" \
  --priority 4

# 2. 立即开始处理
llm-memory todo start --code "todo-critical-bug"

# 3. 查看所有任务（紧急任务会排在最前）
llm-memory todo list

# 4. 完成修复
llm-memory todo complete --code "todo-critical-bug"
```

## 最佳实践

### 任务命名规范

```bash
# 好的命名 ✅
todo-fix-login-bug         # 清晰描述问题
todo-add-payment-feature   # 说明要做什么
todo-refactor-user-service # 明确重构对象

# 不好的命名 ❌
todo-bug                   # 太笼统
todo-feature               # 不具体
TODO_001                   # 使用了大写和下划线
```

### 优先级设置技巧

```bash
# 根据影响范围设置
llm-memory todo create --code "todo-prod-bug" --title "生产 Bug" --priority 4
llm-memory todo create --code "todo-new-feature" --title "新功能" --priority 3
llm-memory todo create --code "todo-optimize" --title "性能优化" --priority 2
llm-memory todo create --code "todo-refactor" --title "代码重构" --priority 1
```

### 批量操作技巧

```bash
# 使用 JSON 文件批量创建
cat tasks.json | llm-memory todo batch-create --json "$(cat -)"

# 或者使用 heredoc
llm-memory todo batch-create --json "$(cat <<'EOF'
[
  {"code":"todo-1","title":"任务 1","priority":3},
  {"code":"todo-2","title":"任务 2","priority":2}
]
EOF
)"
```
