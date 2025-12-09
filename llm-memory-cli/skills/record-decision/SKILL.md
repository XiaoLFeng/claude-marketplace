---
name: record-decision
description: "Decision recorder - Record technical decisions, solution choices, and experience summaries. Use when: after tech selection, implementation decision, bug investigation, standards established. Not for: temporary info (use task-add)."
---

# Record Decision

记录技术决策、方案选型和经验总结，保存到知识库供后续复用。

## 触发条件

- 完成技术选型（框架、库、工具）
- 确定实现方案
- 排查完复杂 Bug
- 制定项目规范
- 用户说"记录这个"、"保存决策"、"记住"

## 不触发条件

- 临时任务信息 → 使用 task-add
- 搜索历史记录 → 使用 search-history

## 操作流程

### 创建记忆

```bash
llm-memory memory add mem-auth-jwt-decision "认证方案选型：JWT vs Session" \
  --category "技术决策" \
  --tags "auth,jwt,架构"
```

然后使用编辑器添加详细内容。

## Code 命名规范

```
格式: mem-<项目>-<描述>
正则: ^[a-z][a-z\-]*[a-z]$

示例:
  mem-auth-jwt-decision     ✅
  mem-api-design-standard   ✅
  mem-bug-fix-login         ✅
```

## 分类建议

| 分类 | 适用场景 |
|------|----------|
| 技术决策 | 框架选型、架构决策 |
| 设计规范 | API 规范、代码规范 |
| 问题解决 | Bug 修复、故障排查 |
| 最佳实践 | 开发经验、优化技巧 |

## 使用场景

### 场景 1: 技术选型后

```markdown
完成 JWT vs Session 选型讨论后：

1. 调用 record-decision skill
2. 执行: llm-memory memory add mem-auth-jwt-decision "..."
3. 添加详细内容
```

### 场景 2: Bug 修复后

```markdown
排查并修复复杂 Bug 后：

1. 调用 record-decision skill
2. 执行: llm-memory memory add mem-bug-login-timeout "..."
3. 记录问题、原因、解决方案
```

## 输出示例

```
✅ 决策已记录

💡 [mem-auth-jwt-decision] 认证方案选型：JWT vs Session
   分类: 技术决策
   标签: auth, jwt, 架构

已保存到知识库~
```

## CLI 命令

- `llm-memory memory add <code> <title>` - 创建记忆
- `--category` - 设置分类
- `--tags` - 设置标签（逗号分隔）

详见：
- [Code 格式规范](./references/code-format.md)
- [内容模板](./references/templates.md)
