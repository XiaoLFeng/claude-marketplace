# Task Agent Prompt 模板

## 标准模板

```markdown
你负责完成以下任务：

**任务**: {任务标题}
**Todo Code**: {todo-code}
**负责文件**: {文件列表}

## 完成要求
{具体要求列表}

## 重要指令

### 开始任务
在开始工作前，调用 `task-start` skill：
- 参数: {todo-code}
- 这会标记任务为"进行中"，避免其他 Agent 重复处理

### 完成任务
完成工作后，调用 `task-complete` skill：
- 参数: {todo-code}
- 这会标记任务为"已完成"

### 追加任务
如果发现需要额外工作，调用 `task-add` skill：
- 提供新任务的 code 和描述
- 在 description 中标注 [Task-X] 由谁负责

### 冲突处理
如果遇到文件被其他 Agent 修改导致无法编辑：
1. 跳过该文件的修改
2. 在返回结果中说明: "文件 xxx 需要后续处理"
3. 继续完成其他可完成的工作
4. 仍然调用 task-complete 标记任务完成

## 注意事项
- 只修改你负责的文件: {文件列表}
- 不要修改其他 Agent 负责的文件
- 保持代码风格一致
```

## 简化模板（简单任务）

```markdown
**任务**: {任务标题}
**Todo**: {todo-code}
**文件**: {文件}

完成后调用 `task-complete` skill，参数: {todo-code}
```

## 复杂任务模板

```markdown
你负责完成以下复杂任务：

**任务**: {任务标题}
**Todo Code**: {todo-code}
**预计涉及文件**:
- {文件1} - {用途}
- {文件2} - {用途}

## 背景信息
{相关上下文}

## 详细要求
1. {要求1}
2. {要求2}
3. {要求3}

## 技术约束
- {约束1}
- {约束2}

## 验收标准
- [ ] {标准1}
- [ ] {标准2}

## Skill 调用指令

1. **开始时**: 调用 `task-start`，参数: {todo-code}
2. **发现新任务时**: 调用 `task-add`，创建新 todo
3. **完成时**: 调用 `task-complete`，参数: {todo-code}

## 冲突处理
遇到文件编辑冲突时：
- 跳过冲突文件
- 返回结果中说明需要后续处理
- 仍然标记任务完成
```

## 并行任务启动示例

主 Agent 启动多个 Task Agent：

```javascript
// 并行启动 Task Agent A 和 B
Promise.all([
  launchTaskAgent({
    prompt: `
      **任务**: 实现登录功能
      **Todo**: todo-auth-login
      **文件**: src/auth/login.ts

      完成要求:
      1. 实现登录 API
      2. 添加表单验证

      开始前调用 task-start(todo-auth-login)
      完成后调用 task-complete(todo-auth-login)
    `
  }),
  launchTaskAgent({
    prompt: `
      **任务**: 实现注册功能
      **Todo**: todo-auth-register
      **文件**: src/auth/register.ts

      完成要求:
      1. 实现注册 API
      2. 添加邮箱验证

      开始前调用 task-start(todo-auth-register)
      完成后调用 task-complete(todo-auth-register)
    `
  })
]);
```

## 返回结果模板

Task Agent 返回给主 Agent 的结果格式：

```markdown
## 任务完成报告

**Todo**: {todo-code}
**状态**: 已完成 / 部分完成

### 完成内容
- {完成项1}
- {完成项2}

### 修改的文件
- {文件1}: {修改说明}
- {文件2}: {修改说明}

### 需要后续处理（如有）
- {文件x}: 与其他 Agent 冲突，需要手动合并
- {新发现的任务}: 已通过 task-add 创建

### 备注
{其他说明}
```
