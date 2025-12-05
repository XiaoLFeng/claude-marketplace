# 最佳实践指南

## Code 命名规范

### 推荐做法

✅ **使用语义化名称**
- `plan-api-redesign` - 清晰表达意图
- `todo-fix-login` - 一目了然
- `mem-jwt-decision` - 明确主题

✅ **简洁但清晰**
- `todo-fix-login` - 简洁（3 词）
- `todo-add-tests` - 清晰明了
- `mem-api-design` - 恰到好处

✅ **包含动作词（Todo）**
- `todo-add-tests` - 添加
- `todo-update-docs` - 更新
- `todo-fix-bug` - 修复
- `todo-implement-jwt` - 实现
- `todo-refactor-auth` - 重构

✅ **体现层级关系**
- `todo-auth-jwt-impl` - 认证模块下的 JWT 实现
- `todo-auth-middleware` - 认证模块下的中间件
- `todo-auth-validation` - 认证模块下的验证

### 避免做法

❌ **过长**
- `plan-this-is-a-very-long-description-of-the-task`

❌ **过短**
- `abc`, `tsk`

❌ **数字编号**
- `task001`, `todo-123`

❌ **无意义**
- `temp`, `test`, `xxx`

### 命名示例集（50+个）

**Memory 命名：**
1. `mem-api-design` - API 设计文档
2. `mem-database-schema` - 数据库架构
3. `mem-deployment-guide` - 部署指南
4. `mem-security-notes` - 安全笔记
5. `mem-performance-tips` - 性能技巧
6. `mem-code-style` - 代码风格
7. `mem-git-workflow` - Git 工作流
8. `mem-testing-strategy` - 测试策略
9. `mem-error-handling` - 错误处理
10. `mem-cache-strategy` - 缓存策略

**Plan 命名：**
1. `plan-user-system` - 用户系统
2. `plan-api-v2` - API v2 开发
3. `plan-database-migration` - 数据库迁移
4. `plan-auth-refactor` - 认证重构
5. `plan-ui-redesign` - UI 重新设计
6. `plan-payment-integration` - 支付集成
7. `plan-microservices` - 微服务化
8. `plan-mobile-app` - 移动应用
9. `plan-admin-panel` - 管理后台
10. `plan-search-feature` - 搜索功能

**Todo 命名：**
1. `todo-fix-login-bug` - 修复登录
2. `todo-add-api-docs` - 添加文档
3. `todo-update-readme` - 更新 README
4. `todo-implement-jwt` - 实现 JWT
5. `todo-refactor-auth` - 重构认证
6. `todo-add-tests` - 添加测试
7. `todo-optimize-query` - 优化查询
8. `todo-fix-memory-leak` - 修复内存泄漏
9. `todo-upgrade-deps` - 升级依赖
10. `todo-add-logging` - 添加日志

---

## 优先级设置策略

### 推荐做法

✅ **基于影响范围和紧急程度**
- Bug 影响所有用户 → Priority 4
- 功能优化影响部分用户 → Priority 3
- 代码重构不影响用户 → Priority 2

✅ **阻塞性任务设为高优先级**
- 阻塞其他任务 → Priority 4
- 关键路径任务 → Priority 3

✅ **不确定时使用默认（中）**
- 无明确判断依据 → Priority 2

✅ **定期重新评估**
- 每天检查高优先级任务
- 每周评估所有任务
- 根据新情况调整

### 避免做法

❌ **所有任务都是紧急**
- 失去优先级的意义

❌ **从不更新优先级**
- 不适应变化

❌ **忽略依赖关系**
- 导致阻塞

---

## Plan vs Todo 选择

### 使用 Plan

✅ **多步骤复杂任务**（>3步）
- 需要详细的实施步骤
- 有明确的阶段划分

✅ **长期目标**（>3天）
- 需要持续跟踪
- 有里程碑节点

✅ **需要跟踪整体进度**
- 使用 0-100% 进度表示
- 需要向上汇报进度

✅ **有子任务依赖关系**
- 任务之间有先后顺序
- 需要管理依赖

### 使用 Todo

✅ **单一任务**
- 可以一次完成
- 无需拆分

