# Plan CLI 使用示例

真实场景的完整 Plan CLI 使用案例，展示如何在实际项目中应用计划管理。

---

## 示例 1: 用户认证系统重构

### 场景描述

用户需求：重构现有的用户认证系统，采用 JWT 机制，支持 refresh token。

**项目特点：**
- 复杂度：高
- 预计时长：1 周
- 涉及模块：数据库、后端 API、中间件、测试
- 依赖关系：有明确的任务依赖

### 步骤 1: 创建计划

```bash
./main plan create \
  --code "plan-auth-refactor" \
  --title "用户认证系统重构" \
  --description "采用 JWT 机制，支持 refresh token，提升安全性" \
  --content "# 用户认证系统重构实施计划

## 阶段 1: 数据库设计 (Day 1-2)
- 设计 users 表结构
- 设计 refresh_tokens 表结构
- 添加必要的索引和约束
- 编写数据库迁移脚本

## 阶段 2: JWT 核心实现 (Day 2-3)
- 实现 JWT 生成逻辑
- 实现 JWT 验证逻辑
- 实现 refresh token 机制
- 配置过期时间和密钥管理

## 阶段 3: API 端点开发 (Day 3-4)
- POST /api/auth/register - 用户注册
- POST /api/auth/login - 用户登录
- POST /api/auth/refresh - 刷新令牌
- POST /api/auth/logout - 登出

## 阶段 4: 中间件和安全 (Day 4-5)
- 实现 JWT 验证中间件
- 添加到受保护路由
- 实现登录失败限流
- CSRF 保护

## 阶段 5: 测试和验证 (Day 5-7)
- 单元测试（覆盖率 > 80%）
- 集成测试
- 安全测试
- 性能测试"
```

**输出示例：**
```
计划创建成功！标识码: plan-auth-refactor, 标题: 用户认证系统重构
```

### 步骤 2: 开始计划

```bash
./main plan start --code "plan-auth-refactor"
# 输出：计划 plan-auth-refactor 已开始
```

### 步骤 3: 第 1 天 - 完成数据库设计

```bash
# 完成第一阶段任务
# ... 工作中 ...

# 更新进度 (阶段 1 完成)
./main plan progress --code "plan-auth-refactor" --progress 20
# 输出：计划进度已更新至 20%
```

### 步骤 4: 第 3 天 - 完成 JWT 实现

```bash
# 完成第二阶段任务
# ... 工作中 ...

# 更新进度 (阶段 1-2 完成)
./main plan progress --code "plan-auth-refactor" --progress 50
# 输出：计划进度已更新至 50%
```

### 步骤 5: 第 5 天 - API 和中间件完成

```bash
# 完成第三、四阶段任务
# ... 工作中 ...

# 更新进度 (阶段 1-4 完成)
./main plan progress --code "plan-auth-refactor" --progress 80
# 输出：计划进度已更新至 80%
```

### 步骤 6: 第 7 天 - 项目完成

```bash
# 完成全部测试

# 完成计划
./main plan complete --code "plan-auth-refactor"
# 输出：计划 plan-auth-refactor 已完成

# 验证结果
./main plan list
# 应显示 plan-auth-refactor 状态为 completed，进度 100%
```

---

## 示例 2: Plan + Todo 协作完整示例

展示如何在 Plan 和 Todo 之间进行协作。

### 场景：数据库迁移项目

#### 创建 Plan

```bash
./main plan create \
  --code "plan-db-migration" \
  --title "数据库迁移至新架构" \
  --description "将现有 MySQL 迁移至分布式数据库，支持高可用" \
  --content "# 数据库迁移计划

## 阶段 1: 方案设计和准备 (Day 1-2)
- 评估现有数据量和性能
- 设计新的数据库架构
- 制定迁移策略和回滚方案

## 阶段 2: 环境搭建 (Day 2-3)
- 部署新的数据库集群
- 配置备份和同步机制
- 进行灾难恢复演练

## 阶段 3: 数据迁移 (Day 3-5)
- 执行初始全量迁移
- 配置增量同步
- 验证数据完整性

## 阶段 4: 应用适配 (Day 5-6)
- 修改应用连接字符串
- 适配新的数据库特性
- 测试应用集成

## 阶段 5: 验收和回退准备 (Day 6-7)
- 性能基准测试
- 灾难恢复演练
- 制定应急方案"
```

#### 创建关联的 Todo

