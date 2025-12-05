# Todo CLI 使用示例

## 示例 1：紧急 Bug 修复

### 场景
用户报告无法登录，需要紧急修复。

### 步骤 1：任务评估

```
复杂度：低（单一问题排查）
时间跨度：短期（预计 2 小时）
优先级：4🔴 紧急（影响核心功能）
```

### 步骤 2：创建待办

```bash
./llm-memory todo create \
  --code "todo-fix-login-bug" \
  --title "修复登录失败 Bug" \
  --description "排查并修复用户无法登录的问题，涉及认证逻辑" \
  --priority 4
```

**输出：**
```
✅ 待办创建成功！标识码: todo-fix-login-bug, 标题: 修复登录失败 Bug
```

### 步骤 3：任务执行

```bash
# 开始任务
./llm-memory todo start --code "todo-fix-login-bug"
# 输出：✅ 待办 todo-fix-login-bug 已开始

# ... 排查和修复代码 ...

# 完成任务
./llm-memory todo complete --code "todo-fix-login-bug"
# 输出：✅ 待办 todo-fix-login-bug 已完成

# 查看任务列表（确认完成）
./llm-memory todo list
```

### 关键学习点

- Bug 修复通常是 Priority 4（紧急）
- 简单任务不需要 Plan，直接创建 Todo
- 快速创建、快速完成

---

## 示例 2：用户认证系统重构（完整流程）

### 场景
需要重构现有的用户认证系统，采用 JWT 机制，支持 refresh token。

### 工作流设计

**项目特点：**
- 复杂度：高（涉及数据库、API、前端集成）
- 时间跨度：长期（预计 1 周）
- 依赖关系：有（数据库设计 → API 实现 → 前端集成）
- 知识积累：需要（设计决策、安全最佳实践）

### 步骤 1：创建相关待办

使用批量创建方式（推荐）：

```bash
./llm-memory todo batch-create --json '[
  {
    "code": "todo-design-auth-schema",
    "title": "设计认证数据库架构",
    "description": "设计 users、sessions、tokens 等表结构，支持 JWT 和 refresh token",
    "priority": 3
  },
  {
    "code": "todo-implement-jwt",
    "title": "实现 JWT 令牌机制",
    "description": "实现 JWT 生成、验证、刷新逻辑",
    "priority": 4
  },
  {
    "code": "todo-auth-api-endpoints",
    "title": "开发登录和注册 API",
    "description": "POST /login, POST /register, POST /refresh 等端点",
    "priority": 3
  },
  {
    "code": "todo-auth-middleware",
    "title": "添加认证中间件",
    "description": "实现 JWT 验证中间件，保护受限路由",
    "priority": 2
  },
  {
    "code": "todo-auth-unit-tests",
    "title": "编写认证单元测试",
    "description": "测试覆盖率达到 80% 以上",
    "priority": 2
  }
]'
```

**输出：**
```
✅ 批量创建成功! 共处理 5 个待办事项

Created:
- todo-design-auth-schema
- todo-implement-jwt
- todo-auth-api-endpoints
- todo-auth-middleware
- todo-auth-unit-tests
```

### 步骤 2：开始关键任务

```bash
# 开始数据库设计（基础）
./llm-memory todo start --code "todo-design-auth-schema"

# ... 完成数据库设计 ...

./llm-memory todo complete --code "todo-design-auth-schema"
```

### 步骤 3：开始 JWT 实现

```bash
# JWT 实现是关键任务，阻塞其他开发
./llm-memory todo start --code "todo-implement-jwt"

# ... 实现 JWT 逻辑 ...

./llm-memory todo complete --code "todo-implement-jwt"
```

### 步骤 4：并行开发

```bash
# 数据库和 JWT 完成后，可以并行开发 API 端点和中间件
./llm-memory todo batch-start --codes "todo-auth-api-endpoints,todo-auth-middleware"

# ... 开发中 ...

# 完成 API 端点
./llm-memory todo complete --code "todo-auth-api-endpoints"

# 完成中间件
./llm-memory todo complete --code "todo-auth-middleware"
```

### 步骤 5：最后的测试

```bash
# 开始测试任务
./llm-memory todo start --code "todo-auth-unit-tests"

# ... 编写测试 ...

# 完成测试
./llm-memory todo complete --code "todo-auth-unit-tests"
```

