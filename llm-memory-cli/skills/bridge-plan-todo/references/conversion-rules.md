# Bridge Plan-Todo - 转换规则

## Plan 内容解析

### Markdown 结构识别

Plan 的 content 字段通常采用 Markdown 格式，需要解析以下结构：

```markdown
## Phase 1: 阶段标题
- [ ] 任务项 1
- [ ] 任务项 2

## Phase 2: 阶段标题
- [ ] 任务项 3
- [ ] 任务项 4
```

### 解析脚本示例

```bash
#!/bin/bash
# parse-plan.sh
# 解析 Plan content 并输出任务列表

parse_plan_content() {
  local content="$1"
  local plan_code="$2"
  local phase=0
  local step=0

  echo "$content" | while IFS= read -r line; do
    # 匹配 Phase 标题
    if [[ "$line" =~ ^##[[:space:]]+Phase[[:space:]]+([0-9]+) ]]; then
      phase="${BASH_REMATCH[1]}"
      step=0
      continue
    fi

    # 匹配任务项
    if [[ "$line" =~ ^-[[:space:]]+\[[[:space:]]\][[:space:]]+(.+)$ ]]; then
      ((step++))
      task="${BASH_REMATCH[1]}"
      code="todo-${plan_code}-${phase}-${step}"

      # 根据 Phase 确定优先级
      case $phase in
        1) priority=4 ;;
        2) priority=3 ;;
        *) priority=2 ;;
      esac

      echo "{\"code\":\"$code\",\"title\":\"$task\",\"priority\":$priority}"
    fi
  done
}
```

---

## Todo Code 生成规则

### 命名格式

```
todo-<plan-code>-<phase>-<step>
```

### 示例

| Plan Code | Phase | Step | Todo Code |
|-----------|-------|------|-----------|
| plan-auth-refactor | 1 | 1 | todo-plan-auth-refactor-1-1 |
| plan-auth-refactor | 1 | 2 | todo-plan-auth-refactor-1-2 |
| plan-auth-refactor | 2 | 1 | todo-plan-auth-refactor-2-1 |

### 简化命名

为避免 code 过长，可以省略 `plan-` 前缀：

```
todo-<short-plan-code>-<phase>-<step>
```

示例：`todo-auth-refactor-1-1`

---

## 优先级映射

### 根据 Phase 自动分配

```bash
get_priority_by_phase() {
  local phase=$1
  case $phase in
    1) echo 4 ;;  # 紧急
    2) echo 3 ;;  # 高
    3) echo 2 ;;  # 中
    *) echo 2 ;;  # 默认中
  esac
}
```

### 优先级说明

| Phase | 优先级 | 说明 |
|-------|--------|------|
| Phase 1 | 4 (紧急) | 基础工作，需要优先完成 |
| Phase 2 | 3 (高) | 核心功能实现 |
| Phase 3+ | 2 (中) | 后续工作 |

---

## 完整转换脚本

```bash
#!/bin/bash
# convert-plan-to-todos.sh
# 将 Plan 内容转换为 Todo 列表

PLAN_CODE=$1

if [ -z "$PLAN_CODE" ]; then
  echo "用法: $0 <plan-code>"
  exit 1
fi

# 获取计划内容
echo "📋 获取计划 $PLAN_CODE ..."
PLAN_JSON=$(llm-memory plan get "$PLAN_CODE" --format json 2>/dev/null)

if [ -z "$PLAN_JSON" ]; then
  echo "❌ 计划不存在"
  exit 1
fi

CONTENT=$(echo "$PLAN_JSON" | jq -r '.content')
TITLE=$(echo "$PLAN_JSON" | jq -r '.title')

echo "📝 计划标题: $TITLE"
echo ""

# 解析并生成 Todo
TODOS="[]"
PHASE=0
STEP=0

while IFS= read -r line; do
  # 匹配 Phase
  if [[ "$line" =~ ^##[[:space:]]+Phase[[:space:]]+([0-9]+) ]]; then
    PHASE="${BASH_REMATCH[1]}"
    STEP=0
    echo "🔹 发现 Phase $PHASE"
    continue
  fi

  # 匹配任务
  if [[ "$line" =~ ^-[[:space:]]+\[[[:space:]]\][[:space:]]+(.+)$ ]]; then
    ((STEP++))
    TASK="${BASH_REMATCH[1]}"
    CODE="todo-${PLAN_CODE#plan-}-${PHASE}-${STEP}"

    # 优先级
    case $PHASE in
      1) PRIORITY=4 ;;
      2) PRIORITY=3 ;;
      *) PRIORITY=2 ;;
    esac

    echo "  ✅ [$CODE] $TASK (优先级: $PRIORITY)"

    # 添加到 JSON 数组
    TODO_ITEM=$(cat <<EOF
{"code":"$CODE","title":"$TASK","priority":$PRIORITY}
EOF
)
    TODOS=$(echo "$TODOS" | jq ". + [$TODO_ITEM]")
  fi
done <<< "$CONTENT"

# 输出结果
echo ""
echo "📊 共解析 $(echo "$TODOS" | jq 'length') 个任务"
echo ""

# 询问是否创建
read -p "是否批量创建这些待办？(y/n) " -n 1 -r
echo ""

if [[ $REPLY =~ ^[Yy]$ ]]; then
  echo '{"items":'"$TODOS"'}' | llm-memory todo batch-create --stdin
  echo "✅ 待办创建完成"
fi
```

---

## 注意事项

### 1. Code 唯一性

创建前应检查 code 是否已存在：

```bash
# 检查是否存在
if llm-memory todo get "$CODE" &>/dev/null; then
  echo "⚠️ Todo $CODE 已存在，跳过"
else
  llm-memory todo create "$CODE" --title "$TITLE"
fi
```

### 2. 特殊字符处理

任务标题中的特殊字符需要转义：

```bash
# 转义双引号
TITLE=$(echo "$TASK" | sed 's/"/\\"/g')
```

### 3. 空 Phase 处理

如果 Plan 内容没有 Phase 分组：

```bash
# 默认 Phase 为 1
if [ "$PHASE" -eq 0 ]; then
  PHASE=1
fi
```
