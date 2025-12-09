# Todo CLI 命令详解

## llm-memory todo list

列出待办任务。

```bash
llm-memory todo list

# 指定作用域
llm-memory todo list --scope all  # personal/group/all
```

**输出示例**：

```
📋 待办任务列表

进行中 (1):
  🟠 [todo-impl-login] 实现登录接口

待处理 (2):
  🔴 [todo-fix-bug] 修复紧急 Bug
  🟡 [todo-add-test] 添加单元测试

已完成 (1):
  ✓ [todo-design-api] 设计 API 接口
```

## llm-memory todo create

创建单个待办任务。

```bash
llm-memory todo create \
  --code "todo-fix-login" \
  --title "修复登录 Bug" \
  --description "用户反馈登录时偶发 500 错误" \
  --priority 4
```

**参数说明**：

| 参数 | 必填 | 说明 | 默认值 |
|-----|------|------|--------|
| --code | ✅ | 唯一标识符 | - |
| --title | ✅ | 任务标题 | - |
| --description | ❌ | 详细描述 | - |
| --priority | ❌ | 优先级 (1-4) | 2 |
| --scope | ❌ | 作用域 | personal |

## llm-memory todo batch-create

批量创建待办（最多 100 个）。

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
    "code": "todo-add-tests",
    "title": "添加单元测试",
    "priority": 2
  }
]'
```

**混合模式结果**：

```
✅ 成功创建 2 个任务
❌ 失败 1 个

失败详情：
  • todo-dup: Code already exists
```

## llm-memory todo start

批量将任务标记为进行中。

```bash
llm-memory todo start --code "todo-fix-login"

# 批量开始
llm-memory todo batch-start --codes "todo-1,todo-2"
```

## llm-memory todo complete

批量将任务标记为已完成。

```bash
llm-memory todo complete --code "todo-fix-login"

# 批量完成
llm-memory todo batch-complete --codes "todo-1,todo-2,todo-3"
```

**输出示例**：

```
✅ 3 个任务已完成

  ✓ [todo-1] 实现登录接口
  ✓ [todo-2] 实现注册接口
  ✓ [todo-3] 添加单元测试

剩余待办：2 个
```

## llm-memory todo update

批量更新任务属性。

```bash
llm-memory todo update --json '[
  {
    "code": "todo-add-tests",
    "title": "添加单元测试（新）",
    "priority": 3,
    "status": 1
  }
]'
```

**可更新字段**：
- `title` - 任务标题
- `description` - 任务描述
- `priority` - 优先级 (1-4)
- `status` - 状态码 (0-3)

## llm-memory todo cancel

批量取消任务。

```bash
llm-memory todo cancel --code "todo-deprecated"

# 批量取消
llm-memory todo batch-cancel --codes "todo-1,todo-2"
```

## llm-memory todo final

⚠️ **危险操作**：清空所有待办（不可恢复）。

```bash
llm-memory todo final --scope personal

# 需要二次确认
# Are you sure to delete all todos? [y/N]: y
```

## 状态码说明

| 状态码 | 名称 | 说明 |
|-------|------|------|
| 0 | pending | 待处理 |
| 1 | in_progress | 进行中 |
| 2 | completed | 已完成 |
| 3 | cancelled | 已取消 |

## 作用域说明

| Scope | 说明 |
|-------|------|
| personal | 当前目录私有（默认） |
| group | 组内共享（需先加入组） |
| all | 查询时返回全部可见 |

## 常见用法

### 快速创建任务

```bash
# 简单任务（使用默认优先级）
llm-memory todo create --code "todo-fix-typo" --title "修复拼写错误"

# 紧急任务
llm-memory todo create --code "todo-hotfix" --title "修复生产 Bug" --priority 4
```

### 工作流示例

```bash
# 1. 创建任务
llm-memory todo create --code "todo-feature-x" --title "开发功能 X" --priority 3

# 2. 开始工作
llm-memory todo start --code "todo-feature-x"

# 3. 完成任务
llm-memory todo complete --code "todo-feature-x"

# 4. 查看剩余任务
llm-memory todo list
```

### 批量操作

```bash
# 创建一批相关任务
llm-memory todo batch-create --json '[
  {"code":"todo-step-1","title":"步骤 1","priority":3},
  {"code":"todo-step-2","title":"步骤 2","priority":3},
  {"code":"todo-step-3","title":"步骤 3","priority":2}
]'

# 完成多个任务
llm-memory todo batch-complete --codes "todo-step-1,todo-step-2"
```
