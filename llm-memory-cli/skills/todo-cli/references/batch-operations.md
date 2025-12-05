# 批量操作完整手册

## 为什么使用批量操作

### 效率对比

**单个创建方式（5 个任务需要 5 次命令）：**
```bash
./llm-memory todo create --code "todo-1" --title "任务1" --priority 3
./llm-memory todo create --code "todo-2" --title "任务2" --priority 3
./llm-memory todo create --code "todo-3" --title "任务3" --priority 2
./llm-memory todo create --code "todo-4" --title "任务4" --priority 2
./llm-memory todo create --code "todo-5" --title "任务5" --priority 1
# 等待多次命令执行...
```

**批量创建方式（1 个命令搞定）：**
```bash
./llm-memory todo batch-create --json '[
  {"code":"todo-1","title":"任务1","priority":3},
  {"code":"todo-2","title":"任务2","priority":3},
  {"code":"todo-3","title":"任务3","priority":2},
  {"code":"todo-4","title":"任务4","priority":2},
  {"code":"todo-5","title":"任务5","priority":1}
]'
```

### 批量操作优势

1. **效率提升**：减少命令执行次数
2. **原子性**：所有操作在同一事务中完成
3. **错误处理**：统一处理，提供详细反馈
4. **状态一致**：避免中间状态的不一致
5. **易于管理**：JSON 格式便于版本控制和复用

---

## 所有批量命令详细说明

### 1. 批量创建 (batch-create)

#### 语法 - JSON 格式

```bash
./llm-memory todo batch-create --json '[
  {
    "code": "todo-code-1",
    "title": "任务标题 1",
    "description": "可选的任务描述",
    "priority": 3
  },
  {
    "code": "todo-code-2",
    "title": "任务标题 2",
    "priority": 2
  }
]'
```

#### 语法 - JSON 文件格式

```bash
./llm-memory todo batch-create --json-file ./todos.json
```

#### JSON 结构说明

```json
{
  "code": "todo-fix-login-bug",      // 必填：待办标识码
  "title": "修复登录 Bug",            // 必填：任务标题
  "description": "排查认证问题",      // 可选：任务详情
  "priority": 4                       // 可选：优先级 1-4，默认 2
}
```

#### 详细示例

**认证系统重构 - 5 个相关任务：**
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

#### 响应格式

成功创建：
```
✅ 批量创建成功! 共处理 5 个待办事项

Created:
- todo-design-auth-schema
- todo-implement-jwt
- todo-auth-api-endpoints
- todo-auth-middleware
- todo-auth-unit-tests
```

部分失败：
```
⚠️ 批量创建完成! 共处理 5 个待办事项 (3 成功, 2 失败)

Created:
- todo-design-auth-schema
- todo-implement-jwt

Failed:
- todo-auth-api-endpoints (Code already exists)
- todo-auth-middleware (Invalid priority: 5)
```

#### 限制

- **最多 100 个**任务/操作
- **Code 必须唯一**
- **Priority 必须在 1-4 范围内**

---

### 2. 批量开始 (batch-start)

#### 语法

```bash
./llm-memory todo batch-start --codes "code1,code2,code3"
```

#### 参数说明

- `--codes`: 逗号分隔的 code 列表（无空格）

#### 示例

**开始 3 个任务：**
```bash
./llm-memory todo batch-start --codes "todo-design-auth-schema,todo-implement-jwt,todo-auth-api-endpoints"
```

**开始所有待处理任务：**
```bash
# 先列出所有待处理任务
./llm-memory todo list --filter pending

# 然后开始它们
./llm-memory todo batch-start --codes "todo-1,todo-2,todo-3"
```

#### 响应示例

```
✅ 批量开始成功! 共处理 3 个待办事项

Started:
- todo-design-auth-schema
- todo-implement-jwt
- todo-auth-api-endpoints
```

---

### 3. 批量完成 (batch-complete)

#### 语法

```bash
./llm-memory todo batch-complete --codes "code1,code2,code3"
```

#### 示例

**完成认证系统的所有任务：**
```bash
./llm-memory todo batch-complete --codes "todo-design-auth-schema,todo-implement-jwt,todo-auth-api-endpoints,todo-auth-middleware,todo-auth-unit-tests"
```

