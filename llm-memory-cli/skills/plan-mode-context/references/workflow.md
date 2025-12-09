# Plan Mode Context Workflow Reference

本文档详细说明 Plan 模式下上下文加载的完整工作流程。

## 工作流程图

```
进入 Plan 模式
    ↓
Step 1: 加载现有计划
    ├─ llm-memory plan list
    └─ llm-memory plan get --code xxx
    ↓
Step 2: 加载待办任务
    └─ llm-memory todo list
    ↓
Step 3: 搜索相关记忆
    ├─ llm-memory memory search --keyword xxx
    └─ llm-memory memory get --code xxx
    ↓
Step 4: 汇总展示
    └─ 输出上下文摘要
```

## 详细命令说明

### 1. 计划管理

#### 列出所有计划

```bash
llm-memory plan list
```

**输出示例：**
```json
[
  {
    "code": "plan-user-auth",
    "title": "用户认证系统重构",
    "status": "in_progress",
    "progress": 45,
    "created_at": "2025-12-01T10:00:00Z"
  }
]
```

#### 获取计划详情

```bash
llm-memory plan get --code plan-user-auth
```

**输出示例：**
```json
{
  "code": "plan-user-auth",
  "title": "用户认证系统重构",
  "description": "将现有认证系统从 Session 迁移到 JWT",
  "steps": [
    {
      "step": 1,
      "description": "调研 JWT 库",
      "status": "completed"
    },
    {
      "step": 2,
      "description": "实现 JWT 中间件",
      "status": "in_progress"
    }
  ],
  "status": "in_progress",
  "progress": 45
}
```

### 2. 待办管理

#### 列出所有待办

```bash
llm-memory todo list
```

**输出示例：**
```json
[
  {
    "code": "todo-fix-login",
    "title": "修复登录 Bug",
    "description": "用户在特定条件下登录失败",
    "priority": 4,
    "status": "pending",
    "created_at": "2025-12-08T15:30:00Z"
  }
]
```

### 3. 记忆管理

#### 搜索记忆

```bash
llm-memory memory search --keyword "JWT"
```

**输出示例：**
```json
[
  {
    "code": "mem-jwt-decision",
    "title": "JWT vs Session 技术选型",
    "type": "decision",
    "relevance": 0.95,
    "created_at": "2025-11-28T09:00:00Z"
  }
]
```

#### 获取记忆详情

```bash
llm-memory memory get --code mem-jwt-decision
```

**输出示例：**
```json
{
  "code": "mem-jwt-decision",
  "title": "JWT vs Session 技术选型",
  "type": "decision",
  "content": "经过评估，决定采用 JWT 方案...",
  "context": {
    "problem": "需要支持移动端和 Web 端统一认证",
    "solution": "使用 JWT + Refresh Token 方案",
    "reasons": ["无状态", "跨域友好", "移动端友好"]
  },
  "tags": ["authentication", "jwt", "architecture"],
  "created_at": "2025-11-28T09:00:00Z"
}
```

## 上下文汇总模板

加载完所有信息后，应该按照以下格式汇总展示：

```markdown
📋 当前项目上下文

## 🎯 进行中的计划 (X个)

1. **[plan-xxx]** 计划标题
   - 状态: in_progress
   - 进度: XX%
   - 描述: ...

2. ...

## ✅ 待完成的任务 (X个)

🔴 **高优先级**
- [todo-xxx] 任务标题 (优先级: 4-5)

🟡 **中优先级**
- [todo-xxx] 任务标题 (优先级: 2-3)

⚪ **低优先级**
- [todo-xxx] 任务标题 (优先级: 0-1)

## 📚 相关记忆 (X条)

### 决策记录
1. **[mem-xxx]** 记忆标题
   - 类型: decision
   - 相关性: 0.XX
   - 简述: ...

### 问题排查
2. **[mem-xxx]** 记忆标题
   - 类型: troubleshooting
   - 相关性: 0.XX
   - 简述: ...

### 技术经验
3. **[mem-xxx]** 记忆标题
   - 类型: knowledge
   - 相关性: 0.XX
   - 简述: ...

## 💡 建议

基于以上上下文，建议接下来关注：
1. ...
2. ...
```

## 错误处理

### 命令执行失败

如果某个命令执行失败，应该：
1. 记录错误信息
2. 继续执行后续步骤
3. 在最终汇总时标注哪些信息未能加载

### 空数据处理

如果某个类别没有数据：
- 计划为空 → 显示 "暂无进行中的计划"
- 待办为空 → 显示 "暂无待办任务"
- 记忆为空 → 显示 "暂无相关记忆"

## 使用场景

### 场景 1: 新项目启动

用户进入 Plan 模式准备规划新功能：
1. 加载上下文 → 发现没有相关计划
2. 搜索记忆 → 找到之前类似功能的技术决策
3. 在制定计划时参考历史记忆

### 场景 2: 继续未完成工作

用户进入 Plan 模式继续之前的工作：
1. 加载上下文 → 发现有进行中的计划
2. 查看待办 → 发现有未完成的任务
3. 制定接下来的工作计划，避免遗漏

### 场景 3: 跨对话协作

多次对话中处理同一个项目：
1. 第一次对话 → 记录决策和计划
2. 第二次对话 → 进入 Plan 模式自动加载上下文
3. 无需用户重复说明，直接基于历史信息继续工作

## 最佳实践

1. **优先级排序**
   - 优先展示高优先级待办
   - 优先展示进行中的计划
   - 按相关性排序记忆

2. **信息过滤**
   - 如果计划/待办/记忆过多，只展示最相关的前 N 条
   - 提供完整列表的访问方式

3. **上下文关联**
   - 在汇总时建立计划、待办、记忆之间的关联
   - 例如：某个待办属于哪个计划，某个记忆对应哪个决策

4. **建议生成**
   - 基于加载的上下文，主动给出接下来的工作建议
   - 提醒用户注意高优先级任务或即将逾期的任务