✅ **短期行动项**（<1天）
- 快速完成
- 无需持续跟踪

✅ **独立任务**（无依赖）
- 不依赖其他任务
- 可以随时开始

✅ **简单的提醒事项**
- 无需详细计划
- 只需记录即可

### 决策流程

```
任务分析
  ↓
步骤数量？
  >3步 → 考虑 Plan
  ≤3步 → 考虑 Todo
  ↓
时间跨度？
  >3天 → Plan
  ≤1天 → Todo
  ↓
依赖关系？
  有依赖 → Plan
  无依赖 → Todo
  ↓
进度跟踪？
  需要 → Plan
  不需要 → Todo
```

---

## Memory 内容组织

### 推荐做法

✅ **使用 Markdown 格式**
```markdown
# 标题

## 背景

## 核心内容

## 代码示例

## 参考链接
```

✅ **包含代码示例**
- 便于理解和复用
- 提供完整的上下文

✅ **添加参考链接**
- 链接到官方文档
- 链接到相关讨论

✅ **使用合适的分类和标签**
- 分类：架构设计、问题解决、代码示例、项目规范
- 标签：auth, jwt, api, security 等

✅ **内容结构清晰**
- 有标题和章节
- 使用列表和代码块
- 逻辑清晰

### 避免做法

❌ **纯文本无格式**
- 难以阅读
- 缺少结构

❌ **重复记录相似内容**
- 浪费空间
- 难以维护

❌ **缺少上下文信息**
- 不知道为什么记录
- 无法理解应用场景

❌ **标签过多或过少**
- 过多：难以筛选
- 过少：难以分类

### Memory 内容模板

**架构决策模板：**
```markdown
# <技术选型> 决策记录

## 背景
<为什么需要做这个决策>

## 候选方案
- 方案 A：<优缺点>
- 方案 B：<优缺点>

## 最终决策
<选择了哪个方案>

## 理由
<选择的原因>

## 影响
<对项目的影响>

## 参考资料
- [Link 1](URL)
```

**问题解决模板：**
```markdown
# <问题描述> 解决方案

## 问题现象
<具体表现>

## 排查过程
1. <步骤1>
2. <步骤2>

## 根本原因
<问题根源>

## 解决方案
<如何解决>

## 代码示例
\`\`\`language
<code>
\`\`\`

## 预防措施
<如何避免再次发生>
```

**代码示例模板：**
```markdown
# <功能> 代码示例

## 使用场景
<何时使用>

## 代码
\`\`\`language
<code>
\`\`\`

## 说明
<代码解释>

## 注意事项
<使用时要注意什么>
```

---

## 作用域选择（MCP）

### Personal 作用域

**使用场景：**
- 项目特定的记录
- 临时笔记和想法
- 本地实验数据
- 个人开发任务

**示例：**
```javascript
memory_create({
  code: "mem-local-config",
  title: "本地开发配置",
  content: "...",
  scope: "personal"
})
```

### Group 作用域

**使用场景：**
- 团队协作项目
- 多仓库管理
- 共享的开发环境
- 团队规范

**示例：**
```javascript
plan_create({
  code: "plan-team-project",
  title: "团队项目计划",
  description: "...",
  content: "...",
  scope: "group"
})
```

### Global 作用域（仅 Memory）

**使用场景：**
- 通用知识和最佳实践
- 跨项目共享的配置
- 用户全局偏好
- 技术文档

**示例：**
```javascript
memory_create({
  code: "mem-git-best-practices",
  title: "Git 最佳实践",
  content: "...",
  global: true
})
```

---

## 批量操作技巧

### 推荐做法

✅ **先查询 list 获取所有项**
```bash
# CLI
./main todo list

# MCP
todo_list({ scope: "all" })
```

✅ **筛选需要操作的 codes**
- 按优先级筛选
- 按状态筛选
- 按创建时间筛选

✅ **使用 batch 工具批量处理**
```bash
# CLI
./main todo batch-complete --codes "t1,t2,t3"

# MCP
todo_batch_complete({ codes: ["t1", "t2", "t3"] })
```

✅ **检查返回结果处理失败项**
- 查看 failure_count
- 查看 failures 数组
- 重新提交失败的项

