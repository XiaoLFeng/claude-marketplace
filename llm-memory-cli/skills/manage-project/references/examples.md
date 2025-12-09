# Manage Project 使用示例

真实场景下的项目计划管理示例。

## 示例 1: 新功能开发项目

### 场景
开发一个新的用户认证系统，包括设计、实现、测试等多个阶段。

### 操作流程

#### 1. 创建项目计划

```bash
llm-memory plan create \
  --code "plan-user-auth-v2" \
  --title "用户认证系统 v2.0" \
  --description "重构现有认证系统，添加 OAuth 2.0、双因素认证支持" \
  --content "## 项目目标
完成用户认证系统的全面升级

## 背景
- 当前系统仅支持用户名密码
- 需要支持第三方登录
- 安全性需要增强

## 实施步骤
1. 需求分析和技术调研
2. 系统设计和架构规划
3. 核心功能实现
4. 测试和安全审计
5. 文档编写和团队培训
6. 灰度发布和监控

## 关键里程碑
- 第 1 周：完成设计
- 第 2-3 周：核心功能实现
- 第 4 周：测试和上线

## 风险评估
- 第三方 OAuth 服务稳定性
- 用户迁移过程中的数据安全
- 性能影响

## 资源需求
- 2 名开发人员
- 1 名安全顾问
- 测试环境支持"
```

#### 2. 第一周：需求分析阶段

```bash
# 创建相关任务
llm-memory todo create \
  --code "todo-research-oauth" \
  --title "调研 OAuth 2.0 实现方案" \
  --description "对比 Google、GitHub、Auth0 等方案"

llm-memory todo create \
  --code "todo-security-review" \
  --title "安全需求评审" \
  --description "与安全团队讨论双因素认证实现"

# 记录关键决策
llm-memory memory create \
  --code "mem-auth-provider-choice" \
  --title "OAuth 提供商选择" \
  --content "## 决策
选择 Auth0 作为第三方认证提供商

## 理由
1. 文档完善，社区支持好
2. 支持多种认证方式
3. 符合安全合规要求
4. 定价合理

## 备选方案
- 自建 OAuth 服务（开发成本高）
- AWS Cognito（与现有架构集成复杂）"

# 第一周结束，更新进度
llm-memory plan update \
  --code "plan-user-auth-v2" \
  --progress 15 \
  --content "## 项目目标
完成用户认证系统的全面升级

## 背景
- 当前系统仅支持用户名密码
- 需要支持第三方登录
- 安全性需要增强

## 实施步骤
1. 需求分析和技术调研 ✅
   - 完成 OAuth 2.0 方案调研
   - 完成安全需求评审
   - 选定 Auth0 作为认证提供商
2. 系统设计和架构规划 (进行中)
3. 核心功能实现
4. 测试和安全审计
5. 文档编写和团队培训
6. 灰度发布和监控

## 关键决策
- 参考 mem-auth-provider-choice

## 下一步
- 完成系统架构设计
- 准备开发环境"
```

#### 3. 第二周：设计和实现

```bash
# 更新进度
llm-memory plan update \
  --code "plan-user-auth-v2" \
  --progress 40 \
  --content "## 项目目标
完成用户认证系统的全面升级

## 实施步骤
1. 需求分析和技术调研 ✅
2. 系统设计和架构规划 ✅
   - 完成数据库设计
   - 完成 API 设计
   - 完成前端交互设计
3. 核心功能实现 (进行中)
   - OAuth 登录 ✅
   - 用户注册/登录 (进行中)
   - 双因素认证 (待开始)
4. 测试和安全审计
5. 文档编写和团队培训
6. 灰度发布和监控

## 技术栈
- 后端：Node.js + Express + Auth0 SDK
- 前端：React + Auth0 React SDK
- 数据库：PostgreSQL

## 当前问题
- Session 管理需要优化
- 需要实现 token 刷新机制"
```

#### 4. 第三周：实现完成

