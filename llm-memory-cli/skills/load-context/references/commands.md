# Load Context - CLI 命令参考

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
# 搜索所有相关记忆
llm-memory memory search "认证"

# 指定作用域搜索
llm-memory memory search "认证" --scope personal
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
# 列出所有计划
llm-memory plan list

# 列出个人计划
llm-memory plan list --scope personal
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
# 列出所有待办
llm-memory todo list

# 列出个人待办
llm-memory todo list --scope personal
```

**输出示例**：

```
✅ 待办列表：

🔴 ⏳ [todo-fix-login] 修复登录 Bug
   优先级: 紧急 | 状态: 进行中

🟠 ⏸️ [todo-add-cache] 添加缓存层
   优先级: 高 | 状态: 待处理

🟢 ✅ [todo-update-docs] 更新文档
   优先级: 低 | 状态: 已完成
```

---

## 组合使用

### 完整上下文加载脚本

```bash
#!/bin/bash
# load-context.sh

echo "📥 加载上下文..."
echo ""

echo "=== 💡 相关记忆 ==="
llm-memory memory search "$1" --scope all
echo ""

echo "=== 📋 活跃计划 ==="
llm-memory plan list --scope all
echo ""

echo "=== ✅ 待办事项 ==="
llm-memory todo list --scope all
echo ""

echo "✅ 上下文加载完成"
```

**使用**：

```bash
./load-context.sh "认证"
```

---

## 输出格式

### 状态图标说明

| 图标 | 含义 |
|------|------|
| ✅ | 已完成 |
| ⏳ | 进行中 |
| ⏸️ | 待处理 |
| 🔴 | 紧急优先级 |
| 🟠 | 高优先级 |
| 🟡 | 中优先级 |
| 🟢 | 低优先级 |

---

## 参考链接

- [Memory CLI 完整文档](../../memory-cli/references/commands.md)
- [Plan CLI 完整文档](../../plan-cli/references/commands.md)
- [Todo CLI 完整文档](../../todo-cli/references/commands.md)
