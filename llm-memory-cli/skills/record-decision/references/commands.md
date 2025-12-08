# Record Decision - CLI 命令参考

## 使用的 CLI 命令

### llm-memory memory create

创建记忆条目。

**语法**：

```bash
llm-memory memory create <code> --title <title> --content <content> [options]
```

**参数**：

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| code | string | ✅ | 记忆唯一标识码 |
| --title | string | ✅ | 记忆标题 |
| --content | string | ✅ | 详细内容（支持 Markdown） |
| --category | string | ❌ | 分类（默认 "默认"） |
| --tags | string | ❌ | 标签列表（逗号分隔） |
| --priority | number | ❌ | 优先级 1-4（默认 2） |
| --global | flag | ❌ | 是否全局可见 |

**示例**：

```bash
# 基本创建
llm-memory memory create mem-jwt-decision \
  --title "JWT 认证方案选型" \
  --content "选择 JWT + Refresh Token 方案..."

# 完整参数
llm-memory memory create mem-jwt-decision \
  --title "JWT 认证方案选型" \
  --content "$(cat decision.md)" \
  --category "架构决策" \
  --tags "auth,jwt,security" \
  --priority 3

# 全局可见
llm-memory memory create mem-api-convention \
  --title "API 响应格式规范" \
  --content "..." \
  --global
```

**输出**：

```
✅ 记忆创建成功

Code: mem-jwt-decision
标题: JWT 认证方案选型
分类: 架构决策
标签: auth, jwt, security
优先级: 3 (高)
作用域: 项目
```

---

### llm-memory memory search

搜索记忆（检查是否已记录）。

**语法**：

```bash
llm-memory memory search <keyword> [--scope <scope>]
```

**参数**：

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| keyword | string | ✅ | 搜索关键词 |
| --scope | string | ❌ | 作用域：personal/group/global/all |

**示例**：

```bash
# 检查是否已有类似记录
llm-memory memory search "JWT 认证"
```

**输出**：

```
🔍 搜索结果：

[mem-jwt-decision] JWT 认证方案选型
  分类: 架构决策 | 标签: auth, jwt
  创建: 2024-12-01

未找到更多结果。
```

---

### llm-memory memory get

获取记忆详情。

**语法**：

```bash
llm-memory memory get <code>
```

**示例**：

```bash
llm-memory memory get mem-jwt-decision
```

**输出**：

```
💡 记忆详情：

Code: mem-jwt-decision
标题: JWT 认证方案选型
分类: 架构决策
标签: auth, jwt, security
优先级: 3 (高)
创建时间: 2024-12-01 10:00:00
更新时间: 2024-12-01 10:00:00

内容:
---
# JWT vs Session 认证方案选型

## 背景
需要选择合适的认证方案...

## 最终决策
选择 JWT + Refresh Token
---
```

---

## 内容模板

### 技术选型模板

```bash
llm-memory memory create mem-tech-selection \
  --title "<技术选型标题>" \
  --category "架构决策" \
  --tags "architecture,<技术标签>" \
  --priority 3 \
  --content "$(cat <<'EOF'
# <技术选型标题>

## 背景
<为什么需要做这个选型>

## 候选方案

### 方案 1: <名称>
- **优点**：
  - ...
- **缺点**：
  - ...

### 方案 2: <名称>
- **优点**：
  - ...
- **缺点**：
  - ...

## 最终决策
<选择了什么方案>

## 理由
<为什么选择这个方案>

## 影响
<这个决策带来的影响>
EOF
)"
```

### Bug 排查模板

```bash
llm-memory memory create mem-bug-fix \
  --title "<问题标题>" \
  --category "问题排查" \
  --tags "bug,<相关标签>" \
  --priority 4 \
  --content "$(cat <<'EOF'
# <问题标题>

## 背景
<问题现象和影响>

## 排查过程
1. <第一步>
2. <第二步>
3. ...

## 根本原因
<问题的根本原因>

## 解决方案
<如何解决的>

## 预防措施
<如何避免再次发生>

## 相关代码
<代码片段或文件路径>
EOF
)"
```

### 设计规范模板

```bash
llm-memory memory create mem-convention \
  --title "<规范标题>" \
  --category "设计规范" \
  --tags "convention,<相关标签>" \
  --priority 3 \
  --global \
  --content "$(cat <<'EOF'
# <规范标题>

## 规范内容
<详细规范说明>

## 示例
<正确和错误的示例>

## 适用范围
<在什么情况下使用>

## 例外情况
<什么情况下可以不遵守>
EOF
)"
```

---

## Code 命名规则

### 格式

```
mem-<主题>[-<补充信息>]
```

### 示例

| 场景 | Code 示例 |
|------|----------|
| 技术选型 | `mem-jwt-decision`, `mem-redis-vs-memcached` |
| Bug 修复 | `mem-memory-leak-fix`, `mem-api-timeout-solution` |
| 设计规范 | `mem-api-convention`, `mem-error-handling` |
| 最佳实践 | `mem-caching-strategy`, `mem-logging-pattern` |

---

## 分类和标签推荐

### 推荐分类

| 分类 | 说明 | 示例场景 |
|------|------|---------:|
| 架构决策 | 技术选型、架构设计 | 选择框架、数据库 |
| 问题排查 | Bug 修复、故障处理 | 内存泄漏、性能问题 |
| 设计规范 | 代码规范、API 设计 | 命名约定、接口规范 |
| 最佳实践 | 可复用模式、优化技巧 | 缓存策略、错误处理 |

### 推荐标签

```bash
# 技术领域
auth, api, database, security, performance

# 决策类型
architecture, design, bugfix, optimization

# 技术栈
nodejs, react, postgresql, redis, jwt
```

---

## 参考链接

- [Memory CLI 完整文档](../../memory-cli/references/commands.md)
- [Code 格式规范](../../shared-references/code-format.md)
