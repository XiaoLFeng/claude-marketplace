---
name: search-history
description: "历史搜索器 - 搜索之前的记录和经验。适用：用户问'之前怎么做的'、'有没有相关记录'、'历史上...'。不适用：创建新记录用 record-knowledge。"
---

# Search History

搜索之前的记录和经验，快速找到相关知识。

## 触发条件

- 用户问"之前怎么做的"、"上次是怎么处理的"
- 用户说"搜索"、"查找"、"有没有..."
- 用户提到"历史"、"之前"、"以前"
- 需要参考过往经验

## 不触发条件

- 需要创建新记录（使用 record-knowledge）
- 只需要列出全部记录（直接调用 memory_list）
- 加载项目上下文（使用 session-init 或 plan-mode-context）

## 操作流程

### Step 1: 提取关键词

从用户请求中提取搜索关键词：
- "之前怎么处理 CORS 问题的" → 关键词: "cors"
- "有没有 JWT 相关的记录" → 关键词: "jwt"

### Step 2: 搜索记忆

```javascript
const results = await memory_search({
  keyword: "关键词",
  scope: "all"  // personal/group/global/all
});
```

### Step 3: 获取详情

对相关结果获取完整内容：

```javascript
for (const mem of results.slice(0, 5)) {
  const detail = await memory_get({ code: mem.code });
  // 展示详情
}
```

### Step 4: 展示结果

```
🔍 搜索结果: "jwt"

找到 3 条相关记录：

1. 📚 [mem-jwt-decision] JWT vs Session 技术选型
   分类: 技术决策 | 标签: auth, security
   摘要: 选择 JWT 的原因和实施要点...

2. 📚 [mem-jwt-refresh] JWT Refresh Token 实现
   分类: 代码示例 | 标签: jwt, token
   摘要: Refresh Token 的实现方案...

3. 📚 [mem-jwt-blacklist] JWT 黑名单机制
   分类: 技术决策 | 标签: jwt, redis
   摘要: 使用 Redis 实现 Token 黑名单...

需要查看某条记录的完整内容吗？
```

## MCP 工具使用

- `memory_search` - 搜索记忆
- `memory_get` - 获取记忆详情

详见：[使用示例](./references/examples.md)
