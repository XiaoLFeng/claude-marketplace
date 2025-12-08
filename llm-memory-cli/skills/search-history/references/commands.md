# Search History - CLI 命令参考

## 使用的 CLI 命令

### llm-memory memory search

搜索记忆库中的相关记录。

**语法**：

```bash
llm-memory memory search <keyword> [--scope <scope>]
```

**参数**：

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| keyword | string | ✅ | 搜索关键词 |
| --scope | string | ❌ | 作用域：personal/group/global/all（默认 all） |

**示例**：

```bash
# 搜索认证相关记录
llm-memory memory search "认证"

# 只搜索全局记录
llm-memory memory search "认证" --scope global
```

**输出示例**：

```
🔍 搜索结果：

[mem-jwt-decision] JWT 认证方案选型
  分类: 架构决策 | 标签: auth, jwt
  创建: 2024-12-01

[mem-auth-flow] 用户认证流程设计
  分类: 技术文档 | 标签: auth, flow
  创建: 2024-11-28
```

---

### llm-memory plan list

列出所有计划。

**语法**：

```bash
llm-memory plan list [--scope <scope>]
```

**参数**：

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| --scope | string | ❌ | 作用域：personal/group/all（默认 all） |

**示例**：

```bash
llm-memory plan list
```

**输出示例**：

```
📋 计划列表：

⏳ [plan-auth-refactor] 用户认证系统重构
   进度: 45% | 状态: 进行中

✅ [plan-db-optimization] 数据库性能优化
   进度: 100% | 状态: 已完成
```

---

### llm-memory todo list

列出所有待办。

**语法**：

```bash
llm-memory todo list [--scope <scope>]
```

**参数**：

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| --scope | string | ❌ | 作用域：personal/group/all（默认 all） |

**示例**：

```bash
llm-memory todo list
```

**输出示例**：

```
✅ 待办列表：

🔴 ⏳ [todo-fix-login] 修复登录 Bug
   优先级: 紧急 | 状态: 进行中

🟠 ⏸️ [todo-add-cache] 添加缓存层
   优先级: 高 | 状态: 待处理
```

---

## 搜索策略

### 并行搜索脚本

```bash
#!/bin/bash
# search-history.sh
# 用法: ./search-history.sh <keyword>

KEYWORD="$1"

if [ -z "$KEYWORD" ]; then
  echo "用法: $0 <keyword>"
  exit 1
fi

echo "🔍 搜索 \"$KEYWORD\" 的结果："
echo ""

# 并行执行搜索
{
  echo "### 💡 Memory 记录"
  llm-memory memory search "$KEYWORD" --scope all 2>/dev/null || echo "无记录"
} &

{
  echo "### 📋 Plan 计划"
  llm-memory plan list --scope all 2>/dev/null | grep -i "$KEYWORD" || echo "无相关计划"
} &

{
  echo "### ✅ Todo 任务"
  llm-memory todo list --scope all 2>/dev/null | grep -i "$KEYWORD" || echo "无相关任务"
} &

wait
```

### 结果过滤

由于 `plan list` 和 `todo list` 不支持关键词搜索，需要在输出中过滤：

```bash
# 过滤计划
llm-memory plan list | grep -i "认证"

# 过滤待办
llm-memory todo list | grep -i "认证"
```

---

## 高级搜索

### 按分类搜索记忆

```bash
# 搜索架构决策类
llm-memory memory search "架构决策" --scope all

# 搜索问题排查类
llm-memory memory search "问题排查" --scope all
```

### 按标签搜索

```bash
# 搜索带 auth 标签的记忆
llm-memory memory search "auth" --scope all
```

### 组合搜索脚本

```bash
#!/bin/bash
# advanced-search.sh

search_by_category() {
  local category="$1"
  echo "📂 分类: $category"
  llm-memory memory list --scope all | grep "$category"
}

search_by_tag() {
  local tag="$1"
  echo "🏷️ 标签: $tag"
  llm-memory memory search "$tag" --scope all
}

search_by_status() {
  local status="$1"
  echo "📊 状态: $status"

  case "$status" in
    "进行中")
      llm-memory plan list | grep "⏳"
      llm-memory todo list | grep "⏳"
      ;;
    "已完成")
      llm-memory plan list | grep "✅"
      llm-memory todo list | grep "✅"
      ;;
    *)
      echo "未知状态"
      ;;
  esac
}
```

---

## 结果格式化

### 格式化脚本

```bash
#!/bin/bash
# format-results.sh

format_search_results() {
  local keyword="$1"

  echo "🔍 搜索 \"$keyword\" 的结果："
  echo ""

  # Memory
  echo "### 💡 Memory 记录"
  local memories=$(llm-memory memory search "$keyword" --scope all 2>/dev/null)
  if [ -n "$memories" ]; then
    echo "$memories" | while read -r line; do
      echo "- $line"
    done
  else
    echo "未找到相关记录"
  fi
  echo ""

  # Plans
  echo "### 📋 Plan 计划"
  local plans=$(llm-memory plan list --scope all 2>/dev/null | grep -i "$keyword")
  if [ -n "$plans" ]; then
    echo "$plans" | while read -r line; do
      echo "- $line"
    done
  else
    echo "未找到相关计划"
  fi
  echo ""

  # Todos
  echo "### ✅ Todo 任务"
  local todos=$(llm-memory todo list --scope all 2>/dev/null | grep -i "$keyword")
  if [ -n "$todos" ]; then
    echo "$todos" | while read -r line; do
      echo "- $line"
    done
  else
    echo "未找到相关任务"
  fi
}

# 使用
format_search_results "$1"
```

---

## 输出图标说明

### 状态图标

| 图标 | 含义 |
|------|------|
| ✅ | 已完成 |
| ⏳ | 进行中 |
| ⏸️ | 待处理 |
| ❌ | 已取消 |

### 优先级图标

| 图标 | 含义 |
|------|------|
| 🔴 | 紧急 (4) |
| 🟠 | 高 (3) |
| 🟡 | 中 (2) |
| 🟢 | 低 (1) |

### 类型图标

| 图标 | 含义 |
|------|------|
| 💡 | Memory 记忆 |
| 📋 | Plan 计划 |
| ✅ | Todo 待办 |

---

## 参考链接

- [Memory CLI 完整文档](../../memory-cli/references/commands.md)
- [Plan CLI 完整文档](../../plan-cli/references/commands.md)
- [Todo CLI 完整文档](../../todo-cli/references/commands.md)