### 步骤 6：项目完成

```bash
# 查看所有已完成的任务
./llm-memory todo list --filter completed

# 可选：删除已完成的任务
./llm-memory todo batch-delete --codes "todo-design-auth-schema,todo-implement-jwt,todo-auth-api-endpoints,todo-auth-middleware,todo-auth-unit-tests" --force
```

### 关键学习点

- 使用批量创建提高效率
- 根据依赖关系调整优先级
- 使用 batch-start 并行处理无依赖任务
- 长期项目建议配合 Plan 使用

---

## 示例 3：优先级判断实例（20+ 真实任务）

### 场景
中等规模项目，需要分类处理多种类型的任务。

### 优先级分类示例

#### Priority 4 🔴 紧急 - 24 小时内必须解决

```bash
# 1. 线上 Bug - 影响用户使用
./llm-memory todo create --code "todo-fix-payment-fail" \
  --title "修复支付失败 Bug" --priority 4

# 2. 安全漏洞 - 需要立即修复
./llm-memory todo create --code "todo-fix-sql-injection" \
  --title "修复 SQL 注入漏洞" --priority 4

# 3. 阻塞性 Bug - 影响开发进度
./llm-memory todo create --code "todo-fix-build-error" \
  --title "修复构建失败" --priority 4

# 4. 数据丢失风险 - 需要紧急处理
./llm-memory todo create --code "todo-fix-data-corruption" \
  --title "修复数据库数据损坏" --priority 4
```

#### Priority 3 🟠 高 - 3 天内完成

```bash
# 1. 重要功能实现
./llm-memory todo create --code "todo-impl-payment-gateway" \
  --title "集成支付网关" --priority 3

# 2. 影响用户体验的功能
./llm-memory todo create --code "todo-improve-search-perf" \
  --title "优化搜索性能" --priority 3

# 3. 核心流程优化
./llm-memory todo create --code "todo-optimize-checkout" \
  --title "优化结账流程" --priority 3

# 4. 计划中的发布功能
./llm-memory todo create --code "todo-impl-export-pdf" \
  --title "实现 PDF 导出功能" --priority 3
```

#### Priority 2 🟡 中 - 1 周内完成

```bash
# 1. 常规开发任务
./llm-memory todo create --code "todo-refactor-auth-module" \
  --title "重构认证模块" --priority 2

# 2. 代码质量改进
./llm-memory todo create --code "todo-add-unit-tests" \
  --title "补充单元测试" --priority 2

# 3. 文档完善
./llm-memory todo create --code "todo-update-api-docs" \
  --title "更新 API 文档" --priority 2

# 4. 技术债务（可管理的）
./llm-memory todo create --code "todo-cleanup-temp-code" \
  --title "清理临时代码" --priority 2

# 5. 中等规模重构
./llm-memory todo create --code "todo-migrate-old-config" \
  --title "迁移旧配置系统" --priority 2
```

#### Priority 1 🟢 低 - 可选/长期

```bash
# 1. 性能微调（非关键）
./llm-memory todo create --code "todo-optimize-cache" \
  --title "优化缓存策略" --priority 1

# 2. 代码美化
./llm-memory todo create --code "todo-improve-code-style" \
  --title "改进代码风格一致性" --priority 1

# 3. 可选功能
./llm-memory todo create --code "todo-add-dark-mode" \
  --title "添加深色主题" --priority 1

# 4. 长期优化
./llm-memory todo create --code "todo-refactor-legacy-code" \
  --title "重构遗留代码" --priority 1

# 5. 文档完善（非必须）
./llm-memory todo create --code "todo-write-deployment-guide" \
  --title "编写部署指南" --priority 1

# 6. 技术试验
./llm-memory todo create --code "todo-poc-ai-search" \
  --title "POC：AI 搜索功能" --priority 1
```

### 完整优先级判断表

| 优先级 | 特征 | 示例 | 处理时间 |
|--------|------|------|---------|
| 4 紧急 | 影响生产/安全 | 线上 Bug、安全漏洞 | 24h 内 |
| 3 高   | 重要功能/影响体验 | 核心功能、性能优化 | 3 天内 |
| 2 中   | 常规任务 | 开发、测试、文档 | 1 周内 |
| 1 低   | 可选改进 | 代码美化、技术试验 | 长期 |

---

## 示例 4：批量创建 - 项目启动清单

