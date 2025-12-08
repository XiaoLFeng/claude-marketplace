# Record Decision - MCP 工具参考

## 使用的 MCP 工具

### memory_create

创建记忆条目。

**参数**：

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| code | string | ✅ | 记忆唯一标识码 |
| title | string | ✅ | 记忆标题 |
| content | string | ✅ | 详细内容（支持 Markdown） |
| category | string | ❌ | 分类（默认"默认"）|
| tags | array | ❌ | 标签列表 |
| priority | number | ❌ | 优先级 1-4（默认 2）|
| global | boolean | ❌ | 是否全局可见（默认 false）|

**示例**：

```javascript
memory_create({
  code: "mem-jwt-decision",
  title: "JWT 认证方案选型",
  content: `
# JWT vs Session 认证方案选型

## 背景
需要选择合适的认证方案...

## 最终决策
选择 JWT + Refresh Token
  `,
  category: "架构决策",
  tags: ["auth", "jwt", "architecture"],
  priority: 3,
  global: false
})
```

**返回**：

```json
{
  "success": true,
  "code": "mem-jwt-decision",
  "message": "Memory created successfully"
}
```

---

### memory_search

搜索记忆（用于检查是否已记录）。

**参数**：

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| keyword | string | ✅ | 搜索关键词 |
| scope | string | ❌ | 作用域：personal/group/global/all |

**示例**：

```javascript
// 检查是否已有类似记录
memory_search({
  keyword: "JWT 认证",
  scope: "all"
})
```

---

## 决策类型和分类

### 推荐分类

| 分类 | 说明 | 示例场景 |
|------|------|---------|
| 架构决策 | 技术选型、架构设计 | 选择框架、数据库 |
| 问题排查 | Bug 修复、故障处理 | 内存泄漏、性能问题 |
| 设计规范 | 代码规范、API 设计 | 命名约定、接口规范 |
| 最佳实践 | 可复用模式、优化技巧 | 缓存策略、错误处理 |

### 推荐标签

```javascript
// 技术领域
["auth", "api", "database", "security", "performance"]

// 决策类型
["architecture", "design", "bugfix", "optimization"]

// 技术栈
["nodejs", "react", "postgresql", "redis", "jwt"]
```

---

## Code 命名规则

### 格式

```
mem-<主题>[-<补充信息>]
```

### 推荐命名

| 场景 | Code 示例 |
|------|----------|
| 技术选型 | `mem-jwt-decision`, `mem-redis-vs-memcached` |
| Bug 修复 | `mem-memory-leak-fix`, `mem-api-timeout-solution` |
| 设计规范 | `mem-api-design-convention`, `mem-error-handling` |
| 最佳实践 | `mem-caching-strategy`, `mem-logging-pattern` |

---

## 内容模板

### 技术选型模板

```markdown
# <技术选型标题>

## 背景
<为什么需要做这个选型>

## 候选方案

### 方案 1: <名称>
- **优点**：
  - ...
- **缺点**：
  - ...

### 方案 2: <名称>
- **优点**：
  - ...
- **缺点**：
  - ...

## 最终决策
<选择了什么方案>

## 理由
<为什么选择这个方案>

## 影响
<这个决策带来的影响>

## 参考资料
- <相关链接>
```

### Bug 排查模板

```markdown
# <问题标题>

## 背景
<问题现象和影响>

## 排查过程
1. <第一步>
2. <第二步>
3. ...

## 根本原因
<问题的根本原因>

## 解决方案
<如何解决的>

## 预防措施
<如何避免再次发生>

## 相关代码
<代码片段或链接>
```

---

## 参考链接

- [Memory MCP 工具完整文档](../../memory-mcp/references/tools.md)
- [Code 格式规范](../../shared-references/code-format.md)
