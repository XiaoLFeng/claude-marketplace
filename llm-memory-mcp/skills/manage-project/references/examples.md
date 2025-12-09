# Manage Project 使用示例

## 示例 1: 创建功能开发计划

**场景**：开发用户认证系统

```javascript
plan_create({
  code: "plan-user-auth",
  title: "用户认证系统开发",
  description: "实现完整的用户认证功能，包括登录、注册、JWT 令牌",
  content: `## 目标
实现安全、可扩展的用户认证系统

## 技术选型
- JWT + Refresh Token
- Redis 存储令牌黑名单
- bcrypt 密码加密

## 实施步骤
1. [ ] 设计数据库模型
2. [ ] 实现用户注册
3. [ ] 实现用户登录
4. [ ] JWT 令牌生成和验证
5. [ ] Refresh Token 机制
6. [ ] 登出和令牌失效
7. [ ] 单元测试
8. [ ] 集成测试

## 验收标准
- 通过所有测试用例
- 安全审计通过
- 性能指标：登录响应 < 200ms

## 预计时间
2 周`
})
```

**输出**：

```
✅ 计划已创建

📋 [plan-user-auth] 用户认证系统开发
   进度: 0% | 状态: 待开始

开始工作后记得更新进度~
```

## 示例 2: 更新进度

**场景**：完成了部分工作，更新进度

```javascript
// 完成了设计和注册功能 (2/8 = 25%)
plan_update({
  code: "plan-user-auth",
  progress: 25
})
```

**输出**：

```
✅ 进度已更新

📋 [plan-user-auth] 用户认证系统开发
   进度: 25% | 状态: 进行中
   ████░░░░░░░░░░░░░░░░
```

## 示例 3: 查看计划详情

**场景**：查看计划的完整内容

```javascript
const plan = await plan_get({ code: "plan-user-auth" });
```

**输出**：

```
📋 计划详情

[plan-user-auth] 用户认证系统开发
进度: 45% | 状态: 进行中

## 目标
实现安全、可扩展的用户认证系统

## 实施步骤
1. [x] 设计数据库模型
2. [x] 实现用户注册
3. [x] 实现用户登录
4. [ ] JWT 令牌生成和验证
5. [ ] Refresh Token 机制
...

创建于: 2024-01-01
更新于: 2024-01-10
```

## 示例 4: 完成计划

**场景**：所有工作完成

```javascript
plan_update({
  code: "plan-user-auth",
  progress: 100
})
```

**输出**：

```
🎉 计划已完成！

📋 [plan-user-auth] 用户认证系统开发
   进度: 100% | 状态: 已完成
   ████████████████████

恭喜完成！要记录什么经验吗？
```

## 示例 5: 更新计划内容

**场景**：需求变更，更新计划内容

```javascript
plan_update({
  code: "plan-user-auth",
  content: `## 目标
实现安全、可扩展的用户认证系统

## 技术选型（已更新）
- JWT + Refresh Token
- Redis 存储令牌黑名单
- bcrypt 密码加密
- 新增：OAuth 2.0 集成  ← 新增

## 实施步骤
...（更新后的步骤）...`
})
```

## 示例 6: 列出所有计划

**场景**：查看当前所有进行中的计划

```javascript
const plans = await plan_list({ scope: "all" });
```

**输出**：

```
📋 计划列表

进行中 (2):
  • [plan-user-auth] 用户认证系统开发 (45%)
  • [plan-api-v2] API 2.0 重构 (20%)

待开始 (1):
  • [plan-mobile-app] 移动端适配 (0%)

已完成 (3):
  • [plan-db-migration] 数据库迁移 (100%)
  ...
```
