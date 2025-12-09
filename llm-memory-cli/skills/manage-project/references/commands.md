# Plan MCP 命令详解

管理长期项目计划的完整命令参考。

## 命令概览

| 命令 | 用途 | 使用频率 |
|-----|------|---------|
| `plan list` | 查看所有计划 | 高 |
| `plan create` | 创建新计划 | 中 |
| `plan get` | 获取计划详情 | 高 |
| `plan update` | 更新计划 | 高 |
| `plan delete` | 删除计划 | 低 |

## 1. plan list

列出所有项目计划。

```bash
llm-memory plan list
```

**输出示例：**
```
计划列表：
1. [plan-user-auth] 用户认证系统 (进度: 45%)
2. [plan-api-v2] API v2 重构 (进度: 80%)
3. [plan-db-migration] 数据库迁移 (进度: 10%)
```

**使用场景：**
- 开始工作前查看所有进行中的项目
- 定期检查项目整体进度
- 决定接下来优先处理哪个项目

## 2. plan create

创建新的项目计划。

```bash
llm-memory plan create \
  --code "plan-user-auth" \
  --title "用户认证系统" \
  --description "重构现有认证系统，添加 OAuth 支持" \
  --content "## 目标\n完成认证系统重构\n\n## 步骤\n1. 需求分析\n2. 设计方案\n3. 实现功能\n4. 测试验证"
```

**参数说明：**
- `--code`: 计划唯一标识，格式 `plan-<描述>`
- `--title`: 计划标题（必填）
- `--description`: 简短描述
- `--content`: 详细内容（支持 Markdown）

**创建后状态：**
- 进度自动设为 0
- 状态为"待开始"

## 3. plan get

获取计划的详细信息。

```bash
llm-memory plan get --code plan-user-auth
```

**输出示例：**
```
计划：用户认证系统
Code: plan-user-auth
进度: 45%
状态: 进行中

描述：
重构现有认证系统，添加 OAuth 支持

详细内容：
## 目标
完成认证系统重构

## 步骤
1. 需求分析 ✅
2. 设计方案 ✅
3. 实现功能 (进行中)
4. 测试验证

创建时间: 2025-12-01
更新时间: 2025-12-05
```

**使用场景：**
- 重新回顾项目目标和步骤
- 检查当前进度
- 继续之前未完成的工作

## 4. plan update

更新计划的内容或进度。

### 更新进度

```bash
llm-memory plan update --code plan-user-auth --progress 60
```

### 更新内容

```bash
llm-memory plan update \
  --code plan-user-auth \
  --content "## 目标\n完成认证系统重构\n\n## 步骤\n1. 需求分析 ✅\n2. 设计方案 ✅\n3. 实现功能 ✅\n4. 测试验证 (进行中)"
```

### 更新多个字段

```bash
llm-memory plan update \
  --code plan-user-auth \
  --progress 75 \
  --title "用户认证系统 v2.0" \
  --description "重构认证系统并添加 OAuth 2.0 支持"
```

**参数说明：**
- `--code`: 要更新的计划标识（必填）
- `--progress`: 进度（0-100）
- `--title`: 新标题
- `--description`: 新描述
- `--content`: 新的详细内容

**更新时机：**
- 完成一个关键步骤后
- 项目方向调整时
- 添加新的发现或决策时

## 5. plan delete

删除不再需要的计划。

```bash
llm-memory plan delete --code plan-user-auth
```

**确认提示：**
```
确认删除计划 "用户认证系统" (plan-user-auth)？
此操作不可恢复。[y/N]
```

**使用场景：**
- 项目已完成且不需要保留
- 计划被取消或废弃
- 创建了重复的计划

**注意：**
- 建议完成的项目保留一段时间再删除
- 重要项目考虑归档而不是删除

## 最佳实践

### Code 命名规范

```bash
# 好的示例
plan-user-auth          # 简洁明了
plan-api-v2-refactor   # 包含版本号
plan-db-migration      # 清晰的目标

# 不好的示例
plan-1                 # 无意义
plan-fix              # 太模糊
user-auth             # 缺少 plan- 前缀
```

### 进度更新建议

```
0%    - 刚创建，未开始
25%   - 完成需求分析和设计
50%   - 核心功能实现完成
75%   - 功能开发完成，进入测试
90%   - 测试完成，准备上线
100%  - 项目完全完成
```

### Content 结构建议

```markdown
## 目标
明确的项目目标

## 背景
为什么要做这个项目

## 步骤
1. 步骤一 ✅
2. 步骤二 (进行中)
3. 步骤三

## 关键决策
- 决策 1：选择方案 A 因为...
- 决策 2：...

## 风险和依赖
- 风险：...
- 依赖：需要等待 X 完成

## 资源
- 文档链接
- 相关代码
```

## 与其他工具配合

### 搭配 Todo

```bash
# 1. 创建项目计划
llm-memory plan create --code plan-feature-x --title "功能 X 开发"

# 2. 创建具体任务
llm-memory todo create --code todo-design-api --title "设计 API 接口"
llm-memory todo create --code todo-implement --title "实现功能"

# 3. 完成任务后更新计划进度
llm-memory plan update --code plan-feature-x --progress 50
```

### 搭配 Memory

```bash
# 1. 记录关键决策
llm-memory memory create \
  --code mem-auth-decision \
  --title "认证方案选择" \
  --content "选择 OAuth 2.0，因为..."

# 2. 在计划中引用
llm-memory plan update \
  --code plan-user-auth \
  --content "## 决策\n参考：mem-auth-decision"
```

## 常见问题

### Q1: 计划和任务有什么区别？

**计划 (Plan)**：
- 长期、复杂的项目
- 需要跟踪整体进度
- 时间跨度 >3 天

**任务 (Todo)**：
- 短期、具体的事项
- 关注完成状态
- 时间跨度 <3 天

### Q2: 什么时候应该更新进度？

建议在以下时机更新：
- 完成一个关键里程碑
- 每天工作结束时
- 项目方向发生重大变化

### Q3: 如何处理暂停的计划？

```bash
# 方法 1：在内容中标注
llm-memory plan update \
  --code plan-feature-x \
  --content "## 状态\n⏸️ 暂停中 - 等待产品需求确认"

# 方法 2：如果长期不用，可以删除
llm-memory plan delete --code plan-feature-x
```

### Q4: 计划完成后要删除吗？

建议：
- 保留 1-2 周，便于回顾和总结
- 提取重要经验到 Memory
- 然后再删除

## 相关资源

- [使用示例](./examples.md)
- [Memory 命令详解](../../record-knowledge/references/commands.md)
- [Todo 命令详解](../../manage-tasks/references/commands.md)
