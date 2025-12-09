---
name: search-history
description: "History searcher - Search previous records and experiences. Use when: user asks 'how did we do before', 'any related records', 'historical approach'. Not for: creating records (use record-decision), loading context (use load-context)."
---

# Search History

搜索之前的记录和经验，从知识库中查找相关信息。

## 触发条件

- 用户问"之前怎么做的"
- 用户说"搜索"、"查找"、"有没有..."
- 需要参考历史经验
- 用户问"历史上如何处理"

## 不触发条件

- 创建新记录 → 使用 record-decision
- 加载项目上下文 → 使用 load-context

## 操作流程

### 搜索记忆

```bash
llm-memory memory search "jwt"
```

### 获取详情

```bash
llm-memory memory show mem-auth-jwt-decision
```

### 列出所有记忆

```bash
llm-memory memory list
```

## 搜索策略

### 关键词搜索

```bash
llm-memory memory search "jwt"
llm-memory memory search "登录"
llm-memory memory search "性能优化"
```

### 多关键词尝试

```bash
llm-memory memory search "认证"
llm-memory memory search "token"
llm-memory memory search "auth"
```

## 使用场景

### 场景 1: 查找技术决策

```markdown
用户: "之前 JWT 是怎么选型的？"

1. 调用 search-history skill
2. 执行: llm-memory memory search "jwt"
3. 找到 mem-auth-jwt-decision
4. 执行: llm-memory memory show mem-auth-jwt-decision
5. 展示详情
```

### 场景 2: 查找 Bug 修复经验

```markdown
用户: "登录超时问题之前怎么解决的？"

1. 调用 search-history skill
2. 执行: llm-memory memory search "登录超时"
3. 展示解决方案
```

## 输出示例

### 搜索结果

```
🔍 搜索结果: "jwt"

找到 2 条相关记录:

📌 [mem-auth-jwt-decision] 认证方案选型：JWT vs Session
   分类: 技术决策
   更新: 2024-01-10

📌 [mem-auth-jwt-refresh] JWT Refresh Token 实现
   分类: 最佳实践
   更新: 2024-01-15

需要查看哪条记录的详情？
```

### 详情展示

```
💡 记忆详情

[mem-auth-jwt-decision] 认证方案选型

分类: 技术决策
标签: auth, jwt

───────────────────────────────

## 背景
需要为用户认证系统选择令牌方案

## 决策
选择 JWT + Refresh Token 方案

## 理由
1. 系统需要水平扩展
2. 团队有 JWT 经验
```

## CLI 命令

- `llm-memory memory search <keyword>` - 搜索记忆
- `llm-memory memory list` - 列出记忆
- `llm-memory memory show <code>` - 获取详情

详见：[搜索技巧](./references/search-tips.md)
