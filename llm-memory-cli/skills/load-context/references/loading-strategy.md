# 上下文加载策略

## 加载级别

| 级别 | 场景 | 操作 |
|------|------|------|
| 快速 | 新对话开始 | 只列表 |
| 深度 | Plan 模式 | 列表 + 详情 + 搜索 |

## 快速加载流程

```
新对话开始
    ↓
llm-memory plan list
    ↓
llm-memory todo list
    ↓
简要展示概况
```

## 深度加载流程

```
进入 Plan 模式
    ↓
llm-memory plan list
    ↓
llm-memory todo list
    ↓
llm-memory memory search "关键词"
    ↓
llm-memory plan show xxx (关键计划)
    ↓
详细展示上下文
```

## CLI 命令汇总

```bash
# 列出计划
llm-memory plan list

# 列出任务
llm-memory todo list

# 搜索记忆
llm-memory memory search "keyword"

# 计划详情
llm-memory plan show plan-xxx

# 记忆详情
llm-memory memory show mem-xxx
```

## 输出格式

### 概览格式

```
📊 项目状态概览

📋 计划: X 进行中, Y 待开始
📝 任务: X 待处理, Y 进行中
💡 记忆: X 条相关
```

### 详细格式

```
═══════════════════════════════════════
📋 [plan-code] 计划标题 (进度%)
├── 目标: ...
├── Agent: ...
═══════════════════════════════════════
```
