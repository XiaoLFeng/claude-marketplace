---
name: record-knowledge
description: "知识记录器 - 保存重要信息到知识库。适用：用户说'记录这个'、'保存'、做出技术决策、排查完 Bug。不适用：临时信息用 todo，只是询问不需记录。"
---

# Record Knowledge

保存重要信息到知识库，确保关键知识不会丢失。

## 触发条件

- 用户说"记录这个"、"保存知识"、"记住"、"记录决策"
- 做出技术方案选型后
- 排查复杂 Bug（>30分钟）后
- 制定项目规范后
- 发现可复用代码模式后

## 不触发条件

- 只是询问问题，不需要持久化
- 临时信息（使用 manage-tasks 创建待办）
- 简单操作记录

## 操作流程

### Step 1: 提取关键信息

从对话中提取：
- 标题（简洁描述）
- 内容（详细信息）
- 分类（技术决策/Bug解决/代码示例/项目规范）
- 标签（便于搜索）

### Step 2: 生成 Code

```
格式：mem-<主题>
规则：全小写 + 连字符，≥3字符，字母开头结尾

示例：
  mem-jwt-decision      ✅
  mem-login-bug-fix     ✅
  mem-api-design        ✅
  Memory_001            ❌
```

### Step 3: 创建记忆

```javascript
memory_create({
  code: "mem-xxx",
  title: "标题",
  content: "详细内容...",
  category: "技术决策",
  tags: ["auth", "security"],
  global: false  // true=全局可见, false=项目私有
})
```

### Step 4: 确认保存

```
✅ 知识已保存

📚 [mem-jwt-decision] JWT vs Session 技术选型
   分类: 技术决策
   标签: auth, security
   范围: 当前项目

需要时可以通过 search-history 搜索~
```

## 分类建议

| 分类 | 适用场景 |
|------|---------|
| 技术决策 | 框架选型、库选择、架构设计 |
| Bug解决 | 复杂问题排查过程和方案 |
| 代码示例 | 可复用的代码片段 |
| 项目规范 | 编码规范、命名约定 |
| 配置模板 | 环境配置、部署配置 |

## MCP 工具使用

- `memory_create` - 创建记忆

详见：
- [Code 格式规则](./references/code-format.md)
- [使用示例](./references/examples.md)
