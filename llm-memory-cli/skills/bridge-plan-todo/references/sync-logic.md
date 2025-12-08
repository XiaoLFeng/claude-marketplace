# Bridge Plan-Todo - 同步逻辑

## 进度计算

### 基本公式

```
progress = (completedTodos / totalTodos) * 100
```

### 计算脚本

```bash
#!/bin/bash
# calculate-progress.sh

calculate_progress() {
  local plan_code=$1

  # 获取关联的 Todo 列表
  local todos=$(llm-memory todo list --filter "todo-${plan_code#plan-}-*" --format json)

  local total=$(echo "$todos" | jq 'length')
  local completed=$(echo "$todos" | jq '[.[] | select(.status == 2)] | length')

  if [ "$total" -eq 0 ]; then
    echo 0
    return
  fi

  echo $((completed * 100 / total))
}

# 使用
PROGRESS=$(calculate_progress "plan-auth-refactor")
echo "进度: $PROGRESS%"
```

---

## 状态同步

### Todo 状态 → Plan 状态

| Todo 状态组合 | Plan 状态 |
|--------------|----------|
| 全部待处理 (status=0) | pending |
| 任一进行中 (status=1) | in_progress |
| 任一已完成 (status=2) | in_progress |
| 全部已完成 (status=2) | completed |

### 同步脚本

```bash
#!/bin/bash
# sync-plan-status.sh

sync_plan_status() {
  local plan_code=$1

  local todos=$(llm-memory todo list --filter "todo-${plan_code#plan-}-*" --format json)

  local total=$(echo "$todos" | jq 'length')
  local completed=$(echo "$todos" | jq '[.[] | select(.status == 2)] | length')
  local in_progress=$(echo "$todos" | jq '[.[] | select(.status == 1)] | length')

  local new_status="pending"
  local progress=0

  if [ "$total" -gt 0 ]; then
    progress=$((completed * 100 / total))

    if [ "$completed" -eq "$total" ]; then
      new_status="completed"
    elif [ "$in_progress" -gt 0 ] || [ "$completed" -gt 0 ]; then
      new_status="in_progress"
    fi
  fi

  # 更新计划
  llm-memory plan update "$plan_code" --progress "$progress"

  echo "状态: $new_status, 进度: $progress%"
}
```

---

## Todo 完成触发同步

### 监听完成事件

```bash
#!/bin/bash
# complete-and-sync.sh
# 完成 Todo 并同步 Plan 进度

complete_todo_and_sync() {
  local todo_code=$1

  # 完成 Todo
  llm-memory todo complete "$todo_code"
  echo "✅ 已完成: $todo_code"

  # 提取 Plan code
  # todo-auth-refactor-1-1 -> plan-auth-refactor
  local plan_part=$(echo "$todo_code" | sed 's/^todo-//' | sed 's/-[0-9]*-[0-9]*$//')
  local plan_code="plan-$plan_part"

  # 同步进度
  echo "🔄 同步计划进度..."
  sync_plan_status "$plan_code"
}

# 使用
complete_todo_and_sync "todo-auth-refactor-1-1"
```

---

## 批量同步

### 同步所有活跃计划

```bash
#!/bin/bash
# sync-all-plans.sh

echo "🔄 同步所有计划进度..."
echo ""

# 获取所有进行中的计划
PLANS=$(llm-memory plan list --format json | jq -r '.[] | select(.status == "in_progress") | .code')

for plan_code in $PLANS; do
  echo "📋 $plan_code"

  # 计算进度
  TODOS=$(llm-memory todo list --filter "todo-${plan_code#plan-}-*" --format json)
  TOTAL=$(echo "$TODOS" | jq 'length')
  COMPLETED=$(echo "$TODOS" | jq '[.[] | select(.status == 2)] | length')

  if [ "$TOTAL" -gt 0 ]; then
    PROGRESS=$((COMPLETED * 100 / TOTAL))
    llm-memory plan update "$plan_code" --progress "$PROGRESS"
    echo "   进度: $COMPLETED/$TOTAL ($PROGRESS%)"
  else
    echo "   无关联待办"
  fi
  echo ""
done

echo "✅ 同步完成"
```

---

## 自动化触发

### 使用 watch 监控

```bash
# 每 30 秒同步一次
watch -n 30 './sync-all-plans.sh'
```

### 使用 cron 定时任务

```bash
# 每小时同步一次
0 * * * * /path/to/sync-all-plans.sh >> /var/log/plan-sync.log 2>&1
```

---

## 冲突处理

### 手动进度 vs 自动计算

如果手动设置了 Plan 进度，自动同步可能会覆盖：

```bash
#!/bin/bash
# smart-sync.sh
# 智能同步，避免覆盖手动设置

smart_sync() {
  local plan_code=$1

  # 获取当前计划
  local plan=$(llm-memory plan get "$plan_code" --format json)
  local current_progress=$(echo "$plan" | jq '.progress')

  # 计算 Todo 进度
  local todos=$(llm-memory todo list --filter "todo-${plan_code#plan-}-*" --format json)
  local total=$(echo "$todos" | jq 'length')

  if [ "$total" -eq 0 ]; then
    echo "⚠️ 无关联 Todo，保持手动进度"
    return
  fi

  local completed=$(echo "$todos" | jq '[.[] | select(.status == 2)] | length')
  local calc_progress=$((completed * 100 / total))

  # 只在计算进度大于当前进度时更新（避免回退）
  if [ "$calc_progress" -gt "$current_progress" ]; then
    llm-memory plan update "$plan_code" --progress "$calc_progress"
    echo "📈 进度更新: $current_progress% -> $calc_progress%"
  else
    echo "ℹ️ 进度保持: $current_progress% (计算值: $calc_progress%)"
  fi
}
```

---

## 调试输出

### 详细日志模式

```bash
#!/bin/bash
# sync-with-debug.sh

DEBUG=${DEBUG:-0}

debug_log() {
  if [ "$DEBUG" -eq 1 ]; then
    echo "[DEBUG] $1"
  fi
}

sync_plan_with_debug() {
  local plan_code=$1

  debug_log "开始同步: $plan_code"

  local todos=$(llm-memory todo list --filter "todo-${plan_code#plan-}-*" --format json)
  debug_log "获取到 Todo: $(echo "$todos" | jq 'length') 个"

  local total=$(echo "$todos" | jq 'length')
  local completed=$(echo "$todos" | jq '[.[] | select(.status == 2)] | length')
  local in_progress=$(echo "$todos" | jq '[.[] | select(.status == 1)] | length')

  debug_log "统计: 总数=$total, 完成=$completed, 进行中=$in_progress"

  if [ "$total" -gt 0 ]; then
    local progress=$((completed * 100 / total))
    debug_log "计算进度: $progress%"

    llm-memory plan update "$plan_code" --progress "$progress"
    echo "✅ $plan_code: $progress%"
  fi
}

# 启用调试模式
DEBUG=1 sync_plan_with_debug "plan-auth-refactor"
```