```bash
# 更新进度
llm-memory plan update \
  --code "plan-user-auth-v2" \
  --progress 70

# 记录实现中的重要发现
llm-memory memory create \
  --code "mem-token-refresh-solution" \
  --title "Token 刷新机制实现" \
  --content "## 问题
Access Token 过期后用户体验差

## 解决方案
使用 Refresh Token 实现无感刷新

## 实现细节
\`\`\`javascript
// 拦截器自动刷新
axios.interceptors.response.use(
  response => response,
  async error => {
    if (error.response.status === 401) {
      const newToken = await refreshToken()
      error.config.headers.Authorization = \`Bearer \${newToken}\`
      return axios.request(error.config)
    }
    return Promise.reject(error)
  }
)
\`\`\`

## 参考资料
- Auth0 文档：https://auth0.com/docs/tokens/refresh-tokens"
```

#### 5. 第四周：测试和上线

```bash
# 测试阶段
llm-memory plan update \
  --code "plan-user-auth-v2" \
  --progress 85 \
  --content "## 实施步骤
1. 需求分析和技术调研 ✅
2. 系统设计和架构规划 ✅
3. 核心功能实现 ✅
4. 测试和安全审计 (进行中)
   - 单元测试 ✅
   - 集成测试 ✅
   - 安全审计 (进行中)
   - 性能测试 (待开始)
5. 文档编写和团队培训
6. 灰度发布和监控

## 测试结果
- 单元测试覆盖率：92%
- 集成测试：通过
- 发现 2 个安全问题（已修复）

## 待办
- 完成性能测试
- 编写用户文档
- 准备培训材料"

# 项目完成
llm-memory plan update \
  --code "plan-user-auth-v2" \
  --progress 100 \
  --content "## 项目总结
用户认证系统 v2.0 已成功上线

## 完成内容
1. 需求分析和技术调研 ✅
2. 系统设计和架构规划 ✅
3. 核心功能实现 ✅
4. 测试和安全审计 ✅
5. 文档编写和团队培训 ✅
6. 灰度发布和监控 ✅

## 成果
- 支持 Google、GitHub OAuth 登录
- 实现双因素认证
- 通过安全审计
- 性能提升 30%

## 经验教训
- Token 刷新机制需要提前规划（参考 mem-token-refresh-solution）
- 安全测试应该更早介入
- 灰度发布策略很重要

## 相关资源
- 代码仓库：https://github.com/company/auth-v2
- 文档：https://docs.company.com/auth-v2
- 决策记录：mem-auth-provider-choice, mem-token-refresh-solution

## 后续计划
- 监控一周，观察用户反馈
- 准备下一版本功能规划"
```

#### 6. 项目完成后

```bash
# 保留计划 1-2 周后删除
llm-memory plan delete --code "plan-user-auth-v2"
```

---

## 示例 2: 技术债务重构

### 场景
重构一个老旧的 API 服务，改善代码质量和性能。

### 操作流程