### 场景
新项目启动，需要一次性创建 10 个初期任务。

### 创建方式 1：使用 JSON 字符串

```bash
./llm-memory todo batch-create --json '[
  {"code":"todo-setup-project-structure","title":"创建项目结构","priority":4},
  {"code":"todo-setup-ci-cd","title":"配置 CI/CD 流程","priority":4},
  {"code":"todo-setup-database","title":"设置数据库","priority":4},
  {"code":"todo-setup-auth","title":"实现基础认证","priority":3},
  {"code":"todo-create-api-docs","title":"创建 API 文档框架","priority":3},
  {"code":"todo-setup-logging","title":"配置日志系统","priority":2},
  {"code":"todo-setup-monitoring","title":"设置监控告警","priority":2},
  {"code":"todo-create-readme","title":"编写 README","priority":2},
  {"code":"todo-setup-docker","title":"配置 Docker","priority":1},
  {"code":"todo-setup-backup","title":"配置备份策略","priority":1}
]'
```

### 创建方式 2：使用 JSON 文件（推荐）

**project-startup.json:**
```json
[
  {
    "code": "todo-setup-project-structure",
    "title": "创建项目结构",
    "description": "创建标准的项目目录结构",
    "priority": 4
  },
  {
    "code": "todo-setup-ci-cd",
    "title": "配置 CI/CD 流程",
    "description": "集成 GitHub Actions 或 GitLab CI",
    "priority": 4
  },
  {
    "code": "todo-setup-database",
    "title": "设置数据库",
    "description": "创建数据库，运行迁移脚本",
    "priority": 4
  },
  {
    "code": "todo-setup-auth",
    "title": "实现基础认证",
    "description": "用户注册、登录、JWT 认证",
    "priority": 3
  },
  {
    "code": "todo-create-api-docs",
    "title": "创建 API 文档框架",
    "description": "使用 Swagger/OpenAPI",
    "priority": 3
  },
  {
    "code": "todo-setup-logging",
    "title": "配置日志系统",
    "description": "集成日志库，设置日志级别",
    "priority": 2
  },
  {
    "code": "todo-setup-monitoring",
    "title": "设置监控告警",
    "description": "集成 Prometheus/ELK",
    "priority": 2
  },
  {
    "code": "todo-create-readme",
    "title": "编写 README",
    "description": "项目说明、安装指南、使用示例",
    "priority": 2
  },
  {
    "code": "todo-setup-docker",
    "title": "配置 Docker",
    "description": "编写 Dockerfile 和 docker-compose",
    "priority": 1
  },
  {
    "code": "todo-setup-backup",
    "title": "配置备份策略",
    "description": "数据库和配置文件备份",
    "priority": 1
  }
]
```

**执行命令：**
```bash
./llm-memory todo batch-create --json-file ./project-startup.json
```

**输出：**
```
✅ 批量创建成功! 共处理 10 个待办事项

Created:
- todo-setup-project-structure
- todo-setup-ci-cd
- todo-setup-database
- todo-setup-auth
- todo-create-api-docs
- todo-setup-logging
- todo-setup-monitoring
- todo-create-readme
- todo-setup-docker
- todo-setup-backup
```

### 后续管理

```bash
# 开始第一阶段（4 个关键任务）
./llm-memory todo batch-start --codes "todo-setup-project-structure,todo-setup-ci-cd,todo-setup-database,todo-setup-auth"

# 查看进度
./llm-memory todo list --filter in-progress

# 完成第一阶段
./llm-memory todo batch-complete --codes "todo-setup-project-structure,todo-setup-ci-cd,todo-setup-database,todo-setup-auth"

# 开始第二阶段
./llm-memory todo batch-start --codes "todo-create-api-docs,todo-setup-logging,todo-setup-monitoring,todo-create-readme"
```

---

## 示例 5：Todo 状态跟踪完整流程

### 场景
跟踪一周内的开发任务从创建到完成的整个生命周期。

### 第 1 天：任务规划和创建

```bash
# 周一规划本周任务
./llm-memory todo batch-create --json '[
  {"code":"todo-week-feature-1","title":"开发功能 A","priority":3},
  {"code":"todo-week-feature-2","title":"开发功能 B","priority":3},
  {"code":"todo-week-bugfix-1","title":"修复 Bug X","priority":4},
  {"code":"todo-week-bugfix-2","title":"修复 Bug Y","priority":2},
  {"code":"todo-week-testing","title":"测试和 QA","priority":2},
  {"code":"todo-week-docs","title":"编写文档","priority":1}
]'

# 查看所有任务
./llm-memory todo list
```

