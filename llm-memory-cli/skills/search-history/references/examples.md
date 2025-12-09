# Search History - 使用示例

## 场景 1：查找之前的解决方案

### 用户提问
```
用户："之前那个 Redis 连接超时的问题是怎么解决的？"
```

### AI 操作
```bash
# 1. 搜索相关记忆
llm-memory memory search --keyword "Redis 连接超时"

# 2. 获取详细信息
llm-memory memory get --code mem-001
```

### 返回结果示例
```json
{
  "code": "mem-001",
  "title": "Redis 连接超时问题排查",
  "content": "问题：生产环境 Redis 连接频繁超时\n原因：连接池配置过小，maxTotal=8 不足以支撑高并发\n解决方案：调整连接池参数 maxTotal=50, maxIdle=20",
  "tags": ["Redis", "连接池", "超时"],
  "created_at": "2025-11-15T10:30:00Z"
}
```

### AI 回复
```
根据之前的记录（mem-001），Redis 连接超时问题是由连接池配置过小导致的。

解决方案：
- 将 maxTotal 从 8 调整到 50
- 将 maxIdle 调整到 20

这个调整后生产环境运行稳定，未再出现超时问题。
```

---

## 场景 2：搜索技术栈相关经验

### 用户提问
```
用户："我们之前用过 Docker 吗？有什么注意事项？"
```

### AI 操作
```bash
# 搜索 Docker 相关记忆
llm-memory memory search --keyword "Docker"
```

### 返回结果示例
```json
{
  "results": [
    {
      "code": "mem-015",
      "title": "Docker 部署最佳实践",
      "summary": "多阶段构建、镜像体积优化、网络配置",
      "relevance": 0.95
    },
    {
      "code": "mem-023",
      "title": "Docker Compose 配置要点",
      "summary": "服务依赖、环境变量、数据卷挂载",
      "relevance": 0.88
    }
  ]
}
```

### AI 回复
```
找到 2 条 Docker 相关记录：

1. Docker 部署最佳实践（mem-015）
   - 使用多阶段构建减小镜像体积
   - 注意网络配置，使用自定义网络

2. Docker Compose 配置要点（mem-023）
   - depends_on 控制服务启动顺序
   - 环境变量通过 .env 文件管理
   - 数据持久化使用命名卷

需要详细信息可以告诉我～
```

---

## 场景 3：查找项目规范

### 用户提问
```
用户："我们的 Git commit message 规范是什么？"
```

### AI 操作
```bash
# 搜索规范相关记忆
llm-memory memory search --keyword "commit message 规范"

# 获取详情
llm-memory memory get --code mem-008
```

### 返回结果示例
```json
{
  "code": "mem-008",
  "title": "项目 Git 规范",
  "content": "Commit Message 格式：\n- feat: 新功能\n- fix: 修复 bug\n- docs: 文档更新\n- style: 代码格式调整\n- refactor: 重构\n\n示例：feat: 添加用户登录功能",
  "type": "规范",
  "tags": ["Git", "规范", "commit"]
}
```

---

## 场景 4：搜索错误排查经验

### 用户提问
```
用户："有没有遇到过 'Cannot read property of undefined' 这种错误？"
```

### AI 操作
```bash
# 搜索错误相关记忆
llm-memory memory search --keyword "Cannot read property undefined"
```

### 返回结果示例
```json
{
  "results": [
    {
      "code": "mem-042",
      "title": "JavaScript 空值处理最佳实践",
      "summary": "使用可选链操作符、空值合并、类型守卫",
      "relevance": 0.91
    }
  ]
}
```

### AI 回复
```
找到相关记录！之前总结过这类问题的处理方法：

常用解决方案：
1. 使用可选链：obj?.property?.subProperty
2. 提供默认值：obj?.property ?? defaultValue
3. 添加类型守卫：if (obj && obj.property) { ... }

详见记录 mem-042。
```

---

## 场景 5：无匹配结果的处理

### 用户提问
```
用户："之前有处理过 GraphQL 相关的问题吗？"
```

### AI 操作
```bash
llm-memory memory search --keyword "GraphQL"
```

### 返回结果示例
```json
{
  "results": [],
  "message": "未找到相关记录"
}
```

### AI 回复
```
没有找到 GraphQL 相关的历史记录。

这可能是个新问题，我可以：
1. 帮你查找最新的技术文档
2. 一起探索解决方案
3. 解决后将经验记录下来（使用 record-knowledge skill）

需要我怎么帮你？
```

---

## 技巧总结

### 1. 关键词选择
- ✅ 使用技术名词：Redis、Docker、TypeScript
- ✅ 使用问题描述：连接超时、内存泄漏
- ✅ 使用功能描述：用户认证、文件上传
- ❌ 避免过于宽泛：问题、代码、项目

### 2. 结果处理
- 如果结果太多，可以添加更具体的关键词
- 如果没有结果，尝试使用同义词或更宽泛的术语
- 使用 `get` 命令获取完整详情

### 3. 与其他 Skill 配合
- 找不到记录 → 使用 `record-knowledge` 创建新记录
- 需要制定计划 → 使用 `plan-mcp` skill
- 需要添加待办 → 使用 `todo-mcp` skill

---

## 命令参考

```bash
# 基础搜索
llm-memory memory search --keyword "关键词"

# 按类型搜索
llm-memory memory search --keyword "关键词" --type "技术决策"

# 按标签搜索
llm-memory memory search --keyword "关键词" --tags "Redis,性能"

# 获取详情
llm-memory memory get --code mem-xxx

# 列出最近的记忆
llm-memory memory list --limit 10
```
