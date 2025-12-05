# Plan CLI 命令完整参考

Plan 命令用于创建、管理和跟踪复杂的多步骤项目和长期目标。

---

## 1. 创建计划 (create)

### 语法

```bash
./main plan create \
  --code <code> \
  --title <title> \
  --description <desc> \
  --content <markdown-content> \
  [--global]
```

### 参数详解

| 参数 | 类型 | 必需 | 说明 |
|------|------|------|------|
| `--code` | string | ✅ | 计划标识码（全小写+连字符，3字符以上） |
| `--title` | string | ✅ | 计划标题（简洁清晰） |
| `--description` | string | ✅ | 计划摘要（一句话说明目标） |
| `--content` | string | ✅ | 详细内容（**Markdown格式**） |
| `--global` | flag | ❌ | 创建全局计划（默认为项目级别） |

### Content 参数详解（Markdown格式建议）

```markdown
# <计划标题>

## 阶段 1: <阶段名称> (Day X-Y)
- 步骤 1：...
- 步骤 2：...
- 验收标准：...

## 阶段 2: <阶段名称> (Day Y-Z)
- 步骤 1：...
- 步骤 2：...

## 时间表
- 预计开始：...
- 预计完成：...

## 成功标准
- 指标 1：...
- 指标 2：...
```

### 示例

```bash
./main plan create \
  --code "plan-api-redesign" \
  --title "API 系统重新设计" \
  --description "优化 API 架构，提升性能和可维护性" \
  --content "# API 系统重新设计

## 阶段 1: 需求分析 (Day 1-2)
- 调研现有 API 瓶颈
- 设计新的 API 结构
- 评估向后兼容性

## 阶段 2: 核心实现 (Day 3-5)
- 实现新 API 端点
- 数据映射层
- 错误处理

## 成功标准
- 性能提升 30%
- 测试覆盖率 > 80%"
```

### 错误处理

```bash
# ❌ Code 格式错误
./main plan create --code "Plan_001" --title "..." --description "..." --content "..."
# 错误：code 格式错误: 全小写字母，可含连字符，开头末尾必须是字母

# ✅ 修正
./main plan create --code "plan-api-design" --title "..." --description "..." --content "..."
```

---

## 2. 列出计划 (list)

### 语法

```bash
./main plan list [--global]
```

### 参数详解

| 参数 | 类型 | 必需 | 说明 |
|------|------|------|------|
| `--global` | flag | ❌ | 显示全局计划（默认显示项目计划） |

### 示例

```bash
# 列出当前项目计划
./main plan list

# 列出全局计划
./main plan list --global
```

### 输出示例

```
┌──────────────────┬────────────────────┬──────────┬──────────┐
│ Code             │ Title              │ Status   │ Progress │
├──────────────────┼────────────────────┼──────────┼──────────┤
│ plan-auth-ref    │ 用户认证系统重构   │ in_prog  │ 45%      │
│ plan-api-design  │ API 重新设计       │ pending  │ 0%       │
└──────────────────┴────────────────────┴──────────┴──────────┘
```

---

## 3. 开始计划 (start)

### 语法

```bash
./main plan start --code <code>
```

### 参数详解

| 参数 | 类型 | 必需 | 说明 |
|------|------|------|------|
| `--code` | string | ✅ | 计划标识码 |

### 功能说明

- 将计划状态从 `pending` 变更为 `in_progress`
- 进度自动设置为 1%
- 记录开始时间戳

### 示例

```bash
./main plan start --code "plan-auth-refactor"
# 输出：计划 plan-auth-refactor 已开始
```

### 错误处理

```bash
# ❌ 计划不存在
./main plan start --code "plan-not-exist"
# 错误：计划不存在或已完成/取消

# ✅ 使用 list 确认 code
./main plan list
```

---

## 4. 更新进度 (progress)

### 语法

```bash
./main plan progress --code <code> --progress <value>
```

### 参数详解

| 参数 | 类型 | 必需 | 说明 |
|------|------|------|------|
| `--code` | string | ✅ | 计划标识码 |
| `--progress` | integer | ✅ | 进度百分比（0-100） |

### 进度对应关系

```
0%        → pending（待开始）
1-99%     → in_progress（进行中）
100%      → completed（已完成）
```

### 示例

```bash
# 更新进度为 50%
./main plan progress --code "plan-auth-refactor" --progress 50

# 更新进度为 100%（完成）
./main plan progress --code "plan-auth-refactor" --progress 100
# 输出：计划已完成
```

### 最佳实践

```bash
# 遵循阶段性进度更新
./main plan progress --code "plan-auth-refactor" --progress 20   # 阶段 1 完成
./main plan progress --code "plan-auth-refactor" --progress 50   # 阶段 2 完成
./main plan progress --code "plan-auth-refactor" --progress 80   # 阶段 3 完成
./main plan progress --code "plan-auth-refactor" --progress 100  # 全部完成
```

