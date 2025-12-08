---
name: record-decision
description: |
  决策记录器 (CLI版本) - 自动识别并记录架构决策到 memory。

  **何时调用此 Skill：**
  - 做出技术方案选型（框架/库/工具）
  - 排查复杂 Bug（>30分钟）
  - 制定项目规范和约定
  - 发现可复用代码模式
  - 用户说"记录这个决策"、"保存这个方案"

  **不调用此 Skill：**
  - 单纯查询信息（使用 search-history）
  - 记录普通笔记（使用 memory-cli）
  - 临时信息记录（使用 todo-cli）
---

# Record Decision Skill

自动识别并记录架构决策到 memory，确保重要决策不会丢失。

## ⚡ 快速参考

### 触发场景

```
自动识别场景：
  - 技术方案选型（框架/库/工具）
  - 复杂 Bug 排查（>30分钟）
  - 可复用代码模式
  - 项目规范和约定

触发关键词：
  - "选择..."、"决定使用..."
  - "对比..."、"方案..."
  - "解决了..."、"排查完..."
  - "规范..."、"约定..."
```

### Memory Code 规则

```
格式：mem-<主题>[-<日期>]

示例：
  mem-jwt-decision           # 技术选型
  mem-auth-bug-fix           # Bug 解决方案
  mem-api-design-convention  # 设计规范
  mem-error-handling-pattern # 代码模式
```

### 内容格式模板

```markdown
# <决策标题>

## 背景
<为什么需要做这个决策>

## 候选方案
### 方案 1: <名称>
- 优点：...
- 缺点：...

## 最终决策
<选择了什么方案>

## 理由
<为什么选择这个方案>

## 影响
<这个决策带来的影响>
```

---

## 🔧 核心操作

### 记录决策到 Memory

**CLI 命令**：

```bash
./main memory create \
  --code "mem-xxx" \
  --title "决策标题" \
  --content "详细内容" \
  --category "架构决策" \
  --tags "tag1,tag2"
```

### 决策记录脚本

```bash
#!/bin/bash
# record-decision.sh

CODE=$1
TITLE=$2
CONTENT_FILE=$3  # Markdown 文件路径

# 读取内容文件
CONTENT=$(cat "$CONTENT_FILE")

# 创建 Memory
./main memory create \
  --code "$CODE" \
  --title "$TITLE" \
  --content "$CONTENT" \
  --category "架构决策" \
  --tags "decision,architecture"

echo "Decision recorded: $CODE"
```

---

## 📊 完整示例

### 示例 1：技术方案选型

**创建决策内容文件**：

```bash
cat > /tmp/jwt-decision.md << 'EOF'
# JWT vs Session 认证方案选型

## 背景
需要为新的用户认证系统选择合适的认证方案。

## 候选方案

### 方案 1: JWT (JSON Web Token)
- 优点：无状态、易扩展、跨域友好、移动端友好
- 缺点：无法主动失效、Token 体积较大

### 方案 2: Session
- 优点：可主动失效、安全性高
- 缺点：需要存储、扩展困难、跨域麻烦

## 最终决策
选择 JWT + Refresh Token 方案

## 理由
1. 系统需要支持水平扩展
2. 需要支持移动端和 Web 端
3. 使用 Refresh Token 解决无法主动失效的问题

## 影响
- 需要实现 Token 黑名单机制
- 需要设计 Refresh Token 存储方案

## 参考资料
- JWT 最佳实践
- OAuth 2.0 规范
EOF
```

**记录决策**：

```bash
./main memory create \
  --code "mem-jwt-vs-session" \
  --title "JWT vs Session 认证方案选型" \
  --content "$(cat /tmp/jwt-decision.md)" \
  --category "架构决策" \
  --tags "auth,jwt,security,architecture"
```

### 示例 2：Bug 排查记录

**创建排查记录文件**：

```bash
cat > /tmp/memory-leak-fix.md << 'EOF'
# 内存泄漏问题排查与解决

## 背景
生产环境出现内存持续增长，最终导致 OOM 崩溃。

## 排查过程
1. 使用 heapdump 获取内存快照
2. 对比多个快照发现 EventEmitter 监听器持续增长
3. 定位到 WebSocket 连接未正确清理

## 根本原因
WebSocket 断开重连时，旧的事件监听器没有被移除。

## 解决方案
在 disconnect 事件中调用 removeAllListeners()

## 预防措施
- 添加监听器数量监控
- 代码 Review 检查点增加事件监听清理
EOF
```

**记录排查过程**：

```bash
./main memory create \
  --code "mem-memory-leak-fix" \
  --title "内存泄漏问题排查与解决" \
  --content "$(cat /tmp/memory-leak-fix.md)" \
  --category "问题排查" \
  --tags "bug,memory-leak,nodejs,websocket"
```

---

## 🎯 使用场景

### 场景 1：技术选型记录

```bash
# 创建决策内容
vim /tmp/decision.md

# 记录决策
./main memory create \
  --code "mem-framework-selection" \
  --title "前端框架选型" \
  --content "$(cat /tmp/decision.md)" \
  --category "架构决策"
```

### 场景 2：Bug 排查记录

```bash
# 排查完成后记录
./main memory create \
  --code "mem-api-timeout-fix" \
  --title "API 超时问题排查" \
  --content "..." \
  --category "问题排查"
```

### 场景 3：使用脚本快速记录

```bash
# 使用封装好的脚本
./record-decision.sh "mem-cache-strategy" "缓存策略设计" /tmp/cache.md
```

---

## 📚 CLI 命令清单

本 Skill 使用以下 CLI 命令：

- `./main memory create` - 创建记忆（来自 memory-cli）
- `./main memory search` - 搜索记忆（来自 memory-cli，检查是否已记录）

详见：[完整命令参考](./references/commands.md)

---

## 🔗 参考文档

- [完整命令参考](./references/commands.md) - CLI 命令详细说明
- [触发场景详解](./references/triggers.md) - 决策场景识别规则
- [使用示例](./references/examples.md) - 真实场景案例
- [Memory Skill](../memory-cli/SKILL.md) - 记忆管理
- [Code 格式](../shared-references/code-format.md) - 格式规则详解
- [架构迁移](../shared-references/architecture-migration.md) - 从 workflow-orchestrator 迁移
