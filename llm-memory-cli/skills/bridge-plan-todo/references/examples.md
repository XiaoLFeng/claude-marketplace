# Bridge Plan-Todo - 使用示例

## 场景 1：Plan 转 Todo（新计划开始）

### 用户创建计划

```bash
llm-memory plan create plan-auth-refactor \
  --title "用户认证系统重构" \
  --description "将现有的 Session 认证迁移到 JWT 机制" \
  --content "$(cat <<'EOF'
## Phase 1: 数据库设计
- [ ] 设计 users 表结构
- [ ] 设计 refresh_tokens 表

## Phase 2: JWT 实现
- [ ] 实现 JWT 生成逻辑
- [ ] 实现 Token 刷新机制
- [ ] 实现 Token 黑名单
EOF
)"
```

### 触发转换

用户说："帮我把这个计划拆分成待办事项"

### 执行命令

```bash
# 获取计划内容
llm-memory plan get plan-auth-refactor

# 批量创建 Todo
cat << 'EOF' | llm-memory todo batch-create --stdin
{
  "items": [
    {"code": "todo-auth-refactor-1-1", "title": "设计 users 表结构", "priority": 4},
    {"code": "todo-auth-refactor-1-2", "title": "设计 refresh_tokens 表", "priority": 4},
    {"code": "todo-auth-refactor-2-1", "title": "实现 JWT 生成逻辑", "priority": 3},
    {"code": "todo-auth-refactor-2-2", "title": "实现 Token 刷新机制", "priority": 3},
    {"code": "todo-auth-refactor-2-3", "title": "实现 Token 黑名单", "priority": 3}
  ]
}
EOF
```

### 输出

```
✅ 已创建 5 个待办：

🔴 [todo-auth-refactor-1-1] 设计 users 表结构
🔴 [todo-auth-refactor-1-2] 设计 refresh_tokens 表
🟠 [todo-auth-refactor-2-1] 实现 JWT 生成逻辑
🟠 [todo-auth-refactor-2-2] 实现 Token 刷新机制
🟠 [todo-auth-refactor-2-3] 实现 Token 黑名单
```

---

## 场景 2：Todo 完成同步 Plan

### 用户完成任务

```
用户："users 表设计完成了"
```

### 执行命令

```bash
# 完成 Todo
llm-memory todo complete todo-auth-refactor-1-1

# 获取当前进度
llm-memory todo list --filter "todo-auth-refactor-*"
```

### 输出

```
✅ 待办列表：

✅ 🔴 [todo-auth-refactor-1-1] 设计 users 表结构 - 已完成
⏸️ 🔴 [todo-auth-refactor-1-2] 设计 refresh_tokens 表 - 待处理
⏸️ 🟠 [todo-auth-refactor-2-1] 实现 JWT 生成逻辑 - 待处理
⏸️ 🟠 [todo-auth-refactor-2-2] 实现 Token 刷新机制 - 待处理
⏸️ 🟠 [todo-auth-refactor-2-3] 实现 Token 黑名单 - 待处理

完成: 1/5 (20%)
```

### 同步进度

```bash
llm-memory plan update plan-auth-refactor --progress 20
```

### 确认更新

```bash
llm-memory plan get plan-auth-refactor
```

输出：

```
📋 计划详情：

Code: plan-auth-refactor
标题: 用户认证系统重构
进度: 20%
状态: 进行中
```

---

## 场景 3：批量完成同步

### 用户说

```
"Phase 1 的两个任务都完成了"
```

### 执行命令

```bash
# 批量完成
llm-memory todo batch-complete \
  todo-auth-refactor-1-1 \
  todo-auth-refactor-1-2

# 计算新进度 (2/5 = 40%)
llm-memory plan update plan-auth-refactor --progress 40
```

### 输出

```
✅ 已完成 2 个待办：
- todo-auth-refactor-1-1
- todo-auth-refactor-1-2

📋 计划进度已更新: 40%
```

---

## 场景 4：计划完成

### 所有任务完成

```bash
# 完成剩余任务
llm-memory todo batch-complete \
  todo-auth-refactor-2-1 \
  todo-auth-refactor-2-2 \
  todo-auth-refactor-2-3

# 更新计划为完成
llm-memory plan update plan-auth-refactor --progress 100
```

### 输出