---

## 5. 完成计划 (complete)

### 语法

```bash
./main plan complete --code <code>
```

### 参数详解

| 参数 | 类型 | 必需 | 说明 |
|------|------|------|------|
| `--code` | string | ✅ | 计划标识码 |

### 功能说明

- 将计划状态变更为 `completed`
- 进度自动设置为 100%
- 记录完成时间戳

### 示例

```bash
./main plan complete --code "plan-auth-refactor"
# 输出：计划 plan-auth-refactor 已完成
```

### 错误处理

```bash
# ❌ 计划不存在
./main plan complete --code "plan-not-exist"
# 错误：计划不存在或已完成/取消

# ✅ 先查看计划列表
./main plan list
```

---

## 6. 删除计划 (delete)

### 语法

```bash
./main plan delete --code <code>
```

### 参数详解

| 参数 | 类型 | 必需 | 说明 |
|------|------|------|------|
| `--code` | string | ✅ | 计划标识码 |

### 功能说明

- 删除指定计划及其所有关联数据
- 仅在 `pending` 或 `in_progress` 状态可删除
- 已完成的计划需要先取消再删除

### 示例

```bash
./main plan delete --code "plan-obsolete"
# 输出：计划 plan-obsolete 已删除
```

### 错误处理

```bash
# ❌ 已完成的计划无法删除
./main plan delete --code "plan-auth-refactor"
# 错误：已完成的计划无法删除

# ✅ 删除活跃状态的计划
./main plan list
./main plan delete --code "plan-pending-task"
```

---

## 进度管理最佳实践

### 1. 定期更新进度

```bash
# 推荐频率：每 1-2 天更新一次
./main plan progress --code "plan-xxx" --progress <current>

# 阶段完成时必须更新
./main plan progress --code "plan-xxx" --progress 33   # 阶段 1/3 完成
./main plan progress --code "plan-xxx" --progress 66   # 阶段 2/3 完成
./main plan progress --code "plan-xxx" --progress 100  # 完成
```

### 2. 关键时间点检查清单

```bash
# 计划开始时
./main plan start --code "plan-xxx"
./main plan list

# 每个阶段完成时
./main plan progress --code "plan-xxx" --progress <value>

# 计划完成时
./main plan complete --code "plan-xxx"
./main plan list  # 验证状态
```

### 3. 与 Todo 协作

```bash
# 创建计划
./main plan create \
  --code "plan-auth" \
  --title "认证系统重构" \
  --description "..." \
  --content "..."

# 创建关联的 Todo
./main todo create \
  --code "todo-auth-design" \
  --title "设计认证架构" \
  --priority 3

# 任务完成后更新计划进度
./main todo complete --code "todo-auth-design"
./main plan progress --code "plan-auth" --progress 30
```

### 4. 并发处理多个计划

```bash
# 并发创建
./main plan create --code "plan-api" --title "..." --description "..." --content "..."
./main plan create --code "plan-db" --title "..." --description "..." --content "..."

# 查看所有计划
./main plan list

# 独立更新进度
./main plan progress --code "plan-api" --progress 50
./main plan progress --code "plan-db" --progress 25
```

---

## Code 格式规范

### ✅ 有效示例

```
plan-api-redesign
plan-auth-refactor
plan-database-migration
plan-mobile-app
```

### ❌ 无效示例

```
Plan_001        ❌ 含大写字母和下划线
plan_123        ❌ 含下划线
plan-           ❌ 末尾不是字母
-plan           ❌ 开头不是字母
ab              ❌ 少于 3 字符
plan.redesign   ❌ 含点号
```

---

## 常见错误处理

### 错误 1：Code 格式不符

```
错误：code 格式错误: 全小写字母，可含连字符，开头末尾必须是字母
```

**解决方案：**
- 检查是否全小写
- 将 `_` 替换为 `-`
- 确保首尾是字母
- 长度 >= 3 个字符

### 错误 2：Code 重复

```
错误：活跃状态中已存在相同的 code
```

**解决方案：**
- 执行 `./main plan list` 查看已存在的 code
- 使用更具体的名称
- 或完成/删除现有计划

### 错误 3：计划不存在

```
错误：计划不存在或已完成/取消
```

**解决方案：**
- 执行 `./main plan list` 确认 code
- 检查是否在正确的目录（项目/全局）
- 使用 `--global` 查看全局计划

### 错误 4：进度范围错误

```
错误：进度必须在 0-100 之间
```

**解决方案：**
- 只使用 0-100 的整数
- 0 = pending，1-99 = in_progress，100 = completed