**完成今天的所有任务：**
```bash
# 查看今天的任务
./llm-memory todo list --sort created

# 完成它们
./llm-memory todo batch-complete --codes "task1,task2,task3"
```

---

### 4. 批量取消 (batch-cancel)

#### 语法

```bash
./llm-memory todo batch-cancel --codes "code1,code2"
```

#### 说明

- 将任务状态改为"已取消"
- 与"已完成"不同，表示任务不再进行
- 已取消的任务可以后续恢复（如支持）

#### 示例

**取消不再需要的任务：**
```bash
./llm-memory todo batch-cancel --codes "todo-deprecated-1,todo-deprecated-2,todo-no-longer-needed"
```

---

### 5. 批量删除 (batch-delete)

#### 语法

```bash
./llm-memory todo batch-delete --codes "code1,code2" [--force]
```

#### 参数说明

- `--codes`: 逗号分隔的 code 列表
- `--force`: 强制删除，无需确认（可选）

#### 示例

**删除已完成的任务（需要确认）：**
```bash
./llm-memory todo batch-delete --codes "todo-fix-login-bug,todo-update-docs"
# 提示：确定要删除 2 个待办吗？(y/n)
```

**强制删除（无需确认）：**
```bash
./llm-memory todo batch-delete --codes "todo-fix-login-bug,todo-update-docs" --force
# 输出：✅ 已删除 2 个待办事项
```

#### 警告

- 删除是不可逆的操作
- 使用 `--force` 时要格外谨慎

---

### 6. 批量更新 (batch-update)

#### 语法

```bash
./llm-memory todo batch-update --json '[
  {"code":"code1","priority":3,"title":"新标题"},
  {"code":"code2","priority":2}
]'
```

#### JSON 结构

```json
{
  "code": "todo-design-auth-schema",    // 必填：待办标识码
  "priority": 3,                        // 可选：更新优先级
  "title": "新的标题",                   // 可选：更新标题
  "description": "新的描述"              // 可选：更新描述
}
```

#### 示例

**调整认证项目的优先级：**
```bash
./llm-memory todo batch-update --json '[
  {
    "code": "todo-design-auth-schema",
    "priority": 4,
    "description": "紧急：需要在 JWT 实现前完成"
  },
  {
    "code": "todo-auth-unit-tests",
    "priority": 3,
    "title": "编写认证集成测试和单元测试"
  }
]'
```

**降低长期任务的优先级：**
```bash
./llm-memory todo batch-update --json '[
  {"code":"todo-refactor-old-code","priority":1},
  {"code":"todo-technical-debt-cleanup","priority":1}
]'
```

#### 响应示例

```
✅ 批量更新成功! 共处理 2 个待办事项

Updated:
- todo-design-auth-schema (priority: 3 → 4)
- todo-auth-unit-tests (priority: 2 → 3)
```

---

## 混合模式结果处理

### 部分失败处理

当批量操作中有任务失败时的处理方式：

```bash
# 例如批量创建 5 个任务，其中 2 个失败
./llm-memory todo batch-create --json '[...]'

# 输出
⚠️ 批量创建完成! 共处理 5 个待办事项 (3 成功, 2 失败)

Created:
- todo-task-1
- todo-task-2
- todo-task-3

Failed:
- todo-task-4 (Code already exists)
- todo-task-5 (Invalid priority: 5)

# 修正后重试失败的任务
./llm-memory todo batch-create --json '[
  {"code":"todo-task-4-new","title":"任务4（重命名）","priority":3},
  {"code":"todo-task-5","title":"任务5","priority":3}
]'
```

### 事务特性

- **原子性**：同一次操作中的所有成功任务是同时提交的
- **隔离性**：失败的任务不会影响已成功的任务
- **一致性**：数据状态始终保持一致

---

## JSON 文件方式

### 创建待办列表文件

**todos.json:**
```json
[
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
  }
]
```

### 使用文件批量创建

```bash
# 基于 todos.json 创建
./llm-memory todo batch-create --json-file ./todos.json
```

### 优势

1. **易于版本控制**：JSON 文件可提交到 git
2. **可复用性**：保存常用的任务模板
3. **易于编辑**：使用任何文本编辑器修改
4. **大量操作**：处理大量任务时更清晰

