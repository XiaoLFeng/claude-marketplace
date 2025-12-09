# 工作流参考

## 进度同步流程

当完成一批任务后，需要同步到项目计划的进度，保持 Plan 和 Todo 的一致性。

### 1. 获取任务列表

```bash
llm-memory todo list
```

显示所有待办事项的状态:
- 已完成的任务
- 进行中的任务
- 待开始的任务
- 关联的计划信息

### 2. 获取计划详情

```bash
llm-memory plan get --code plan-xxx
```

获取特定计划的详细信息:
- 计划当前进度
- 计划步骤列表
- 关联的待办任务

### 3. 计算新进度

根据已完成的任务计算计划进度:

**计算公式:**
```
进度 = (已完成任务数 / 总任务数) × 100%
```

**示例:**
```
总任务数: 5
已完成: 3
进度 = (3 / 5) × 100% = 60%
```

### 4. 更新计划进度

```bash
llm-memory plan update --code plan-xxx --progress 60
```

更新计划的进度百分比。

## 常见场景

### 场景 1: 批量完成任务后同步

用户完成了多个待办任务,需要更新整体进度。

**操作**:
1. 执行 `llm-memory todo list` 查看已完成的任务
2. 确定这些任务关联的计划
3. 计算新的进度百分比
4. 执行 `llm-memory plan update --code xxx --progress XX` 更新进度
5. 向用户展示同步结果

**输出示例:**
```
🔄 进度同步完成

📋 [plan-user-auth] 用户认证系统重构

   已完成任务:
   ✅ [todo-jwt-research] 调研 JWT 库
   ✅ [todo-jwt-middleware] 实现 JWT 中间件
   ✅ [todo-jwt-tests] 编写单元测试

   任务进度: 3/5 完成 (60%)
   计划进度: 60% → 已同步
```

### 场景 2: 单个任务完成后同步

用户完成了一个关键任务,需要立即更新进度。

**操作**:
1. 确认任务已标记为完成
2. 查询该任务关联的计划
3. 重新计算进度
4. 更新计划进度

**输出示例:**
```
✅ 任务完成: [todo-jwt-middleware] 实现 JWT 中间件

🔄 同步到计划...

📋 [plan-user-auth] 用户认证系统重构
   进度: 40% → 60% (+20%)
```

### 场景 3: 用户主动要求同步

用户说:"同步一下进度"、"更新计划进度"。

**操作**:
1. 列出所有进行中的计划
2. 逐个计算每个计划的任务完成情况
3. 批量更新所有计划的进度
4. 展示同步报告

**输出示例:**
```
🔄 正在同步所有计划进度...

📋 [plan-user-auth] 用户认证系统重构
   任务: 3/5 完成 → 进度: 60%

📋 [plan-api-refactor] API 重构
   任务: 7/10 完成 → 进度: 70%

✨ 同步完成! 共更新 2 个计划
```

## 详细命令说明

### 1. 列出所有待办

```bash
llm-memory todo list
```

**输出示例:**
```json
[
  {
    "code": "todo-jwt-research",
    "title": "调研 JWT 库",
    "status": "completed",
    "plan": "plan-user-auth",
    "completed_at": "2025-12-08T14:30:00Z"
  },
  {
    "code": "todo-jwt-middleware",
    "title": "实现 JWT 中间件",
    "status": "completed",
    "plan": "plan-user-auth",
    "completed_at": "2025-12-08T16:00:00Z"
  },
  {
    "code": "todo-jwt-tests",
    "title": "编写单元测试",
    "status": "completed",
    "plan": "plan-user-auth",
    "completed_at": "2025-12-08T17:30:00Z"
  },
  {
    "code": "todo-jwt-docs",
    "title": "编写文档",
    "status": "pending",
    "plan": "plan-user-auth"
  },
  {
    "code": "todo-jwt-deploy",
    "title": "部署到生产环境",
    "status": "pending",
    "plan": "plan-user-auth"
  }
]
```

### 2. 获取计划详情

```bash
llm-memory plan get --code plan-user-auth
```

**输出示例:**
```json
{
  "code": "plan-user-auth",
  "title": "用户认证系统重构",
  "description": "将现有认证系统从 Session 迁移到 JWT",
  "status": "in_progress",
  "progress": 40,
  "total_tasks": 5,
  "completed_tasks": 2,
  "created_at": "2025-12-01T10:00:00Z",
  "updated_at": "2025-12-07T15:00:00Z"
}
```

### 3. 更新计划进度

```bash
llm-memory plan update --code plan-user-auth --progress 60
```

**参数说明:**
- `--code`: 计划的唯一标识符
- `--progress`: 新的进度百分比 (0-100)

**输出示例:**
```json
{
  "success": true,
  "code": "plan-user-auth",
  "old_progress": 40,
  "new_progress": 60,
  "updated_at": "2025-12-08T17:45:00Z"
}
```

## 进度计算规则

### 基本规则

1. **任务权重相等**
   - 默认情况下,所有任务权重相等
   - 进度 = 已完成任务数 / 总任务数 × 100%

2. **任务权重不等**
   - 如果任务设置了权重,按权重计算
   - 进度 = Σ(已完成任务权重) / Σ(总任务权重) × 100%

### 示例计算

**示例 1: 权重相等**
```
总任务: 5个
已完成: 3个
进度 = 3/5 × 100% = 60%
```

**示例 2: 权重不等**
```
任务列表:
- 任务A (权重: 2, 已完成)
- 任务B (权重: 1, 已完成)
- 任务C (权重: 3, 进行中)
- 任务D (权重: 1, 待开始)

总权重: 2 + 1 + 3 + 1 = 7
已完成权重: 2 + 1 = 3
进度 = 3/7 × 100% ≈ 43%
```

## 最佳实践

1. **及时同步**
   - 完成重要任务后立即同步进度
   - 避免进度信息长期不一致

2. **批量同步**
   - 完成一批相关任务后统一同步
   - 减少频繁更新的开销

3. **自动检测**
   - 当用户标记任务完成时,自动检测是否需要同步进度
   - 主动提示用户进行同步

4. **进度可视化**
   - 同步时展示进度变化
   - 使用进度条或百分比直观展示

5. **异常处理**
   - 如果计划进度与任务进度严重不符,提醒用户
   - 例如: 任务都完成了但计划进度只有 50%

## 错误处理

### 计划不存在

如果指定的计划代码不存在:
```
❌ 错误: 计划 [plan-xxx] 不存在
   提示: 使用 'llm-memory plan list' 查看所有计划
```

### 进度值无效

如果进度值超出范围 (0-100):
```
❌ 错误: 进度值必须在 0-100 之间
   当前值: 150
```

### 无关联任务

如果计划没有关联任何任务:
```
⚠️ 警告: 计划 [plan-xxx] 没有关联任务
   无法自动计算进度,请手动设置
```

## 相关技能

- [manage-tasks](../../manage-tasks/SKILL.md) - 任务管理
- [manage-project](../../manage-project/SKILL.md) - 项目管理
- [workflow-orchestrator](../../workflow-orchestrator/SKILL.md) - 工作流编排器