```bash
# 创建设计阶段的 Todo
./main todo create \
  --code "todo-db-design" \
  --title "设计数据库迁移方案" \
  --description "评估现有数据量，设计新架构" \
  --priority 3

# 创建环境搭建的 Todo
./main todo create \
  --code "todo-db-cluster-setup" \
  --title "部署新数据库集群" \
  --description "配置 MySQL 集群，启用备份同步" \
  --priority 3

# 创建数据迁移的 Todo
./main todo create \
  --code "todo-db-data-migrate" \
  --title "执行数据迁移" \
  --description "全量迁移 + 增量同步" \
  --priority 4

# 创建应用适配的 Todo
./main todo create \
  --code "todo-app-adapt-db" \
  --title "应用适配新数据库" \
  --description "修改连接字符串，测试集成" \
  --priority 3

# 创建测试验收的 Todo
./main todo create \
  --code "todo-db-testing" \
  --title "性能测试和验收" \
  --description "基准测试，灾难恢复演练" \
  --priority 2
```

#### 协作工作流

```bash
# Day 1: 开始计划
./main plan start --code "plan-db-migration"

# Day 1: 开始设计 Todo
./main todo start --code "todo-db-design"

# Day 2: 完成设计 Todo
./main todo complete --code "todo-db-design"

# Day 2: 更新计划进度 (20%)
./main plan progress --code "plan-db-migration" --progress 20

# Day 2: 开始集群搭建
./main todo start --code "todo-db-cluster-setup"

# Day 3: 完成集群搭建
./main todo complete --code "todo-db-cluster-setup"

# Day 3: 更新计划进度 (40%)
./main plan progress --code "plan-db-migration" --progress 40

# Day 3: 开始数据迁移（优先级最高）
./main todo start --code "todo-db-data-migrate"

# Day 5: 完成数据迁移
./main todo complete --code "todo-db-data-migrate"

# Day 5: 更新计划进度 (60%)
./main plan progress --code "plan-db-migration" --progress 60

# Day 5: 开始应用适配
./main todo start --code "todo-app-adapt-db"

# Day 6: 完成应用适配
./main todo complete --code "todo-app-adapt-db"

# Day 6: 更新计划进度 (80%)
./main plan progress --code "plan-db-migration" --progress 80

# Day 6: 开始测试验收
./main todo start --code "todo-db-testing"

# Day 7: 完成测试验收
./main todo complete --code "todo-db-testing"

# Day 7: 完成计划
./main plan complete --code "plan-db-migration"
```

#### 查看最终结果

```bash
# 查看计划
./main plan list
# 输出：plan-db-migration 状态为 completed，进度 100%

# 查看所有 Todo
./main todo list
# 输出：所有 5 个 Todo 状态均为 completed
```

---

## 示例 3: 进度跟踪流程示例

展示如何进行有效的进度跟踪和状态管理。

### 场景：功能特性开发

#### 初始化计划

```bash
./main plan create \
  --code "plan-feature-social" \
  --title "社交功能开发" \
  --description "实现用户关注、评论、点赞等社交功能" \
  --content "# 社交功能开发计划

## 阶段 1: 核心数据模型设计
- 用户关系表设计
- 交互记录表设计
- 通知系统表设计

## 阶段 2: 后端 API 实现
- 关注/取消关注 API
- 评论 API
- 点赞 API
- 通知推送 API

## 阶段 3: 前端集成
- UI 组件开发
- API 集成
- 状态管理

## 阶段 4: 测试上线
- 单元测试
- 集成测试
- 灰度上线"
```

#### 日常进度更新

```bash
# Day 1 - 开始计划
./main plan start --code "plan-feature-social"

# 查看计划状态
./main plan list

# Day 2 - 阶段 1 完成
./main plan progress --code "plan-feature-social" --progress 25
./main plan list  # 验证进度

# Day 4 - 阶段 2 完成
./main plan progress --code "plan-feature-social" --progress 50
./main plan list

# Day 6 - 阶段 3 完成
./main plan progress --code "plan-feature-social" --progress 75
./main plan list

# Day 8 - 测试完成，准备上线
./main plan progress --code "plan-feature-social" --progress 100

# 完成计划
./main plan complete --code "plan-feature-social"

# 最终验证
./main plan list
```

#### 查看历史记录

