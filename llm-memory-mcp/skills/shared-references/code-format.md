# Code 格式规则详解

## 格式要求

### 基本规则

- **全小写字母**（a-z）
- **可包含连字符**（-）
- **开头和末尾必须是字母**
- **最少 3 个字符**

### 正则表达式

```regex
^[a-z][a-z-]*[a-z]$
```

或者对于 3 个字符的情况：

```regex
^[a-z]{3,}(-[a-z]+)*$
```

---

## 推荐命名模式

### Memory 命名

**格式：** `mem-<主题>`

**示例（30个）：**
- `mem-api-design` - API 设计文档
- `mem-jwt-decision` - JWT 技术选型
- `mem-deployment-notes` - 部署流程说明
- `mem-security-best-practices` - 安全最佳实践
- `mem-database-schema` - 数据库架构
- `mem-auth-flow` - 认证流程
- `mem-error-handling` - 错误处理规范
- `mem-code-style` - 代码风格约定
- `mem-git-workflow` - Git 工作流
- `mem-performance-tips` - 性能优化技巧
- `mem-testing-strategy` - 测试策略
- `mem-deployment-checklist` - 部署检查清单
- `mem-architecture-decisions` - 架构决策记录
- `mem-redis-config` - Redis 配置
- `mem-nginx-setup` - Nginx 配置
- `mem-docker-compose` - Docker Compose 配置
- `mem-ci-cd-pipeline` - CI/CD 流程
- `mem-monitoring-setup` - 监控配置
- `mem-logging-best-practices` - 日志最佳实践
- `mem-cache-strategy` - 缓存策略
- `mem-rate-limiting` - 限流策略
- `mem-data-migration` - 数据迁移方案
- `mem-backup-strategy` - 备份策略
- `mem-disaster-recovery` - 灾难恢复
- `mem-scaling-strategy` - 扩展策略
- `mem-microservices-architecture` - 微服务架构
- `mem-api-versioning` - API 版本控制
- `mem-documentation-standards` - 文档标准
- `mem-code-review-checklist` - 代码审查清单
- `mem-onboarding-guide` - 新人入职指南

### Plan 命名

**格式：** `plan-<简短描述>`

**示例（30个）：**
- `plan-user-system` - 用户系统开发
- `plan-api-redesign` - API 重新设计
- `plan-database-migration` - 数据库迁移
- `plan-auth-refactor` - 认证模块重构
- `plan-performance-optimization` - 性能优化计划
- `plan-security-enhancement` - 安全增强
- `plan-ui-redesign` - UI 重新设计
- `plan-mobile-app` - 移动应用开发
- `plan-microservices-migration` - 微服务迁移
- `plan-payment-integration` - 支付集成
- `plan-notification-system` - 通知系统
- `plan-search-feature` - 搜索功能
- `plan-analytics-dashboard` - 分析仪表板
- `plan-admin-panel` - 管理后台
- `plan-content-management` - 内容管理系统
- `plan-reporting-system` - 报表系统
- `plan-multi-tenant` - 多租户支持
- `plan-internationalization` - 国际化
- `plan-accessibility` - 无障碍支持
- `plan-seo-optimization` - SEO 优化
- `plan-social-login` - 社交登录
- `plan-email-system` - 邮件系统
- `plan-file-upload` - 文件上传功能
- `plan-real-time-chat` - 实时聊天
- `plan-video-processing` - 视频处理
- `plan-image-optimization` - 图片优化
- `plan-cdn-integration` - CDN 集成
- `plan-load-balancing` - 负载均衡
- `plan-monitoring-alerting` - 监控告警
- `plan-backup-automation` - 自动化备份

### Todo 命名

**格式：** `todo-<动作>-<对象>`

