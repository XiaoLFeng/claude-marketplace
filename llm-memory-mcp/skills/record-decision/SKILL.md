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
- 得出重要结论

## 不触发条件

- 临时任务信息 → 使用 task-add
- 搜索历史记录 → 使用 search-history
- 只是询问，不需要记录
- 信息会很快过时

## 操作流程

### 创建记忆

```javascript
await memory_create({
  code: "mem-auth-jwt-decision",
  title: "认证方案选型：JWT vs Session",
  content: `## 背景
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

## 影响
- 需要实现 Refresh Token 机制
- 需要维护 Token 黑名单`,
  category: "技术决策",
  tags: ["auth", "jwt", "架构"]
});
```

## Code 命名规范

```
格式: mem-<项目>-<描述>
正则: ^[a-z][a-z\-]*[a-z]$

示例:
  mem-auth-jwt-decision     ✅  认证 JWT 决策
  mem-api-design-standard   ✅  API 设计规范
  mem-bug-fix-login         ✅  登录 Bug 修复经验
```

## 分类建议

| 分类 | 适用场景 |
|------|----------|
| 技术决策 | 框架选型、架构决策 |
| 设计规范 | API 规范、代码规范 |
| 问题解决 | Bug 修复、故障排查 |
| 最佳实践 | 开发经验、优化技巧 |
| 项目配置 | 环境配置、部署说明 |

## 内容格式建议

### 技术决策

```markdown
## 背景
为什么需要做这个决策

## 选项对比
| 方案 | 优点 | 缺点 |

## 决策
最终选择

## 理由
选择的原因

## 影响
对项目的影响
```

### Bug 修复

```markdown
## 问题描述
出现了什么问题

## 原因分析
为什么会出现

## 解决方案
如何解决的

## 预防措施
如何避免再次发生
```

### 设计规范

```markdown
## 适用范围
规范适用的场景

## 规范内容
具体的规范要求

## 示例
正确和错误的示例

## 例外情况
什么情况下可以例外
```

## 使用场景

### 场景 1: 技术选型后

```markdown
完成 JWT vs Session 选型讨论后：

1. 调用 record-decision skill
2. 记录选型过程和结论
3. 标注分类: 技术决策
4. 添加相关 tags
```

### 场景 2: Bug 修复后

```markdown
排查并修复复杂 Bug 后：

1. 调用 record-decision skill
2. 记录问题、原因、解决方案
3. 标注分类: 问题解决
4. 便于以后参考
```

### 场景 3: 制定规范

```markdown
讨论并确定 API 设计规范后：

1. 调用 record-decision skill
2. 记录完整规范内容
3. 标注分类: 设计规范
4. 团队可共享参考
```

## 输出示例

```
✅ 决策已记录

💡 [mem-auth-jwt-decision] 认证方案选型：JWT vs Session
   分类: 技术决策
   标签: auth, jwt, 架构

已保存到知识库，后续可通过 search-history 搜索~
```

## MCP 工具

- `memory_create` - 创建记忆
- `memory_update` - 更新记忆
- `memory_get` - 获取记忆详情

详见：
- [Code 格式规范](./references/code-format.md)
- [内容模板](./references/templates.md)