### 避免做法

❌ **循环调用单个创建**（效率低）
- 5 个任务 = 5 次命令调用

❌ **不检查批量操作结果**
- 可能有部分失败
- 需要处理失败项

❌ **超过 100 个不分批**
- 批量操作限制 100 个
- 需要分批处理

---

## 工作流程建议

### 完整流程

1. **需求分析** → 评估复杂度和时间
2. **设计方案** → 选择 Plan/Todo/Memory
3. **用户确认** → 展示建议等待确认
4. **创建记录** → 执行命令/工具
5. **执行任务** → 更新状态
6. **记录知识** → 关键节点创建 Memory
7. **回顾总结** → 评估和改进

### 日常习惯

**每天开始：**
```bash
# CLI
./main todo list

# MCP
todo_list({ scope: "all" })
```

**任务开始：**
```bash
# CLI
./main todo start --code "todo-xxx"

# MCP
todo_batch_start({ codes: ["todo-xxx"] })
```

**任务完成：**
```bash
# CLI
./main todo complete --code "todo-xxx"

# MCP
todo_batch_complete({ codes: ["todo-xxx"] })
```

**新发现：**
```bash
# CLI
./main memory create --code "mem-xxx" --title "..." --content "..."

# MCP
memory_create({
  code: "mem-xxx",
  title: "...",
  content: "..."
})
```

**每周回顾：**
- 评估进度和优先级
- 清理已完成的记录
- 重新规划下周任务

---

## 进度跟踪最佳实践

### 开始任务前

✅ **执行 start 命令标记为进行中**
```bash
# CLI
./main todo start --code "todo-xxx"
./main plan start --code "plan-xxx"

# MCP
todo_batch_start({ codes: ["todo-xxx"] })
plan_update({ code: "plan-xxx", progress: 1 })
```

✅ **确认没有依赖任务未完成**
- 检查前置任务状态
- 确保不会被阻塞

### 任务完成后

✅ **立即执行 complete 命令**
```bash
# CLI
./main todo complete --code "todo-xxx"
./main plan complete --code "plan-xxx"

# MCP
todo_batch_complete({ codes: ["todo-xxx"] })
plan_update({ code: "plan-xxx", progress: 100 })
```

✅ **如果有新发现，创建 Memory 记录**
- 记录解决方案
- 记录踩坑经验

✅ **如果有新任务，创建新 Todo**
- 衍生的新任务
- 后续优化任务

### 定期检查

✅ **每天执行 list 查看未完成任务**
```bash
# CLI
./main todo list
./main plan list

# MCP
todo_list({ scope: "all" })
plan_list({ scope: "all" })
```

✅ **重新评估优先级**
- 根据新情况调整
- 提升/降低优先级

✅ **识别阻塞任务**
- 长期停滞的任务
- 依赖未解决的任务

---

## Plan 进度管理

### 合理设置里程碑

**示例：用户认证系统重构**

```
0%   - 计划创建
20%  - 需求分析完成
40%  - 数据库设计完成
60%  - JWT 实现完成
80%  - 中间件和测试完成
100% - 全部完成
```

### 进度更新频率

**建议频率：**
- 短期项目（<1周）：每天更新
- 中期项目（1-4周）：每2-3天更新
- 长期项目（>4周）：每周更新

### 进度与 Todo 状态关联

**策略：** Plan 进度根据 Todo 完成情况自动计算

```
Plan: 用户认证系统（5 个 Todo）
  ├─ todo-design-db (completed)     ✅
  ├─ todo-impl-jwt (completed)      ✅
  ├─ todo-add-middleware (in_progress) ⏳
  ├─ todo-add-tests (pending)       ⏸️
  └─ todo-update-docs (pending)     ⏸️

进度计算：2/5 = 40%
```

---

## 批量操作最佳实践

### 分批处理策略

**大于 100 个时分批：**
```javascript
// MCP 示例
const allItems = [...]; // 500 个
const batchSize = 100;
const results = [];

for (let i = 0; i < allItems.length; i += batchSize) {
  const batch = allItems.slice(i, i + batchSize);
  const result = await todo_batch_create({
    items: batch,
    scope: "personal"
  });
  results.push(result);
}

// 汇总结果
const totalSuccess = results.reduce((sum, r) => sum + r.success_count, 0);
const totalFailure = results.reduce((sum, r) => sum + r.failure_count, 0);
```