```bash
# 查看所有计划（包括已完成）
./main plan list

# 应该看到类似输出：
# ┌──────────────────────┬─────────────────┬──────────┬──────────┐
# │ Code                 │ Title           │ Status   │ Progress │
# ├──────────────────────┼─────────────────┼──────────┼──────────┤
# │ plan-feature-social  │ 社交功能开发    │ completed│ 100%     │
# │ plan-auth-refactor   │ 认证系统重构    │ completed│ 100%     │
# │ plan-db-migration    │ 数据库迁移      │ completed│ 100%     │
# └──────────────────────┴─────────────────┴──────────┴──────────┘
```

---

## 示例 4: 多计划并行管理

展示如何同时管理多个计划的最佳实践。

### 创建多个计划

```bash
# 创建计划 1
./main plan create \
  --code "plan-backend-api" \
  --title "后端 API 优化" \
  --description "优化现有 API 性能和结构" \
  --content "# 后端 API 优化..."

# 创建计划 2
./main plan create \
  --code "plan-frontend-ui" \
  --title "前端 UI 重构" \
  --description "统一 UI 风格，提升用户体验" \
  --content "# 前端 UI 重构..."

# 创建计划 3
./main plan create \
  --code "plan-infra-upgrade" \
  --title "基础设施升级" \
  --description "升级服务器和部署流程" \
  --content "# 基础设施升级..."
```

### 并行执行和跟踪

```bash
# 启动所有计划
./main plan start --code "plan-backend-api"
./main plan start --code "plan-frontend-ui"
./main plan start --code "plan-infra-upgrade"

# 查看所有计划状态
./main plan list

# 独立更新每个计划的进度
./main plan progress --code "plan-backend-api" --progress 30
./main plan progress --code "plan-frontend-ui" --progress 20
./main plan progress --code "plan-infra-upgrade" --progress 15

# 再次查看所有计划
./main plan list
# ┌──────────────────────┬──────────────┬───────────┬──────────┐
# │ Code                 │ Title        │ Status    │ Progress │
# ├──────────────────────┼──────────────┼───────────┼──────────┤
# │ plan-backend-api     │ 后端API优化  │ in_prog   │ 30%      │
# │ plan-frontend-ui     │ 前端UI重构   │ in_prog   │ 20%      │
# │ plan-infra-upgrade   │ 基础设施升级 │ in_prog   │ 15%      │
# └──────────────────────┴──────────────┴───────────┴──────────┘

# 后续不同时间更新不同计划
./main plan progress --code "plan-backend-api" --progress 60
./main plan progress --code "plan-infra-upgrade" --progress 50
```

### 完成计划

```bash
# 某个计划首先完成
./main plan complete --code "plan-infra-upgrade"

# 继续跟踪其他计划
./main plan list

# 完成所有计划
./main plan complete --code "plan-backend-api"
./main plan complete --code "plan-frontend-ui"

# 最后查看所有已完成的计划
./main plan list
```

---

## 最佳实践总结

### 1. Code 命名规范

✅ 推荐：
```
plan-auth-refactor        # 认证系统重构
plan-db-migration         # 数据库迁移
plan-api-redesign         # API 重新设计
plan-feature-social       # 社交功能开发
```

❌ 避免：
```
plan-001                  # 无意义编号
Plan_Important            # 大写字母和下划线
plan-                     # 末尾非字母
my_plan_001              # 混合格式
```

### 2. Content 内容组织

✅ 推荐结构：
```markdown
# 计划标题

## 阶段 1: <阶段名称> (时间估计)
- 具体任务 1
- 具体任务 2
- 验收标准

## 阶段 2: ...

## 成功标准
- 指标 1
- 指标 2
```

### 3. 进度更新频率

- **日常**：1-2 天更新一次
- **阶段完成**：必须立即更新
- **周期性**：每周一次全面检查

### 4. Plan 和 Todo 的配合

```bash
# 创建计划后，创建对应的 Todo
./main plan create --code "plan-xxx" ...
./main todo create --code "todo-xxx" ...

# Todo 完成时，更新 Plan 进度
./main todo complete --code "todo-xxx"
./main plan progress --code "plan-xxx" --progress <value>
```

### 5. 异常处理

```bash
# 计划出现阻塞时，可以暂停
./main plan list  # 查看当前状态

# 发现计划过期或不可行时
./main plan delete --code "plan-outdated"

# 需要调整计划内容时
./main plan delete --code "plan-xxx"
# ... 创建新计划 ...
```

---

返回 [Plan CLI Skill](../SKILL.md)