**输出：**
```
Code                     | Title          | Status    | Priority
todo-week-feature-1      | 开发功能 A      | pending   | 3
todo-week-feature-2      | 开发功能 B      | pending   | 3
todo-week-bugfix-1       | 修复 Bug X      | pending   | 4
todo-week-bugfix-2       | 修复 Bug Y      | pending   | 2
todo-week-testing        | 测试和 QA       | pending   | 2
todo-week-docs           | 编写文档        | pending   | 1
```

### 第 2-3 天：紧急任务优先

```bash
# 周二开始紧急 Bug 修复
./llm-memory todo start --code "todo-week-bugfix-1"

# ... 修复 Bug X ...

./llm-memory todo complete --code "todo-week-bugfix-1"

# 开始开发功能 A
./llm-memory todo start --code "todo-week-feature-1"

# 查看进行中的任务
./llm-memory todo list --filter in-progress
```

### 第 4-5 天：并行开发

```bash
# 完成功能 A
./llm-memory todo complete --code "todo-week-feature-1"

# 并行开始功能 B 和其他任务
./llm-memory todo batch-start --codes "todo-week-feature-2,todo-week-bugfix-2,todo-week-testing"

# 查看所有待处理任务
./llm-memory todo list --filter pending
```

### 第 6-7 天：冲刺完成

```bash
# 更新优先级（发现某些任务更紧急）
./llm-memory todo batch-update --json '[
  {"code":"todo-week-docs","priority":3}
]'

# 完成全部任务
./llm-memory todo batch-complete --codes "todo-week-feature-2,todo-week-bugfix-2,todo-week-testing,todo-week-docs"

# 验证全部完成
./llm-memory todo list --filter completed
```

### 周末：清理

```bash
# 删除已完成的任务
./llm-memory todo batch-delete --codes "todo-week-feature-1,todo-week-feature-2,todo-week-bugfix-1,todo-week-bugfix-2,todo-week-testing,todo-week-docs" --force
```

---

## 示例 6：动态调整工作流

### 场景
任务执行中发现需要调整优先级或添加新任务。

### 初始计划

```bash
./llm-memory todo batch-create --json '[
  {"code":"todo-impl-search","title":"实现搜索功能","priority":2},
  {"code":"todo-impl-filter","title":"实现过滤功能","priority":2},
  {"code":"todo-impl-export","title":"实现导出功能","priority":1}
]'
```

### 中途发现搜索功能很重要

```bash
# 调整优先级：搜索从中调到高
./llm-memory todo batch-update --json '[
  {"code":"todo-impl-search","priority":3}
]'

# 开始搜索功能
./llm-memory todo start --code "todo-impl-search"
```

### 客户临时要求添加新功能

```bash
# 创建新的高优先级任务
./llm-memory todo create \
  --code "todo-urgent-customer-request" \
  --title "实现客户要求的功能" \
  --priority 4

# 启动新任务
./llm-memory todo start --code "todo-urgent-customer-request"
```

### 完成并汇总

```bash
# 查看所有进行中的任务
./llm-memory todo list --filter in-progress

# 逐个完成
./llm-memory todo batch-complete --codes "todo-impl-search,todo-urgent-customer-request"

# 查看最终状态
./llm-memory todo list
```

---

## 最佳实践总结

### ✅ 做这些

1. **使用一致的命名约定**
   ```bash
   todo-<项目/模块>-<任务描述>
   ```

2. **批量操作处理多个任务**
   ```bash
   ./llm-memory todo batch-create --json-file ./tasks.json
   ```

3. **定期更新优先级**
   ```bash
   ./llm-memory todo batch-update --json '[...]'
   ```

4. **及时完成和清理**
   ```bash
   ./llm-memory todo batch-delete --codes "..." --force
   ```

### ❌ 避免这些

1. ❌ 混合无关的任务一起批处理
2. ❌ 使用无意义的优先级判断
3. ❌ 忘记删除已完成的任务
4. ❌ 过度细粒度的任务拆分

---

参考：[命令参考](./commands.md) | [批量操作](./batch-operations.md)
