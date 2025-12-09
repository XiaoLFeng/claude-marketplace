# Todo 状态说明

## 状态值

| 状态 | 说明 |
|------|------|
| pending | 待处理，任务已创建但未开始 |
| in_progress | 进行中，Agent 正在处理 |
| completed | 已完成，任务成功完成 |
| cancelled | 已取消，任务不再需要 |

## 完成任务的时机

### 应该完成

- ✅ 代码编写完成并测试通过
- ✅ Bug 修复完成
- ✅ 文档更新完成
- ✅ 任务目标已达成

### 不应该完成

- ❌ 代码还未写完
- ❌ 测试未通过
- ❌ 遇到阻塞未解决
- ❌ 任务需要取消（应使用 cancel）

## 部分完成的处理

```
1. 完成能做的部分
    ↓
2. 调用 task-add 创建后续任务
    ↓
3. llm-memory todo done 标记当前任务完成
    ↓
4. 在返回结果中说明:
   - 已完成的部分
   - 未完成的原因
   - 新创建的后续任务
```

## CLI 命令

```bash
# 完成单个任务
llm-memory todo done todo-auth-login

# 批量完成
llm-memory todo done todo-a todo-b todo-c

# 查看任务状态
llm-memory todo list
```
