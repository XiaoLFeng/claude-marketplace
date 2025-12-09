# Search History 使用示例

## 示例 1: 搜索技术决策

**用户请求**：之前是怎么选择认证方案的？

**操作**：

```javascript
// 搜索相关记忆
const results = await memory_search({
  keyword: "认证",
  scope: "all"
});

// 获取详情
for (const mem of results) {
  const detail = await memory_get({ code: mem.code });
  console.log(detail);
}
```

**输出**：

```
🔍 搜索结果: "认证"

找到 2 条相关记录：

1. 📚 [mem-jwt-decision] JWT vs Session 技术选型
   分类: 技术决策

   ## 决策背景
   用户认证方案选型

   ## 最终决策
   选择 JWT

   ## 决策理由
   1. 微服务架构需要无状态
   2. 配合 Redis 实现 Token 黑名单
   ...

2. 📚 [mem-oauth-integration] OAuth 2.0 集成方案
   分类: 技术决策
   ...
```

## 示例 2: 搜索 Bug 解决方案

**用户请求**：之前有遇到过 CORS 问题吗？

**操作**：

```javascript
const results = await memory_search({
  keyword: "cors",
  scope: "all"
});
```

**输出**：

```
🔍 搜索结果: "cors"

找到 1 条相关记录：

1. 📚 [mem-login-cors-bug] 登录接口 CORS 问题排查
   分类: Bug解决

   ## 问题描述
   前端调用登录接口返回 CORS 错误

   ## 根本原因
   Spring Security 过滤器在 CORS 过滤器之前执行

   ## 解决方案
   ...
```

## 示例 3: 搜索代码模板

**用户请求**：有没有分页查询的模板？

**操作**：

```javascript
const results = await memory_search({
  keyword: "分页",
  scope: "all"
});
```

## 示例 4: 无结果处理

**用户请求**：之前有做过 GraphQL 相关的吗？

**输出**：

```
🔍 搜索结果: "graphql"

未找到相关记录。

建议：
- 尝试其他关键词
- 检查拼写是否正确
- 可能还没有相关记录，需要时可以创建
```

## 搜索技巧

1. **使用精确关键词**：如 "jwt" 而非 "token认证"
2. **尝试同义词**：搜不到时换个词试试
3. **组合搜索**：分多次搜索，综合结果
4. **注意范围**：scope 影响搜索范围
