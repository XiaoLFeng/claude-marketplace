# Memory Code 格式规范

## 格式要求

```
格式: mem-<项目/主题>-<描述>
正则: ^[a-z][a-z\-]*[a-z]$

规则:
- 全小写字母
- 使用连字符 (-) 分隔
- 以字母开头和结尾
- 最少 3 个字符
```

## 正确示例

```
mem-auth-jwt-decision       ✅
mem-api-design-standard     ✅
mem-bug-fix-login-timeout   ✅
```

## 错误示例

```
MEM-Auth-Decision           ❌  包含大写
mem_auth_decision           ❌  使用下划线
mem-auth-                   ❌  以连字符结尾
```

## 命名建议

### 按类型命名

```
技术决策: mem-<项目>-<主题>-decision
设计规范: mem-<项目>-<主题>-standard
Bug修复:  mem-bug-<模块>-<问题>
```

## 与其他 Code 的区别

| 类型 | 前缀 | 用途 |
|------|------|------|
| Memory | mem- | 长期知识、决策 |
| Plan | plan- | 项目计划 |
| Todo | todo- | 短期任务 |
