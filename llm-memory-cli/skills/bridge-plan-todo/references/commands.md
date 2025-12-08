# Bridge Plan-Todo - CLI 命令参考

## 使用的 CLI 命令

### llm-memory plan get

获取计划详情。

**语法**：

```bash
llm-memory plan get <code>
```

**参数**：

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| code | string | ✅ | 计划唯一标识码 |

**示例**：

```bash
llm-memory plan get plan-auth-refactor
```

**输出示例**：

```
📋 计划详情：

Code: plan-auth-refactor
标题: 用户认证系统重构
描述: 将现有的 Session 认证迁移到 JWT 机制

内容:
## Phase 1: 数据库设计
- [ ] 设计 users 表结构
- [ ] 设计 refresh_tokens 表

## Phase 2: JWT 实现
- [ ] 实现 JWT 生成逻辑
- [ ] 实现 Token 刷新机制

进度: 45%
状态: 进行中
创建时间: 2024-12-01 10:00:00
```

---

### llm-memory plan update

更新计划。

**语法**：

```bash
llm-memory plan update <code> [options]
```

**参数**：

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| code | string | ✅ | 计划唯一标识码 |
| --title | string | ❌ | 新标题 |
| --description | string | ❌ | 新描述 |
| --content | string | ❌ | 新内容 |
| --progress | number | ❌ | 进度 0-100 |

**示例**：

```bash
# 更新进度
llm-memory plan update plan-auth-refactor --progress 60

# 更新标题和描述
llm-memory plan update plan-auth-refactor \
  --title "JWT 认证系统实现" \
  --description "实现完整的 JWT 认证流程"
```

---

### llm-memory todo create

创建待办事项。

**语法**：

```bash
llm-memory todo create <code> --title <title> [options]
```

**参数**：

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| code | string | ✅ | 待办唯一标识码 |
| --title | string | ✅ | 待办标题 |
| --description | string | ❌ | 详细描述 |
| --priority | number | ❌ | 优先级 1-4（默认 2） |

**示例**：

```bash
llm-memory todo create todo-auth-refactor-1-1 \
  --title "设计 users 表结构" \
  --description "包含用户基本信息和密码哈希" \
  --priority 4
```

---

### llm-memory todo batch-create

批量创建待办事项。

**语法**：

```bash
llm-memory todo batch-create --file <json-file>
# 或
echo '<json>' | llm-memory todo batch-create --stdin
```

**JSON 格式**：

```json
{
  "items": [
    {
      "code": "todo-auth-refactor-1-1",
      "title": "设计 users 表结构",
      "priority": 4
    },
    {
      "code": "todo-auth-refactor-1-2",
      "title": "设计 refresh_tokens 表",
      "priority": 4
    }
  ]
}
```

**示例**：

```bash
# 从文件创建
llm-memory todo batch-create --file todos.json

# 从标准输入创建
cat << 'EOF' | llm-memory todo batch-create --stdin
{
  "items": [
    {"code": "todo-task-1", "title": "任务 1", "priority": 3},
    {"code": "todo-task-2", "title": "任务 2", "priority": 2}
  ]
}
EOF
```

---

### llm-memory todo complete

标记待办为已完成。

**语法**：

```bash
llm-memory todo complete <code>
```

**示例**：

```bash
llm-memory todo complete todo-auth-refactor-1-1
```

---

### llm-memory todo batch-complete

批量完成待办。

**语法**：

```bash
llm-memory todo batch-complete <code1> <code2> ...
```

**示例**：

```bash
llm-memory todo batch-complete \
  todo-auth-refactor-1-1 \
  todo-auth-refactor-1-2
```

---

### llm-memory todo start

标记待办为进行中。

**语法**：

```bash
llm-memory todo start <code>
```

**示例**：

```bash
llm-memory todo start todo-auth-refactor-2-1
```

---

### llm-memory todo list

列出待办（支持过滤）。

**语法**：

```bash
llm-memory todo list [--scope <scope>] [--filter <pattern>]
```

**示例**：

```bash
# 列出所有待办
llm-memory todo list

# 过滤特定计划的待办
llm-memory todo list --filter "todo-auth-refactor-*"
```

---

## 组合使用脚本

### Plan 转 Todo 脚本

```bash
#!/bin/bash
# plan-to-todos.sh
# 用法: ./plan-to-todos.sh <plan-code>

PLAN_CODE=$1

if [ -z "$PLAN_CODE" ]; then
  echo "用法: $0 <plan-code>"
  exit 1
fi

echo "📋 获取计划详情..."
PLAN_CONTENT=$(llm-memory plan get "$PLAN_CODE" --format json)

echo "🔄 解析计划内容并生成 Todo..."
# 这里需要解析 Markdown 内容，提取任务项
# 实际实现需要配合解析脚本

echo "✅ 转换完成"
```

### Todo 完成同步 Plan 脚本

```bash
#!/bin/bash
# sync-progress.sh
# 用法: ./sync-progress.sh <plan-code>

PLAN_CODE=$1

# 获取该计划关联的所有 Todo
TODOS=$(llm-memory todo list --filter "todo-${PLAN_CODE}-*" --format json)

# 计算完成比例
TOTAL=$(echo "$TODOS" | jq 'length')
COMPLETED=$(echo "$TODOS" | jq '[.[] | select(.status == 2)] | length')

if [ "$TOTAL" -gt 0 ]; then
  PROGRESS=$((COMPLETED * 100 / TOTAL))

  echo "📊 进度: $COMPLETED/$TOTAL ($PROGRESS%)"

  # 更新计划进度
  llm-memory plan update "$PLAN_CODE" --progress "$PROGRESS"
  echo "✅ 计划进度已更新"
else
  echo "⚠️ 未找到关联的待办事项"
fi
```

---

## 状态码说明

### Todo 状态

| 值 | 说明 | 图标 |
|----|------|------|
| 0 | 待处理 | ⏸️ |
| 1 | 进行中 | ⏳ |
| 2 | 已完成 | ✅ |
| 3 | 已取消 | ❌ |

### Plan 状态

| 值 | 说明 | 图标 |
|----|------|------|
| pending | 待开始 | ⏸️ |
| in_progress | 进行中 | ⏳ |
| completed | 已完成 | ✅ |

---

## 参考链接

- [Plan CLI 完整文档](../../plan-cli/references/commands.md)
- [Todo CLI 完整文档](../../todo-cli/references/commands.md)
