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
- 用户问"历史上如何处理"、"以前的记录"

## 不触发条件

- 创建新记录 → 使用 record-decision
- 加载项目上下文 → 使用 load-context
- 管理任务 → 使用 task-* 系列

## 操作流程

### 搜索记忆

```javascript
const results = await memory_search({
  keyword: "jwt",
  scope: "all"  // personal/group/global/all
});
```

### 获取详情

```javascript
// 对搜索结果中的相关记忆获取详情
const detail = await memory_get({
  code: "mem-auth-jwt-decision"
});
```

### 列出所有记忆

```javascript
const all = await memory_list({
  scope: "all"
});
```

## 搜索策略

### 关键词搜索

```javascript
// 搜索标题和内容中包含关键词的记忆
memory_search({ keyword: "jwt" })
memory_search({ keyword: "登录" })
memory_search({ keyword: "性能优化" })
```

### 多关键词

```javascript
// 可以尝试多个相关关键词
memory_search({ keyword: "认证" })
memory_search({ keyword: "token" })
memory_search({ keyword: "auth" })
```

## 作用域说明

| Scope | 说明 |
|-------|------|
| personal | 当前目录私有 |
| group | 组内共享 |
| global | 全局可见 |
| all | 全部可见（默认）|

## 使用场景

### 场景 1: 查找技术决策

```markdown
用户: "之前 JWT 是怎么选型的？"

1. 调用 search-history skill
2. 搜索关键词: "jwt"
3. 找到 mem-auth-jwt-decision
4. 获取详情并展示
```

### 场景 2: 查找 Bug 修复经验

```markdown
用户: "登录超时问题之前怎么解决的？"

1. 调用 search-history skill
2. 搜索关键词: "登录超时"
3. 找到相关记录
4. 展示解决方案
```

### 场景 3: 查找设计规范

```markdown
用户: "我们的 API 设计规范是什么？"

1. 调用 search-history skill
2. 搜索关键词: "api 规范"
3. 找到 mem-api-design-standard
4. 展示规范内容
```

## 输出示例

### 搜索结果

```
🔍 搜索结果: "jwt"

找到 2 条相关记录:

📌 [mem-auth-jwt-decision] 认证方案选型：JWT vs Session
   分类: 技术决策
   摘要: 选择 JWT + Refresh Token 方案...
   更新: 2024-01-10

📌 [mem-auth-jwt-refresh] JWT Refresh Token 实现
   分类: 最佳实践
   摘要: Refresh Token 的实现细节...
   更新: 2024-01-15

需要查看哪条记录的详情？
```

### 详情展示

```
💡 记忆详情

[mem-auth-jwt-decision] 认证方案选型：JWT vs Session

分类: 技术决策
标签: auth, jwt, 架构
创建: 2024-01-10
更新: 2024-01-10

───────────────────────────────

## 背景
需要为用户认证系统选择令牌方案

## 选项对比
| 方案 | 优点 | 缺点 |
|------|------|------|
| JWT | 无状态、可扩展 | 无法即时撤销 |
| Session | 可即时撤销 | 需要存储 |

## 决策
选择 JWT + Refresh Token 方案

## 理由
1. 系统需要水平扩展
2. 通过短有效期 + Refresh 降低风险
3. 团队有 JWT 经验
```

## MCP 工具

- `memory_search` - 搜索记忆
- `memory_list` - 列出记忆
- `memory_get` - 获取详情

详见：[搜索技巧](./references/search-tips.md)