**示例（40个）：**
- `todo-fix-login-bug` - 修复登录 Bug
- `todo-add-api-docs` - 添加 API 文档
- `todo-update-readme` - 更新 README
- `todo-implement-jwt` - 实现 JWT
- `todo-refactor-auth-module` - 重构认证模块
- `todo-add-unit-tests` - 添加单元测试
- `todo-optimize-database-query` - 优化数据库查询
- `todo-fix-memory-leak` - 修复内存泄漏
- `todo-upgrade-dependencies` - 升级依赖包
- `todo-add-error-handling` - 添加错误处理
- `todo-implement-caching` - 实现缓存
- `todo-add-logging` - 添加日志
- `todo-fix-security-vulnerability` - 修复安全漏洞
- `todo-add-validation` - 添加验证
- `todo-implement-pagination` - 实现分页
- `todo-add-search-filter` - 添加搜索过滤
- `todo-fix-performance-issue` - 修复性能问题
- `todo-add-rate-limiting` - 添加限流
- `todo-implement-oauth` - 实现 OAuth
- `todo-add-email-notification` - 添加邮件通知
- `todo-fix-ui-bug` - 修复 UI Bug
- `todo-add-responsive-design` - 添加响应式设计
- `todo-implement-dark-mode` - 实现暗黑模式
- `todo-add-breadcrumbs` - 添加面包屑导航
- `todo-fix-accessibility-issues` - 修复无障碍问题
- `todo-add-i18n` - 添加国际化
- `todo-implement-file-upload` - 实现文件上传
- `todo-add-image-compression` - 添加图片压缩
- `todo-fix-cors-issue` - 修复 CORS 问题
- `todo-add-api-versioning` - 添加 API 版本控制
- `todo-implement-websocket` - 实现 WebSocket
- `todo-add-real-time-updates` - 添加实时更新
- `todo-fix-data-inconsistency` - 修复数据不一致
- `todo-add-database-indexes` - 添加数据库索引
- `todo-implement-soft-delete` - 实现软删除
- `todo-add-audit-log` - 添加审计日志
- `todo-fix-timezone-issue` - 修复时区问题
- `todo-add-data-export` - 添加数据导出
- `todo-implement-backup` - 实现备份功能
- `todo-add-health-check` - 添加健康检查

---

## 常见错误和修正

### 错误 1：使用下划线

❌ **错误：** `test_plan_001`
✅ **修正：** `test-plan-one`

**原因：** Code 格式只允许连字符（-），不允许下划线（_）

### 错误 2：包含大写字母

❌ **错误：** `Task-001`
✅ **修正：** `task-one`

**原因：** 必须全小写字母

### 错误 3：数字开头

❌ **错误：** `001-task`
✅ **修正：** `task-one`

**原因：** 开头必须是字母

### 错误 4：连字符开头

❌ **错误：** `-my-task`
✅ **修正：** `my-task`

**原因：** 开头必须是字母

### 错误 5：连字符结尾

❌ **错误：** `task-`
✅ **修正：** `task`

**原因：** 末尾必须是字母

### 错误 6：少于 3 个字符

❌ **错误：** `ab`
✅ **修正：** `abc` 或 `my-task`

**原因：** 最少 3 个字符

### 错误 7：驼峰命名

❌ **错误：** `myTask`
✅ **修正：** `my-task`

**原因：** 必须全小写，使用连字符分隔

### 错误 8：纯数字

❌ **错误：** `123`
✅ **修正：** `task-one-two-three` 或 `item-123`

**原因：** 必须包含字母

### 错误 9：特殊字符

❌ **错误：** `task@001` 或 `task#one`
✅ **修正：** `task-one`

**原因：** 只允许小写字母和连字符

### 错误 10：空格

❌ **错误：** `my task`
✅ **修正：** `my-task`

**原因：** 不允许空格，使用连字符

---

## 唯一性规则

### Memory

- **全局唯一**（整个系统中不能重复）
- 即使删除后也不建议重用相同 code
- 跨所有作用域（personal/group/global）唯一

### Plan / Todo

- **活跃状态唯一**（pending 和 in_progress）
- 已完成（completed）或已取消（cancelled）的 code 可以重用
- 在同一作用域内唯一

### 唯一性检查机制

**检查是否冲突：**

