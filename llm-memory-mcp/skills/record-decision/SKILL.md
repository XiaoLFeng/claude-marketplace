---
name: record-decision
description: |
  决策记录器 (MCP版本) - 自动识别并记录架构决策到 memory。

  **何时调用此 Skill：**
  - 做出技术方案选型（框架/库/工具）
  - 排查复杂 Bug（>30分钟）
  - 制定项目规范和约定
  - 发现可复用代码模式
  - 用户说"记录这个决策"、"保存这个方案"

  **不调用此 Skill：**
  - 单纯查询信息（使用 search-history）
  - 记录普通笔记（使用 memory-mcp）
  - 临时信息记录（使用 todo-mcp）
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

### 方案 2: <名称>
- 优点：...
- 缺点：...

## 最终决策
<选择了什么方案>

## 理由
<为什么选择这个方案>

## 影响
<这个决策带来的影响>

## 参考资料
- <相关链接>
```

---

## 🔧 核心操作

### 自动识别决策场景

**决策模式识别**：

```javascript
// 决策场景关键词模式
const decisionPatterns = [
  // 技术选型
  /选择.*框架/,
  /决定使用/,
  /对比.*方案/,
  /采用.*技术/,
  /引入.*库/,

  // Bug 排查
  /排查.*bug/i,
  /解决了.*问题/,
  /修复.*原因/,
  /发现.*根因/,

  // 规范约定
  /制定.*规范/,
  /约定.*风格/,
  /统一.*标准/,

  // 代码模式
  /封装.*方法/,
  /抽象.*逻辑/,
  /可复用.*模式/
];

function isDecisionScenario(context) {
  return decisionPatterns.some(pattern => pattern.test(context));
}
```

### 提取决策信息

**信息提取逻辑**：

```javascript
function extractDecisionInfo(context) {
  return {
    title: extractTitle(context),
    background: extractBackground(context),
    options: extractOptions(context),
    decision: extractFinalDecision(context),
    reasoning: extractReasoning(context),
    impact: extractImpact(context),
    references: extractReferences(context),
    tags: extractTags(context)
  };
}

// 标题提取：从上下文中提取核心主题
function extractTitle(context) {
  // 例如："选择 JWT 作为认证方案" → "JWT 认证方案选型"
  // ...
}

// 标签提取：从内容中提取关键词
function extractTags(context) {
  // 例如：["auth", "jwt", "security", "architecture"]
  // ...
}
```

### 记录决策到 Memory

**操作流程**：

```javascript
async function recordDecision(context) {
  // 1. 检查是否为决策场景
  if (!isDecisionScenario(context)) return;

  // 2. 提取决策信息
  const info = extractDecisionInfo(context);

  // 3. 生成 Memory Code
  const code = generateDecisionCode(info.title);
  // 例如：mem-jwt-auth-decision

  // 4. 格式化内容
  const content = formatDecisionContent(info);

  // 5. 创建 Memory
  await memory_create({
    code: code,
    title: info.title,
    content: content,
    category: "架构决策",
    tags: info.tags,
    global: false  // 默认项目级别
  });
}

// 内容格式化
function formatDecisionContent(info) {
  return `
# ${info.title}

## 背景
${info.background}

## 候选方案
${info.options.map((opt, i) => `
### 方案 ${i + 1}: ${opt.name}
- 优点：${opt.pros.join(', ')}
- 缺点：${opt.cons.join(', ')}
`).join('\n')}

## 最终决策
${info.decision}

## 理由
${info.reasoning}

## 影响
${info.impact}

## 参考资料
${info.references.map(ref => `- [${ref.title}](${ref.url})`).join('\n')}
  `.trim();
}
```

**使用的 MCP 工具**：

```javascript
// 创建记忆
memory_create({
  code: "mem-xxx",
  title: "决策标题",
  content: "详细内容",
  category: "架构决策",
  tags: ["tag1", "tag2"],
  global: false
})
```

---

## 📊 完整示例

### 示例 1：技术方案选型

**场景**：选择 JWT vs Session 作为认证方案

**自动识别**：
```
用户讨论："我们需要选择认证方案，JWT 还是 Session？"
↓
record-decision 识别到"选择...方案"
↓
开始记录决策过程
```

**自动记录**：
```javascript
await memory_create({
  code: "mem-jwt-vs-session",
  title: "JWT vs Session 认证方案选型",
  content: `
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
- [JWT 最佳实践](https://...)
- [OAuth 2.0 规范](https://...)
  `,
  category: "架构决策",
  tags: ["auth", "jwt", "security", "architecture"],
  global: false
});
```

### 示例 2：复杂 Bug 排查

**场景**：排查内存泄漏问题（耗时 2 小时）

**自动识别**：
```
用户说："终于找到内存泄漏的原因了..."
↓
record-decision 识别到"找到...原因"
↓
开始记录排查过程
```

**自动记录**：
```javascript
await memory_create({
  code: "mem-memory-leak-fix",
  title: "内存泄漏问题排查与解决",
  content: `
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

## 参考资料
- [Node.js Memory Leak 排查指南](https://...)
  `,
  category: "问题排查",
  tags: ["bug", "memory-leak", "nodejs", "websocket"],
  global: false
});
```

---

## 🎯 使用场景

### 场景 1：技术选型自动记录

```
对话中进行技术方案对比
↓
record-decision 识别决策场景
↓
提取决策信息
↓
memory_create 记录
```

### 场景 2：Bug 排查自动记录

```
排查复杂 Bug（>30分钟）
↓
找到根因并解决
↓
record-decision 识别排查场景
↓
记录排查过程和解决方案
```

### 场景 3：手动触发记录

```
用户："记录这个决策"
↓
record-decision 识别意图
↓
提取当前上下文中的决策信息
↓
memory_create 记录
```

---

## 📚 MCP 工具清单

本 Skill 使用以下 MCP 工具：

- `memory_create` - 创建记忆（来自 memory-mcp）
- `memory_search` - 搜索记忆（来自 memory-mcp，检查是否已记录）

详见：[完整工具参考](./references/tools.md)

---

## 🔗 参考文档

- [完整工具参考](./references/tools.md) - MCP 工具详细说明
- [触发场景详解](./references/triggers.md) - 决策场景识别规则
- [使用示例](./references/examples.md) - 真实场景案例
- [Memory Skill](../memory-mcp/SKILL.md) - 记忆管理
- [Code 格式](../shared-references/code-format.md) - 格式规则详解
- [架构迁移](../shared-references/architecture-migration.md) - 从 workflow-orchestrator 迁移
