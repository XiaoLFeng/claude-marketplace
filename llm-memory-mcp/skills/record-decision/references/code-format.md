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
mem-auth-jwt-decision       ✅  认证 JWT 决策
mem-api-design-standard     ✅  API 设计规范
mem-bug-fix-login-timeout   ✅  登录超时 Bug 修复
mem-deploy-k8s-config       ✅  K8s 部署配置
mem-perf-optimize-query     ✅  查询性能优化
```

## 错误示例

```
MEM-Auth-Decision           ❌  包含大写
mem_auth_decision           ❌  使用下划线
mem-auth-                   ❌  以连字符结尾
-mem-auth                   ❌  以连字符开头
mem-a                       ❌  太短
```

## 命名建议

### 按类型命名

```
技术决策: mem-<项目>-<主题>-decision
设计规范: mem-<项目>-<主题>-standard
Bug修复:  mem-bug-<模块>-<问题>
配置记录: mem-config-<环境>-<内容>
```

### 示例

```
mem-auth-token-decision     技术决策-认证令牌
mem-api-response-standard   设计规范-API响应
mem-bug-login-timeout       Bug修复-登录超时
mem-config-prod-database    配置记录-生产数据库
```

## 与其他 Code 的区别

| 类型 | 前缀 | 用途 |
|------|------|------|
| Memory | mem- | 长期知识、决策 |
| Plan | plan- | 项目计划 |
| Todo | todo- | 短期任务 |