```
✅ 所有待办已完成！

📋 计划 plan-auth-refactor 已完成 (100%)
```

---

## 完整工作流脚本

```bash
#!/bin/bash
# workflow.sh - 完整的 Plan-Todo 工作流

# 颜色定义
RED='\033[0;31m'
GREEN='\033[0;32m'
YELLOW='\033[1;33m'
NC='\033[0m'

# 1. 创建计划
create_plan() {
  echo -e "${YELLOW}📋 创建计划...${NC}"

  llm-memory plan create "$1" \
    --title "$2" \
    --description "$3" \
    --content "$4"

  echo -e "${GREEN}✅ 计划创建成功${NC}"
}

# 2. 转换为 Todo
convert_to_todos() {
  local plan_code=$1

  echo -e "${YELLOW}🔄 转换为待办...${NC}"

  # 这里应该解析计划内容，为简化示例直接使用参数
  shift
  local todos_json='{"items":['
  local first=true

  while [ $# -gt 0 ]; do
    local code=$1
    local title=$2
    local priority=$3
    shift 3

    if [ "$first" = true ]; then
      first=false
    else
      todos_json+=','
    fi

    todos_json+="{\"code\":\"$code\",\"title\":\"$title\",\"priority\":$priority}"
  done

  todos_json+=']}'

  echo "$todos_json" | llm-memory todo batch-create --stdin

  echo -e "${GREEN}✅ 待办创建成功${NC}"
}

# 3. 完成 Todo 并同步
complete_and_sync() {
  local todo_code=$1

  echo -e "${YELLOW}✅ 完成待办: $todo_code${NC}"
  llm-memory todo complete "$todo_code"

  # 提取 plan code
  local plan_part=$(echo "$todo_code" | sed 's/^todo-//' | sed 's/-[0-9]*-[0-9]*$//')
  local plan_code="plan-$plan_part"

  # 计算进度
  local todos=$(llm-memory todo list --filter "todo-$plan_part-*" --format json 2>/dev/null)
  local total=$(echo "$todos" | jq 'length')
  local completed=$(echo "$todos" | jq '[.[] | select(.status == 2)] | length')

  if [ "$total" -gt 0 ]; then
    local progress=$((completed * 100 / total))
    llm-memory plan update "$plan_code" --progress "$progress"
    echo -e "${GREEN}📊 进度: $completed/$total ($progress%)${NC}"
  fi
}

# 4. 显示状态
show_status() {
  local plan_code=$1

  echo -e "${YELLOW}📊 当前状态${NC}"
  echo ""

  llm-memory plan get "$plan_code"
  echo ""

  local plan_part=${plan_code#plan-}
  llm-memory todo list --filter "todo-$plan_part-*"
}

# 主程序示例
# create_plan "plan-demo" "示例计划" "这是一个示例" "..."
# convert_to_todos "plan-demo" "todo-demo-1-1" "任务1" 4 "todo-demo-1-2" "任务2" 3
# complete_and_sync "todo-demo-1-1"
# show_status "plan-demo"
```

---

## 最佳实践

### 1. Code 命名一致性

```bash
# 好的命名
plan-auth-refactor
todo-auth-refactor-1-1
todo-auth-refactor-1-2

# 不好的命名（不一致）
plan-auth
todo-authentication-1
todo-auth-task-2
```

### 2. 及时同步

每次完成 Todo 后立即同步进度，保持数据一致：

```bash
# 封装为单一命令
alias complete-sync='complete_and_sync'
```

### 3. 定期检查

```bash
# 检查进度是否同步
check_sync() {
  local plan_code=$1
  local plan=$(llm-memory plan get "$plan_code" --format json)
  local plan_progress=$(echo "$plan" | jq '.progress')

  local todos=$(llm-memory todo list --filter "todo-${plan_code#plan-}-*" --format json)
  local total=$(echo "$todos" | jq 'length')
  local completed=$(echo "$todos" | jq '[.[] | select(.status == 2)] | length')
  local calc_progress=$((completed * 100 / total))

  if [ "$plan_progress" -ne "$calc_progress" ]; then
    echo "⚠️ 进度不同步: Plan=$plan_progress%, 实际=$calc_progress%"
  else
    echo "✅ 进度同步: $plan_progress%"
  fi
}
```
