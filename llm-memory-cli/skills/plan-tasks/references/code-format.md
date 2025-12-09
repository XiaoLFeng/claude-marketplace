# Todo Code 格式规范

## 格式要求

```
格式: todo-<项目>-<任务描述>
正则: ^[a-z][a-z\-]*[a-z]$

规则:
- 全小写字母
- 使用连字符 (-) 分隔
- 以字母开头和结尾
- 最少 3 个字符
```

## 正确示例

```
todo-auth-login           ✅  登录功能
todo-auth-register        ✅  注册功能
todo-api-user-crud        ✅  用户 CRUD API
```

## 错误示例

```
TODO-Auth-Login           ❌  包含大写
todo_auth_login           ❌  使用下划线
todo-auth-                ❌  以连字符结尾
```

## 与 Plan 的关联

```
Plan: plan-user-auth
  └── todo-auth-login
  └── todo-auth-register
  └── todo-auth-jwt

通过 <项目> 部分关联:
  plan-<项目>
    └── todo-<项目>-<任务>
```
