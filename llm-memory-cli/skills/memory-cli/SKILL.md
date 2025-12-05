---
name: memory-cli
description: |
  LLM-Memory 知识库管理工具 (CLI版本) - 持久化存储重要信息、架构决策、代码示例和最佳实践。

  **何时调用此 Skill：**
  - 用户说"记录这个"、"保存知识"、"创建笔记"、"记录决策"
  - 用户需要"搜索记忆"、"查找之前的记录"、"回顾知识"
  - 用户提到"知识库"、"文档管理"、"信息存储"

  **不调用此 Skill：**
  - 只是询问问题而不需要持久化
  - 临时信息（使用 Todo 或 Plan 更合适）
---

# Memory CLI 管理 Skill

管理项目知识库，存储架构决策、问题解决方案、代码示例等重要信息。

## ⚡ 快速参考

### Code 格式

```
全小写 + 连字符，字母开头/结尾，≥3 字符
推荐：mem-<主题>
✅ mem-api-design  ❌ Memory_001
全局唯一（整个系统不能重复）
```

### 何时创建 Memory

**必须创建：**
- 架构决策和技术选型理由
- 复杂 Bug 的排查过程和解决方案
- 重要的代码示例和配置模板
- 项目规范和约定

**可选创建：**
- 调试技巧和工具使用心得
- 第三方库的使用经验
- 环境配置说明

---

## 🔧 核心操作

### 创建记忆

```bash
./main memory create \
  --code "mem-xxx" \
  --title "标题" \
  --content "详细内容" \
  --category "分类" \
  --tags "tag1,tag2,tag3" \
  [--global]
```

### 搜索记忆

```bash
# 关键词搜索（在 title 和 content 中）
./main memory search --keyword "关键词"
```

### 查看和管理

```bash
# 列出所有记忆
./main memory list

# 获取详情
./main memory get --code "mem-xxx"

# 删除记忆
./main memory delete --code "mem-xxx"
```

---

## 📚 CLI 命令清单

- `memory create` - 创建新记忆
- `memory list` - 列出所有记忆
- `memory search` - 搜索记忆
- `memory get` - 获取详情
- `memory delete` - 删除记忆

详见：[完整命令参考](./references/commands.md)

---

## 🔗 参考文档

- [完整命令参考](./references/commands.md) - 所有命令的详细说明
- [使用示例](./references/examples.md) - 真实场景案例
- [Code 格式](../shared-references/code-format.md) - 格式规则详解
- [故障排除](../shared-references/troubleshooting.md) - 错误处理
- [最佳实践](../shared-references/best-practices.md) - 优化建议
