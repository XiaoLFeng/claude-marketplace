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
todo-fix-validation-bug   ✅  修复验证 Bug
todo-refactor-utils       ✅  重构工具函数
todo-add-unit-tests       ✅  添加单元测试
```

## 错误示例

```
TODO-Auth-Login           ❌  包含大写
todo_auth_login           ❌  使用下划线
todo-auth-                ❌  以连字符结尾
-todo-auth                ❌  以连字符开头
todo-a                    ❌  太短
auth-login                ❌  不以 todo- 开头（建议）
```

## 命名建议

### 按功能命名

```
todo-<模块>-<功能>

示例:
  todo-auth-login         认证模块-登录
  todo-auth-register      认证模块-注册
  todo-user-profile       用户模块-个人资料
  todo-order-create       订单模块-创建
```

### 按操作命名

```
todo-<动作>-<目标>

示例:
  todo-fix-login-bug      修复-登录Bug
  todo-add-validation     添加-验证
  todo-refactor-api       重构-API
  todo-update-docs        更新-文档
```

### 按 Agent 分组

```
todo-<项目>-<任务>

在 description 中标注 Agent:
  [Task-A] todo-auth-login
  [Task-B] todo-auth-register
```

## 与 Plan Code 的关系

```
Plan: plan-user-auth
  └── todo-auth-login
  └── todo-auth-register
  └── todo-auth-jwt
  └── todo-auth-test

命名关联:
  plan-<项目>
    └── todo-<项目>-<任务>
```