### 错误重试策略

**收集失败项并重试：**
```javascript
// 第一次批量操作
const result1 = await todo_batch_create({ items: allItems });

// 收集失败项
const failedCodes = result1.failures.map(f => f.code);
const failedItems = allItems.filter(item =>
  failedCodes.includes(item.code)
);

// 修正失败项（如修正 code 格式）
const correctedItems = failedItems.map(item => ({
  ...item,
  code: item.code.replace(/_/g, '-').toLowerCase()
}));

// 重新提交
const result2 = await todo_batch_create({ items: correctedItems });
```

### 性能优化

✅ **优先使用批量操作**
- 1 次调用 vs 100 次调用
- 节省网络开销
- 提高执行效率

❌ **避免循环调用单个命令**
```javascript
// ❌ 低效方式
for (const item of items) {
  await todo_create(item); // 100 次调用
}

// ✅ 高效方式
await todo_batch_create({ items }); // 1 次调用
```

---

## 多项目管理

### 策略 1：使用项目前缀

**命名规范：**
```
<项目名>-<功能>-<描述>

示例：
- myapp-todo-fix-login
- webapp-mem-api-design
- backend-plan-refactor
```

### 策略 2：使用 Group 功能（MCP）

**设置组：**
```javascript
// 添加路径到组
group_add_path({
  group_name: "my-team-project",
  path: "/path/to/project1"
})

group_add_path({
  group_name: "my-team-project",
  path: "/path/to/project2"
})

// 创建组级别的任务
todo_batch_create({
  items: [...],
  scope: "group"
})
```

### 策略 3：使用全局作用域（Memory）

**通用知识：**
```javascript
memory_create({
  code: "mem-git-workflow",
  title: "Git 工作流规范",
  content: "...",
  global: true  // 所有项目可见
})
```

---

## 代码风格建议

### Memory 内容格式

**推荐结构：**
```markdown
# 主题

## 概述
一句话说明这是什么

## 详细说明
具体内容

## 示例
代码或配置示例

## 参考
相关链接
```

### Todo 描述格式

**推荐格式：**
```
任务：<具体做什么>
背景：<为什么要做>
验收：<如何判断完成>
```

**示例：**
```
任务：实现 JWT 令牌机制
背景：当前 Session 认证不适合分布式部署
验收：通过单元测试，支持令牌签发和验证
```

### Plan 内容格式

**推荐结构：**
```markdown
# 项目计划

## 目标
<要达成什么>

## 阶段划分
### 阶段 1：<名称>（进度 0-20%）
- 任务 1
- 任务 2

### 阶段 2：<名称>（进度 20-40%）
- 任务 3
- 任务 4

## 里程碑
- 里程碑 1：<描述>（目标日期）
- 里程碑 2：<描述>（目标日期）

## 风险
- 风险 1：<描述>
- 缓解措施：<如何应对>
```

---

## 团队协作建议

### 命名约定

**统一团队的命名规范：**
- 使用相同的前缀格式
- 统一动作词（fix/add/update/implement）
- 统一领域词（auth/api/ui/db）

### 优先级共识

**团队达成一致的优先级标准：**
- 明确什么算紧急
- 明确什么算重要
- 定期校准判断

### 作用域使用

**建议分配：**
- personal：个人开发任务
- group：团队协作任务
- global：团队规范和知识

---

## 总结

最佳实践的核心：

- ✅ 使用清晰的命名
- ✅ 合理判断优先级
- ✅ 正确选择 Plan/Todo
- ✅ 组织好 Memory 内容
- ✅ 善用批量操作
- ✅ 定期跟踪进度
- ✅ 与团队达成共识

**持续改进：**
- 回顾自己的命名是否合理
- 评估优先级判断是否准确
- 优化工作流程
- 与团队分享经验

---

## 参考资料

- [Code 格式规则](./code-format.md)
- [优先级判断指南](./priority-guide.md)
- [故障排除](./troubleshooting.md)
