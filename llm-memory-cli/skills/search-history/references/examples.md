# Search History - 使用示例

## 场景 1：查询历史决策

### 用户请求

```
"之前认证系统是怎么做的？"
```

### 识别关键词

```bash
# 识别到 "之前" 触发历史查询
KEYWORD="认证"
```

### 执行搜索

```bash
# 搜索记忆
llm-memory memory search "认证" --scope all

# 搜索计划（需要过滤）
llm-memory plan list --scope all | grep -i "认证"

# 搜索待办（需要过滤）
llm-memory todo list --scope all | grep -i "认证"
```

### 输出结果

```
🔍 搜索 "认证" 的结果：

### 💡 Memory 记录
- [mem-jwt-decision] JWT 认证方案选型
  分类: 架构决策 | 标签: auth, jwt
- [mem-auth-flow] 用户认证流程设计
  分类: 技术文档 | 标签: auth, flow

### 📋 Plan 计划
- ⏳ [plan-auth-refactor] 用户认证系统重构 (进度: 45%)

### ✅ Todo 任务
- ✅ 🔴 [todo-auth-refactor-1-1] 设计 users 表结构
- ✅ 🔴 [todo-auth-refactor-1-2] 设计 refresh_tokens 表结构
- ⏳ 🟠 [todo-auth-refactor-2-1] 实现 JWT 生成逻辑
```

---

## 场景 2：查找 Bug 解决方案

### 用户请求

```
"上次内存泄漏是怎么解决的？"
```

### 执行搜索

```bash
llm-memory memory search "内存泄漏" --scope all
```

### 输出结果

```
🔍 搜索 "内存泄漏" 的结果：

### 💡 Memory 记录
- [mem-memory-leak-websocket] WebSocket 内存泄漏问题排查与解决
  分类: 问题排查 | 标签: bug, memory-leak, nodejs

### ✅ Todo 任务
- ✅ 🔴 [todo-fix-memory-leak] 修复内存泄漏问题
```

### 查看详情

```bash
llm-memory memory get mem-memory-leak-websocket
```

输出：

```
💡 记忆详情：

Code: mem-memory-leak-websocket
标题: WebSocket 内存泄漏问题排查与解决
分类: 问题排查

内容:
---
# WebSocket 内存泄漏问题排查与解决

## 根本原因
WebSocket 断开重连时，旧的事件监听器没有被移除

## 解决方案
socket.on('disconnect', () => {
  socket.removeAllListeners();  // 先清理
  socket.connect();
});
---
```

---

## 场景 3：检查任务状态

### 用户请求

```
"上次那个登录功能做完了吗？"
```

### 执行搜索

```bash
llm-memory todo list --scope all | grep -i "登录"
```

### 输出结果

```
🔍 搜索 "登录" 的结果：

### ✅ Todo 任务
- ✅ 🔴 [todo-fix-login-bug] 修复登录 Bug - 已完成
- ✅ 🟡 [todo-add-login-validation] 添加登录验证 - 已完成
- ⏳ 🟡 [todo-login-rate-limit] 登录限流 - 进行中

答：登录相关的大部分任务已完成，还有一个"登录限流"正在进行中。
```

---

## 场景 4：综合历史查询

### 用户请求

```
"之前有没有做过数据库优化相关的工作？"
```

### 执行完整搜索脚本

```bash
#!/bin/bash
# search-all.sh

KEYWORD="数据库优化"

echo "🔍 搜索 \"$KEYWORD\" 的结果："
echo ""

echo "### 💡 Memory 记录"
llm-memory memory search "$KEYWORD" --scope all
echo ""

echo "### 📋 Plan 计划"
llm-memory plan list --scope all | grep -i "数据库\|优化"
echo ""

echo "### ✅ Todo 任务"
llm-memory todo list --scope all | grep -i "数据库\|优化"
```

### 输出结果

```
🔍 搜索 "数据库优化" 的结果：

### 💡 Memory 记录
- [mem-query-optimization] 查询优化最佳实践
  分类: 最佳实践 | 标签: database, performance
- [mem-index-strategy] 索引策略设计
  分类: 架构决策 | 标签: database, index

### 📋 Plan 计划
- ✅ [plan-db-optimization] 数据库性能优化 (进度: 100%)

### ✅ Todo 任务
- ✅ 🟠 [todo-add-index] 添加缺失的索引
- ✅ 🟠 [todo-optimize-slow-query] 优化慢查询
- ✅ 🟡 [todo-update-explain] 更新查询分析文档
```

---

## 最佳实践

### 1. 关键词提取

```bash
# 从用户输入提取关键词
extract_keywords() {
  local input="$1"

  # 移除常见停用词
  echo "$input" | sed -E 's/(之前|上次|以前|有没有|是怎么|做过|的)//g' | tr -s ' '
}

# 使用
KEYWORDS=$(extract_keywords "之前认证系统是怎么做的")
# 结果: "认证系统"
```

### 2. 多关键词搜索

```bash
#!/bin/bash
# multi-search.sh

search_multiple_keywords() {
  local keywords="$@"

  for keyword in $keywords; do
    echo "=== 搜索: $keyword ==="
    llm-memory memory search "$keyword" --scope all
    echo ""
  done
}

# 使用
search_multiple_keywords "认证" "JWT" "Token"
```

### 3. 结果去重

```bash
#!/bin/bash
# 合并多次搜索结果并去重

search_and_merge() {
  local keywords="$@"
  local all_results=""

  for keyword in $keywords; do
    results=$(llm-memory memory search "$keyword" --scope all 2>/dev/null)
    all_results="$all_results\n$results"
  done

  # 去重（按 code）
  echo -e "$all_results" | sort -u
}
```

### 4. 快捷别名

```bash
# 添加到 ~/.bashrc 或 ~/.zshrc

# 快速搜索历史
alias hist='./search-history.sh'

# 搜索记忆
alias mem-search='llm-memory memory search'

# 搜索计划
alias plan-search='llm-memory plan list | grep -i'

# 搜索待办
alias todo-search='llm-memory todo list | grep -i'
```

使用：

```bash
hist "认证"
mem-search "JWT"
plan-search "优化"
todo-search "登录"
```

---

## 完整搜索脚本

```bash
#!/bin/bash
# search-history.sh
# 综合历史搜索工具

set -e

KEYWORD="$1"

if [ -z "$KEYWORD" ]; then
  echo "用法: $0 <keyword>"
  echo "示例: $0 认证"
  exit 1
fi

echo "🔍 搜索 \"$KEYWORD\" 的结果："
echo "================================"
echo ""

# Memory 搜索
echo "### 💡 Memory 记录"
MEMORIES=$(llm-memory memory search "$KEYWORD" --scope all 2>/dev/null)
if [ -n "$MEMORIES" ]; then
  echo "$MEMORIES"
else
  echo "未找到相关记录"
fi
echo ""

# Plan 搜索
echo "### 📋 Plan 计划"
PLANS=$(llm-memory plan list --scope all 2>/dev/null | grep -i "$KEYWORD" || true)
if [ -n "$PLANS" ]; then
  echo "$PLANS"
else
  echo "未找到相关计划"
fi
echo ""

# Todo 搜索
echo "### ✅ Todo 任务"
TODOS=$(llm-memory todo list --scope all 2>/dev/null | grep -i "$KEYWORD" || true)
if [ -n "$TODOS" ]; then
  echo "$TODOS"
else
  echo "未找到相关任务"
fi
echo ""

echo "================================"
echo "✅ 搜索完成"
```
