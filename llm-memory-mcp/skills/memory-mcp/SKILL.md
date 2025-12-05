---
name: memory-mcp
description: |
  LLM-Memory 知识库管理工具 (MCP版本) - 使用 MCP 工具持久化存储重要信息。

  **何时调用此 Skill：**
  - 用户说"记录这个"、"保存知识"、"创建笔记"、"记录决策"
  - 用户需要"搜索记忆"、"查找之前的记录"、"回顾知识"
  - 用户提到"知识库"、"文档管理"

  **不调用此 Skill：**
  - 只是询问问题而不需要持久化
  - 临时任务（使用 Todo 或 Plan 更合适）
---

# Memory MCP 管理 Skill

使用 MCP 工具管理项目知识库，存储架构决策、问题解决方案、代码示例。

## ⚡ 快速参考

### Code 格式

```
全小写 + 连字符，字母开头/结尾，≥3 字符
推荐：mem-<主题>
✅ mem-jwt-decision  ❌ Memory_001
全局唯一（整个系统不能重复）
```

### 作用域选择

```
global: true       - 全局可见（通用知识、跨项目配置）
scope: "personal"  - 当前目录私有（项目特定记录）
scope: "group"     - 组内共享（团队协作项目）
```

### 何时创建 Memory

**必须创建：**
- 架构决策和技术选型理由
- 复杂 Bug 的排查过程和解决方案
- 重要的代码示例和配置模板
- 项目规范和约定

---

## 🔧 核心操作

### 创建记忆

```javascript
memory_create({
  code: "mem-xxx",
  title: "标题",
  content: "详细内容",
  category: "分类",
  tags: ["tag1", "tag2"],
  priority: 2,
  global: false,
  scope: "personal"
})
```

### 搜索记忆

```javascript
memory_search({
  keyword: "关键词",
  scope: "all"  // personal/group/global/all
})
```

### 查看和管理

```javascript
// 列出记忆
memory_list({ scope: "all" })

// 获取详情
memory_get({ code: "mem-xxx" })

// 更新记忆
memory_update({ code: "mem-xxx", title: "新标题" })

// 删除记忆
memory_delete({ code: "mem-xxx" })
```

---

## 📚 MCP 工具清单

- `memory_list` - 列出记忆
- `memory_create` - 创建记忆
- `memory_get` - 获取详情
- `memory_update` - 更新记忆
- `memory_delete` - 删除记忆
- `memory_search` - 搜索记忆

详见：[完整工具参考](./references/tools.md)

---

## 🔗 参考文档

- [完整工具参考](./references/tools.md) - 所有 MCP 工具详细说明
- [使用示例](./references/examples.md) - 真实场景案例
- [作用域系统](./references/tools.md#作用域系统) - 作用域详解
- [Code 格式](../shared-references/code-format.md) - 格式规则详解
- [故障排除](../shared-references/troubleshooting.md) - 错误处理
- [最佳实践](../shared-references/best-practices.md) - 优化建议