```bash
# 1. 创建重构计划
llm-memory plan create \
  --code "plan-api-refactor" \
  --title "API 服务重构" \
  --description "重构老旧的 API 服务，提升性能和可维护性" \
  --content "## 目标
- 提升 API 响应速度 50%
- 减少技术债务
- 改善代码可维护性

## 现状分析
- 代码混乱，难以维护
- 性能瓶颈明显
- 缺少测试覆盖

## 重构步骤
1. 代码审计，识别问题点
2. 性能分析，找出瓶颈
3. 编写测试用例（重构前）
4. 逐步重构，保持功能不变
5. 性能优化
6. 文档更新

## 风险控制
- 保持向后兼容
- 充分测试
- 灰度发布"

# 2. 阶段 1：审计和分析
llm-memory memory create \
  --code "mem-api-bottlenecks" \
  --title "API 性能瓶颈分析" \
  --content "## 发现的问题
1. 数据库查询未优化（N+1 问题）
2. 缺少缓存机制
3. 同步处理导致阻塞
4. 日志过于详细影响性能

## 性能数据
- 平均响应时间：800ms
- P99：2000ms
- QPS：500

## 优化目标
- 平均响应时间：<300ms
- P99：<800ms
- QPS：>1000"

llm-memory plan update --code "plan-api-refactor" --progress 20

# 3. 阶段 2：编写测试
llm-memory todo create \
  --code "todo-write-integration-tests" \
  --title "编写集成测试" \
  --description "覆盖所有 API 端点"

llm-memory plan update --code "plan-api-refactor" --progress 35

# 4. 阶段 3-4：重构实施
llm-memory memory create \
  --code "mem-refactor-patterns" \
  --title "重构采用的设计模式" \
  --content "## 应用的模式
1. Repository Pattern - 数据访问层
2. Service Layer - 业务逻辑层
3. Dependency Injection - 依赖管理
4. Factory Pattern - 对象创建

## 重构策略
采用 Strangler Fig 模式，逐步替换老代码"

llm-memory plan update --code "plan-api-refactor" --progress 70

# 5. 阶段 5：性能优化
llm-memory memory create \
  --code "mem-api-optimization-results" \
  --title "API 优化结果" \
  --content "## 优化措施
1. 添加 Redis 缓存
2. 数据库查询优化（解决 N+1）
3. 异步处理耗时操作
4. 减少不必要的日志

## 性能改善
| 指标 | 优化前 | 优化后 | 提升 |
|-----|-------|-------|-----|
| 平均响应时间 | 800ms | 250ms | 69% |
| P99 | 2000ms | 600ms | 70% |
| QPS | 500 | 1200 | 140% |

## 成本
- Redis 服务器：$50/月
- 开发时间：3 周"

llm-memory plan update --code "plan-api-refactor" --progress 100

# 6. 总结并归档
llm-memory plan get --code "plan-api-refactor"

# 一周后删除
llm-memory plan delete --code "plan-api-refactor"
```

---

## 示例 3: 数据库迁移项目

### 场景
从 MySQL 迁移到 PostgreSQL，需要仔细规划和执行。

```bash
# 1. 创建迁移计划
llm-memory plan create \
  --code "plan-db-migration" \
  --title "数据库迁移：MySQL → PostgreSQL" \
  --description "将生产数据库从 MySQL 迁移到 PostgreSQL" \
  --content "## 目标
安全、无缝地完成数据库迁移

## 迁移原因
- PostgreSQL 更好的 JSON 支持
- 更强大的全文搜索
- 更好的扩展性

## 迁移步骤
1. 评估和规划（1 周）
2. 搭建测试环境（3 天）
3. 数据迁移脚本开发（1 周）
4. 应用程序适配（2 周）
5. 测试验证（1 周）
6. 迁移执行（1 天）
7. 监控和优化（1 周）

## 风险
- 数据丢失风险
- 停机时间
- SQL 语法差异
- 应用程序兼容性

## 回滚方案
保持 MySQL 备份 2 周"

# 2. 记录关键决策
llm-memory memory create \
  --code "mem-db-migration-strategy" \
  --title "数据库迁移策略" \
  --content "## 选择的迁移方案
双写策略 + 数据同步

## 步骤
1. 搭建 PostgreSQL
2. 实时同步数据（MySQL → PostgreSQL）
3. 应用程序双写（写入两个数据库）
4. 验证数据一致性
5. 切换读流量到 PostgreSQL
6. 停止双写，完全切换
7. 监控 1 周，关闭 MySQL

## 优点
- 零停机时间
- 可以随时回滚
- 充分测试

## 缺点
- 双写期间系统复杂度高
- 需要数据一致性校验工具"

# 3. 开发阶段
llm-memory todo create \
  --code "todo-setup-pg" \
  --title "搭建 PostgreSQL 环境"

llm-memory todo create \
  --code "todo-data-sync-tool" \
  --title "开发数据同步工具"

llm-memory todo create \
  --code "todo-schema-conversion" \
  --title "转换数据库 Schema"

# 4. 持续更新进度
llm-memory plan update --code "plan-db-migration" --progress 30
llm-memory plan update --code "plan-db-migration" --progress 50
llm-memory plan update --code "plan-db-migration" --progress 75

# 5. 迁移执行日
llm-memory memory create \
  --code "mem-db-migration-log" \
  --title "数据库迁移执行记录" \
  --content "## 迁移时间
2025-12-15 02:00 - 06:00（凌晨）

## 执行步骤
1. 02:00 - 停止应用程序写入
2. 02:10 - 最后一次数据同步
3. 02:30 - 数据一致性校验（通过）
4. 03:00 - 切换应用程序到 PostgreSQL
5. 03:15 - 启动应用程序
6. 03:30 - 冒烟测试（通过）
7. 04:00 - 开放用户访问
8. 04:00-06:00 - 密切监控

## 结果
- 数据迁移成功
- 停机时间：15 分钟
- 数据完整性：100%
- 性能：提升 20%

## 问题
- 全文搜索索引需要重建（已解决）
- 部分查询性能不如预期（优化中）"

llm-memory plan update --code "plan-db-migration" --progress 95

# 6. 一周后完成
llm-memory plan update --code "plan-db-migration" --progress 100
```