### 实际场景

**sprint-planning.json - Sprint 规划模板：**
```json
[
  {
    "code": "todo-sprint-dev-feature-1",
    "title": "开发功能 A",
    "priority": 3
  },
  {
    "code": "todo-sprint-dev-feature-2",
    "title": "开发功能 B",
    "priority": 3
  },
  {
    "code": "todo-sprint-testing",
    "title": "测试和 QA",
    "priority": 2
  },
  {
    "code": "todo-sprint-docs",
    "title": "编写文档",
    "priority": 2
  },
  {
    "code": "todo-sprint-release",
    "title": "发布和部署",
    "priority": 4
  }
]
```

**使用模板创建 Sprint 任务：**
```bash
./llm-memory todo batch-create --json-file ./sprint-planning.json
```

---

## 最佳实践

### 1. 合理分组

将相关的任务组在一起：
```bash
# ❌ 错误：混合无关任务
./llm-memory todo batch-create --json '[
  {"code":"todo-auth-refactor"...},
  {"code":"todo-ui-redesign"...},
  {"code":"todo-database-migration"...}
]'

# ✅ 正确：按项目/功能分组
# auth-tasks.json
./llm-memory todo batch-create --json-file ./auth-tasks.json
# ui-tasks.json
./llm-memory todo batch-create --json-file ./ui-tasks.json
```

### 2. 优先级一致性

同组任务优先级应该合理：
```json
[
  {"code":"todo-1","priority":4},  // 关键
  {"code":"todo-2","priority":3},  // 高
  {"code":"todo-3","priority":3},  // 高
  {"code":"todo-4","priority":2}   // 中
]
```

### 3. 命名约定

保持 code 的命名一致：
```json
[
  {"code":"todo-auth-design"},
  {"code":"todo-auth-implement"},
  {"code":"todo-auth-test"}
]
```

### 4. 增量更新

使用 batch-update 进行优先级调整：
```bash
# 当发现之前的优先级判断不当时
./llm-memory todo batch-update --json '[
  {"code":"todo-1","priority":2},
  {"code":"todo-2","priority":3}
]'
```

### 5. 定期清理

批量删除已完成的任务：
```bash
./llm-memory todo batch-delete --codes "completed-1,completed-2,completed-3" --force
```

---

## 常见问题

**Q: 可以同时进行多个批量操作吗？**
A: 不推荐。最好等待前一个操作完成后再执行下一个，确保数据一致性。

**Q: 批量操作失败了，部分任务已创建，怎么恢复？**
A: 对失败的任务进行修正，然后重新执行 batch-create。已创建的任务不会重复创建（因为 code 唯一）。

**Q: JSON 文件有大小限制吗？**
A: 没有硬性限制，但建议单个文件包含的任务不超过 100 个。

**Q: 如何避免 Code 重复错误？**
A: 使用唯一的命名规则，例如 `todo-<项目>-<任务序号>`。

---

## 完整工作流示例

```bash
# 1. 创建 Sprint 任务列表
cat > sprint-tasks.json <<EOF
[
  {"code":"todo-feature-1","title":"功能1","priority":3},
  {"code":"todo-feature-2","title":"功能2","priority":3},
  {"code":"todo-testing","title":"测试","priority":2},
  {"code":"todo-docs","title":"文档","priority":2}
]
EOF

# 2. 批量创建
./llm-memory todo batch-create --json-file ./sprint-tasks.json

# 3. 开始核心任务
./llm-memory todo batch-start --codes "todo-feature-1,todo-feature-2"

# 4. 完成部分任务
./llm-memory todo batch-complete --codes "todo-feature-1"

# 5. 调整优先级
./llm-memory todo batch-update --json '[
  {"code":"todo-feature-2","priority":4}
]'

# 6. 完成剩余任务
./llm-memory todo batch-complete --codes "todo-feature-2,todo-testing,todo-docs"

# 7. 清理已完成任务
./llm-memory todo batch-delete --codes "todo-feature-1,todo-feature-2,todo-testing,todo-docs" --force
```

---

参考：[命令参考](./commands.md) | [使用示例](./examples.md)