```bash
# CLI
./main memory list | grep "your-code"
./main plan list | grep "your-code"
./main todo list | grep "your-code"

# MCP
memory_list({ scope: "all" })
plan_list({ scope: "all" })
todo_list({ scope: "all" })
```

---

## 最佳实践

### 1. 使用语义化名称

✅ **推荐：** `plan-api-redesign`（清晰表达意图）
❌ **避免：** `plan-project-001`（无意义编号）

**理由：** 语义化名称便于理解和搜索

### 2. 简洁但清晰

✅ **推荐：** `todo-fix-login`（简洁）
❌ **避免：** `todo-fix-the-login-page-bug-that-causes-error`（过长）

**理由：** 过长的 code 难以记忆和使用

### 3. 包含动作词（Todo）

✅ **推荐：**
- `todo-add-tests`
- `todo-update-docs`
- `todo-fix-bug`

❌ **避免：** `todo-tests`（不明确动作）

**理由：** 动作词明确表达任务的操作类型

### 4. 体现层级关系

✅ **推荐：**
- `todo-auth-jwt-impl`
- `todo-auth-middleware`
- `todo-auth-validation`

（都属于 auth 模块）

**理由：** 便于按模块组织和筛选任务

### 5. 避免数字编号

❌ **避免：** `task001`, `todo-123`
✅ **推荐：** `todo-first-task`, `todo-second-task`

**理由：** 数字编号无语义，难以理解

### 6. 项目前缀（可选）

✅ **推荐：**
- `todo-myapp-fix-login`
- `mem-myapp-architecture`

**适用场景：** 多项目管理时

**理由：** 避免跨项目的 code 冲突

---

## 命名冲突避免

### Plan 和 Todo 命名区分

**策略：** 使用不同的前缀和粒度

**示例：**
- **Plan:** `plan-auth-refactor`（计划）
  - **Todo 1:** `todo-auth-design-schema`（任务1）
  - **Todo 2:** `todo-auth-impl-jwt`（任务2）
  - **Todo 3:** `todo-auth-add-tests`（任务3）

### Memory 和 Plan/Todo 命名区分

**策略：** Memory 使用 `mem-` 前缀

**示例：**
- **Memory:** `mem-auth-decision`（记录技术选型）
- **Plan:** `plan-auth-refactor`（实施计划）
- **Todo:** `todo-auth-impl-jwt`（具体任务）

---

## 调试技巧

### 检查 code 格式

**方法 1：使用 list 命令**

```bash
# CLI
./main memory list
./main plan list
./main todo list

# MCP
memory_list({ scope: "all" })
plan_list({ scope: "all" })
todo_list({ scope: "all" })
```

**方法 2：使用在线正则测试工具**

1. 访问 https://regex101.com/
2. 输入正则：`^[a-z][a-z-]*[a-z]$`
3. 测试你的 code 是否匹配

**方法 3：本地验证**

```bash
# 使用 grep
echo "your-code" | grep -E '^[a-z][a-z-]*[a-z]$'
# 匹配成功则输出 your-code，失败则无输出
```

### 处理冲突

**步骤 1：查询已存在的 code**

```bash
./main [memory/plan/todo] list
```

**步骤 2：使用更具体的命名**

- ❌ `todo-fix-bug` → ✅ `todo-fix-login-bug`
- ❌ `mem-notes` → ✅ `mem-api-design-notes`

**步骤 3：或者先完成/删除旧记录**

```bash
./main todo complete --code "old-code"
./main todo delete --code "old-code"
```

---

## 快速参考卡片

### ✅ 格式规则速记

```
全小写 + 连字符
字母开头和结尾
>= 3 字符
^[a-z][a-z-]*[a-z]$
```

### ✅ 命名模式速记

```
Memory:  mem-<主题>
Plan:    plan-<描述>
Todo:    todo-<动作>-<对象>
```

### ✅ 唯一性速记

```
Memory:     全局唯一
Plan/Todo:  活跃状态唯一
```

---

## 参考资料

- [优先级判断指南](./priority-guide.md)
- [故障排除](./troubleshooting.md)
- [最佳实践](./best-practices.md)