---

## 示例 4: 多人协作项目

### 场景
团队协作开发一个大型功能，需要协调多人工作。

```bash
# 1. 创建协作计划
llm-memory plan create \
  --code "plan-team-feature-x" \
  --title "功能 X 开发（团队协作）" \
  --description "3 人团队协作开发功能 X" \
  --content "## 团队分工
- Alice: 后端 API
- Bob: 前端 UI
- Charlie: 数据库设计

## 里程碑
- Week 1: 设计和接口定义
- Week 2-3: 并行开发
- Week 4: 集成测试

## 协作要点
- 每日站会同步进度
- API 接口先行
- 及时沟通阻塞

## 依赖关系
- Bob 依赖 Alice 的 API
- Alice 依赖 Charlie 的数据库设计"

# 2. 记录接口约定
llm-memory memory create \
  --code "mem-feature-x-api-contract" \
  --title "功能 X API 接口约定" \
  --content "## API 端点
\`\`\`
GET /api/feature-x/list
POST /api/feature-x/create
PUT /api/feature-x/:id
DELETE /api/feature-x/:id
\`\`\`

## 数据格式
\`\`\`json
{
  \"id\": \"string\",
  \"name\": \"string\",
  \"status\": \"enum\",
  \"createdAt\": \"timestamp\"
}
\`\`\`

## 负责人
Alice (alice@company.com)

## 预计完成
2025-12-10"

# 3. 跟踪个人任务
llm-memory todo create \
  --code "todo-alice-api" \
  --title "[Alice] 实现 API"

llm-memory todo create \
  --code "todo-bob-ui" \
  --title "[Bob] 实现 UI"

llm-memory todo create \
  --code "todo-charlie-db" \
  --title "[Charlie] 数据库设计"

# 4. 记录协作中的问题
llm-memory memory create \
  --code "mem-feature-x-integration-issue" \
  --title "功能 X 集成问题" \
  --content "## 问题
API 返回格式与前端预期不一致

## 原因
沟通不充分，API 设计变更未通知

## 解决方案
1. 统一使用 OpenAPI 规范
2. API 变更需通知团队
3. 建立接口变更评审机制

## 后续
更新 API 文档，前端适配新格式"

# 5. 完成
llm-memory plan update --code "plan-team-feature-x" --progress 100
```

---

## 最佳实践总结

### 1. 计划创建时

- **详细但不冗长**：包含目标、步骤、风险、资源
- **设定清晰的里程碑**：便于跟踪进度
- **识别依赖关系**：提前规避风险

### 2. 执行过程中

- **定期更新进度**：建议每周或完成关键步骤后
- **记录关键决策**：使用 Memory 保存重要决定
- **及时调整计划**：根据实际情况灵活调整

### 3. 与其他工具配合

- **Plan + Todo**：计划管理整体，任务管理细节
- **Plan + Memory**：记录过程中的重要决策和经验
- **跨工具引用**：在计划中引用相关记忆和任务

### 4. 项目完成后

- **总结经验教训**：记录到 Memory 供未来参考
- **保留一段时间**：不要立即删除，便于回顾
- **提取可复用内容**：模式、方案、工具等

---

## 相关资源

- [命令详解](./commands.md)
- [Todo 使用示例](../../manage-tasks/references/examples.md)
- [Memory 使用示例](../../record-knowledge/references/examples.md)
