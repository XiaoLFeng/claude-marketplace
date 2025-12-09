# Memory Code 格式规则

## 基本规则

| 规则 | 说明 |
|------|------|
| 字符 | 全小写字母 (a-z) + 连字符 (-) |
| 开头 | 必须是字母 |
| 结尾 | 必须是字母 |
| 长度 | ≥3 个字符 |
| 正则 | `^[a-z][a-z-]*[a-z]$` |

## 推荐格式

```
mem-<主题>
mem-<主题>-<子主题>
```

## 示例

### ✅ 正确示例

```
mem-jwt-decision        # 技术决策
mem-login-bug-fix       # Bug 解决
mem-api-design-v2       # 设计文档
mem-docker-config       # 配置模板
mem-code-style-guide    # 项目规范
```

### ❌ 错误示例

```
Memory_001              # 含大写和下划线
mem-a                   # 太短
mem-jwt-                # 连字符结尾
-mem-jwt                # 连字符开头
mem_jwt_decision        # 含下划线
```

## 唯一性

Memory 的 code 是**全局唯一**的，整个系统不能重复。

创建前可以先搜索确认：

```javascript
memory_search({ keyword: "jwt" })
```

## 命名建议

1. **语义化**：使用描述性名称，不用编号
2. **简洁**：3-5 个词较好
3. **分层**：用连字符分隔层级
4. **一致**：同类记忆使用相似命名模式
