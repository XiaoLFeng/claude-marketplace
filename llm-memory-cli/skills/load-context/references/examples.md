# Load Context - 使用示例

## 场景 1：新对话开始

### 用户请求

```
"帮我继续完成认证系统的开发"
```

### 执行命令

```bash
# 1. 搜索相关记忆
llm-memory memory search "认证" --scope all

# 2. 列出活跃计划
llm-memory plan list --scope all

# 3. 列出待办事项
llm-memory todo list --scope all
```

### 输出结果

```
🔍 记忆搜索结果：

[mem-jwt-decision] JWT 认证方案选型
  分类: 架构决策 | 标签: auth, jwt
  创建: 2024-12-01

---

📋 计划列表：

⏳ [plan-auth-refactor] 用户认证系统重构
   进度: 45% | 状态: 进行中

---

✅ 待办列表：

🔴 ⏳ [todo-auth-refactor-2-1] 实现 JWT 生成逻辑
   优先级: 紧急 | 状态: 进行中

🟠 ⏸️ [todo-auth-refactor-2-2] 实现 Token 刷新机制
   优先级: 高 | 状态: 待处理
```

### 整理后的上下文

```
📥 已加载上下文：

## 历史决策
- JWT 认证方案选型：选择 JWT + Refresh Token 方案

## 当前计划
- 用户认证系统重构（进度 45%）

## 待办事项
- 🔴 实现 JWT 生成逻辑（进行中）
- 🟠 实现 Token 刷新机制（待处理）

可以继续从 "实现 JWT 生成逻辑" 开始工作。
```

---

## 场景 2：项目切换

### 用户请求

```
"我要开始处理性能优化相关的工作"
```

### 执行命令

```bash
llm-memory memory search "性能" --scope all
llm-memory plan list --scope all
llm-memory todo list --scope all
```

### 输出结果

```
🔍 记忆搜索结果：

[mem-query-optimization] 查询优化最佳实践
  分类: 最佳实践 | 标签: database, performance
  创建: 2024-11-15

[mem-caching-strategy] 缓存策略设计
  分类: 架构决策 | 标签: cache, redis
  创建: 2024-11-20

---

📋 计划列表：

✅ [plan-db-optimization] 数据库性能优化
   进度: 100% | 状态: 已完成

⏳ [plan-api-optimization] API 性能优化
   进度: 20% | 状态: 进行中

---

✅ 待办列表：

🟠 ⏳ [todo-api-opt-1-1] 分析慢接口
   优先级: 高 | 状态: 进行中

🟡 ⏸️ [todo-api-opt-1-2] 添加响应缓存
   优先级: 中 | 状态: 待处理
```

---

## 场景 3：无相关上下文

### 用户请求

```
"帮我实现一个新的通知系统"
```

### 执行命令

```bash
llm-memory memory search "通知" --scope all
```

### 输出结果

```
🔍 记忆搜索结果：

未找到相关记录。
```

### 响应

```
📥 上下文加载完成：

未找到与"通知系统"相关的历史记录。
这是一个全新的任务，建议：
1. 先讨论通知系统的需求和设计
2. 创建对应的计划来跟踪进度
```

---

## 场景 4：批量上下文加载

### 用户请求

```
"我需要了解整个项目的当前状态"
```

### 执行脚本

```bash
#!/bin/bash
# full-context.sh

echo "📊 项目状态概览"
echo "================"
echo ""

echo "📋 所有计划："
llm-memory plan list --scope all
echo ""

echo "✅ 待办事项："
llm-memory todo list --scope all
echo ""

echo "💡 最近记忆（前 10 条）："
llm-memory memory list --scope all --limit 10
```

### 输出结果

```
📊 项目状态概览
================

📋 所有计划：
⏳ [plan-auth-refactor] 用户认证系统重构 - 45%
⏳ [plan-api-optimization] API 性能优化 - 20%
✅ [plan-db-optimization] 数据库性能优化 - 100%

✅ 待办事项：
🔴 ⏳ 实现 JWT 生成逻辑
🟠 ⏸️ 实现 Token 刷新机制
🟠 ⏳ 分析慢接口
🟡 ⏸️ 添加响应缓存

💡 最近记忆（前 10 条）：
- JWT 认证方案选型
- 查询优化最佳实践
- 缓存策略设计
- API 响应格式规范
- ...
```

---

## 最佳实践

### 1. 关键词选择

```bash
# 好的关键词 - 具体明确
llm-memory memory search "JWT 认证"
llm-memory memory search "WebSocket 内存泄漏"

# 不好的关键词 - 太宽泛
llm-memory memory search "系统"
llm-memory memory search "问题"
```

### 2. 作用域选择

```bash
# 查看所有相关内容
llm-memory memory search "认证" --scope all

# 只看当前项目
llm-memory memory search "认证" --scope personal

# 只看团队共享
llm-memory memory search "认证" --scope group
```

### 3. 结果整理

加载上下文后，应该整理成结构化的摘要：

```markdown
## 上下文摘要

### 相关决策
- [决策1]：简要说明
- [决策2]：简要说明

### 进行中的工作
- [计划/待办]：当前状态

### 建议行动
- 建议从哪里开始
- 需要注意的事项
```
