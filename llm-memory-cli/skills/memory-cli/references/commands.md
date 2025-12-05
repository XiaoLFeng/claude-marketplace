# Memory 命令完全指南

## 概述

Memory 命令用于创建和管理项目知识库，记录架构决策、问题解决方案、代码片段和项目规范等关键信息。所有记忆默认存储在项目级别，使用 `--global` 标志创建全局记忆。

---

## 1. 创建记忆 (create)

### 语法

```bash
./main memory create \
  --code <code> \
  --title <title> \
  --content <content> \
  [--category <category>] \
  [--tags <tag1,tag2,tag3>] \
  [--global]
```

### 参数详解

| 参数 | 类型 | 必需 | 说明 |
|------|------|------|------|
| `--code` | string | ✅ | 记忆的唯一标识符，格式：全小写字母+连字符，开头末尾必须是字母，>=3字符 |
| `--title` | string | ✅ | 记忆的标题，简洁明了（建议 10-50 字） |
| `--content` | string | ✅ | 记忆的详细内容，支持 Markdown 格式 |
| `--category` | string | ❌ | 分类名称，如"架构设计"、"问题解决"、"代码片段"等 |
| `--tags` | string | ❌ | 标签列表，逗号分隔，用于快速分类（如：auth,jwt,security） |
| `--global` | flag | ❌ | 创建全局记忆，可在所有项目中访问 |

### 参数详解

#### Code 格式规则

```
✅ 规则：
- 全小写字母（a-z）
- 可包含连字符（-）
- 开头和末尾必须是字母
- 最少 3 个字符

✅ 有效示例：
mem-api-design
mem-security-notes
mem-redis-connection

❌ 无效示例：
mem_api_design      ❌ 含下划线
Mem-API-Design      ❌ 含大写字母
-memory-001         ❌ 开头不是字母
memory-             ❌ 末尾不是字母
m1                  ❌ 少于 3 个字符
```

#### 内容格式建议

使用 Markdown 格式增强可读性：

```markdown
# <知识点标题>

## 背景
<为什么需要这个知识点>

## 核心内容
<详细说明>

## 代码示例
```<language>
<code here>
```

## 参考链接
- [Link 1](URL)
- [Link 2](URL)

## 注意事项
- 注意点 1
- 注意点 2
```

### 示例

#### 示例 1：架构决策记录

```bash
./main memory create \
  --code "mem-jwt-choice" \
  --title "JWT 选型决策" \
  --content "# JWT 选型决策

## 为什么选择 JWT

1. **无状态**：不需要服务器端 session 存储
2. **易扩展**：支持水平扩展和微服务架构
3. **跨域支持**：天然支持跨域认证
4. **标准化**：行业标准，库支持完善

## 实现要点

- Access Token: 短期（15分钟）
- Refresh Token: 长期（7天）
- 密码使用 bcrypt 哈希（cost=12）

## 参考资料
- [JWT.io](https://jwt.io/)
- [OWASP 认证备忘单](https://cheatsheetseries.owasp.org/)" \
  --category "架构设计" \
  --tags "认证,jwt,安全"
```

#### 示例 2：Bug 解决方案

```bash
./main memory create \
  --code "mem-redis-connection-fix" \
  --title "Redis 连接失败排查" \
  --content "# Redis 连接失败排查方案

## 问题现象
生产环境 Redis 连接间歇性失败，导致 session 丢失

## 排查步骤

1. 检查连接池配置
2. 查看 Redis 服务器日志
3. 测试网络连接

## 解决方案
增加连接池大小，从 10 增加到 50
设置连接超时重试机制

## 代码修改
\`\`\`go
client := redis.NewClient(&redis.Options{
  Addr:         \"redis:6379\",
  MaxRetries:   3,
  PoolSize:     50,
  MinIdleConns: 10,
})
\`\`\`

## 验证结果
监控日志，连接失败率降低 95%
" \
  --category "问题解决" \
  --tags "redis,连接,troubleshooting"
```

### 错误处理

| 错误信息 | 原因 | 解决方案 |
|---------|------|---------|
| `code 格式错误: 全小写字母，可含连字符，开头末尾必须是字母` | Code 不符合格式规则 | 检查是否有大写字母、下划线、数字开头 |
| `活跃状态中已存在相同的 code` | Code 已被使用 | 使用更具体的命名或删除旧记录 |
| `标题不能为空` | 缺少 title 参数 | 提供有效的标题 |
| `内容不能为空` | 缺少 content 参数 | 提供详细的内容 |

---

## 2. 列出所有记忆 (list)

### 语法

```bash
./main memory list
```

### 说明

列出当前项目的所有活跃记忆（不包括已删除的）

### 输出格式

```
记忆列表 (项目作用域):
╭─────┬──────────────────────┬────────────────────┬──────────────┬──────────────────────╮
│ Code│ Title                │ Category           │ Tags         │ Created At           │
├─────┼──────────────────────┼────────────────────┼──────────────┼──────────────────────┤
│ mem-jwt-choice              │ JWT 选型决策        │ 架构设计      │ auth,jwt             │ 2024-01-15 10:30:00 │
│ mem-redis-connection-fix    │ Redis 连接失败排查  │ 问题解决      │ redis,troubleshooting│ 2024-01-16 14:45:30 │
│ mem-config-template         │ 常用配置模板        │ 代码片段      │ config,template      │ 2024-01-17 09:15:00 │
╰─────┴──────────────────────┴────────────────────┴──────────────┴──────────────────────╯

共 3 条记忆
```

### 示例

```bash
./main memory list
# 显示当前项目的所有记忆

./main memory list --global
# 显示全局记忆（如果实现了此功能）
```

---

## 3. 搜索记忆 (search)

### 语法

```bash
./main memory search --keyword <keyword>
```

### 参数详解

| 参数 | 类型 | 必需 | 说明 |
|------|------|------|------|
| `--keyword` | string | ✅ | 搜索关键词，在 title 和 content 中进行全文搜索 |

### 搜索范围

- **Title**: 精确匹配或部分匹配
- **Content**: 全文搜索
- **Tags**: 标签匹配
- **Category**: 分类匹配

### 输出格式

```
搜索结果 (关键词: "jwt"):
╭─────┬──────────────────────┬────────────────────┬──────────────┬──────────────────────╮
│ Code│ Title                │ Category           │ Tags         │ Created At           │
├─────┼──────────────────────┼────────────────────┼──────────────┼──────────────────────┤
│ mem-jwt-choice              │ JWT 选型决策        │ 架构设计      │ auth,jwt             │ 2024-01-15 10:30:00 │
│ mem-redis-jwt-integration   │ Redis 中的 JWT 验证 │ 集成方案      │ jwt,redis,auth       │ 2024-01-18 11:20:00 │
╰─────┴──────────────────────┴────────────────────┴──────────────┴──────────────────────╯

共找到 2 条记忆
```

### 示例

```bash
# 搜索 JWT 相关的记忆
./main memory search --keyword "jwt"

# 搜索 Redis 相关的记忆
./main memory search --keyword "redis"

# 搜索包含特定错误码的记忆
./main memory search --keyword "404"

# 搜索特定分类的记忆
./main memory search --keyword "架构设计"
```

### 高级用法

```bash
# 搜索多个关键词（取决于实现，通常是 OR 关系）
./main memory search --keyword "jwt redis"

# 搜索特定格式的内容
./main memory search --keyword "code:"  # 查找包含代码示例的记忆
./main memory search --keyword "@deprecated"  # 查找标记为过时的记忆
```

---

## 4. 获取记忆详情 (get)

### 语法

```bash
./main memory get --code <code>
```

### 参数详解

| 参数 | 类型 | 必需 | 说明 |
|------|------|------|------|
| `--code` | string | ✅ | 记忆的唯一标识符 |

### 输出格式

```
记忆详情:
═══════════════════════════════════════════════════════

Code:     mem-jwt-choice
Title:    JWT 选型决策
Category: 架构设计
Tags:     auth, jwt, security
Scope:    项目级别
Created:  2024-01-15 10:30:00
Updated:  2024-01-15 10:30:00

内容:
───────────────────────────────────────────────────────

# JWT 选型决策

## 为什么选择 JWT

1. **无状态**：不需要服务器端 session 存储
2. **易扩展**：支持水平扩展和微服务架构
...（完整内容）
```

### 示例

```bash
# 查看特定记忆
./main memory get --code "mem-jwt-choice"

# 查看 Redis 连接问题的解决方案
./main memory get --code "mem-redis-connection-fix"

# 查看配置模板
./main memory get --code "mem-config-template"
```

### 错误处理

| 错误信息 | 原因 | 解决方案 |
|---------|------|---------|
| `记忆不存在` | Code 拼写错误或不存在 | 执行 `list` 确认 code，或 `search` 查找 |
| `记忆已删除` | 记忆被删除后无法查看 | 检查 code，考虑恢复删除 |

---

## 5. 删除记忆 (delete)

### 语法

```bash
./main memory delete --code <code>
```

### 参数详解

| 参数 | 类型 | 必需 | 说明 |
|------|------|------|------|
| `--code` | string | ✅ | 记忆的唯一标识符 |

### 删除行为

- **软删除**：记忆在数据库中保留，但标记为已删除，不会显示在 list 中
- **不可恢复**：删除后无法通过 CLI 恢复，可能需要直接编辑数据库
- **立即生效**：删除后立即不可访问

### 示例

```bash
# 删除过时的记忆
./main memory delete --code "mem-old-deployment"

# 删除重复的记忆
./main memory delete --code "mem-duplicate-notes"

# 删除不再需要的记忆
./main memory delete --code "mem-experimental-config"
```

### 确认流程

```
⚠️ 警告：此操作不可撤销！
记忆内容将被永久删除。

确认删除 mem-jwt-choice？ (y/n): y

✅ 记忆 mem-jwt-choice 已删除
```

### 错误处理

| 错误信息 | 原因 | 解决方案 |
|---------|------|---------|
| `记忆不存在` | Code 拼写错误 | 确认 code，使用 `list` 查看 |
| `删除失败` | 权限问题或数据库错误 | 检查文件权限，尝试重启 CLI |

---

## Memory 使用场景详解

### 场景 1：架构决策记录

**何时创建**：做出重大技术决策时

**记录内容**：
- 为什么选择这个方案
- 备选方案有哪些
- 优缺点对比
- 实现要点
- 参考资料

**示例 Code**：`mem-jwt-choice`, `mem-postgres-over-mysql`

**查询频率**：低（决策做出后很少改变）

### 场景 2：Bug 解决方案

**何时创建**：遇到复杂 Bug 并成功解决时

**记录内容**：
- 问题现象
- 排查步骤
- 根本原因
- 解决方案
- 关键代码
- 预防措施

**示例 Code**：`mem-redis-connection-fix`, `mem-cors-issue`

**查询频率**：中等（可能在同类问题出现时参考）

### 场景 3：代码片段

**何时创建**：发现有用的代码模式或配置模板时

**记录内容**：
- 代码用途说明
- 完整代码示例
- 使用说明
- 注意事项
- 替代方案

**示例 Code**：`mem-auth-middleware`, `mem-db-connection-pool`

**查询频率**：高（新项目启动时经常参考）

### 场景 4：项目规范

**何时创建**：制定团队规范或发现最佳实践时

**记录内容**：
- 规范描述
- 遵循原因
- 示例代码
- 检查清单
- 例外情况

**示例 Code**：`mem-code-style-guide`, `mem-git-workflow`

**查询频率**：中等（新成员入职或代码审查时）

---

## 批量操作建议

虽然当前 Memory 命令不支持批量创建，但可以通过脚本实现：

```bash
#!/bin/bash
# 批量创建记忆的脚本示例

memories=(
  "mem-jwt-choice|JWT 选型决策|arch-design.md"
  "mem-redis-fix|Redis 连接问题|redis-fix.md"
  "mem-config-template|常用配置|templates/config.md"
)

for mem in "${memories[@]}"; do
  code=$(echo $mem | cut -d'|' -f1)
  title=$(echo $mem | cut -d'|' -f2)
  file=$(echo $mem | cut -d'|' -f3)

  if [ -f "$file" ]; then
    content=$(cat "$file")
    ./main memory create --code "$code" --title "$title" --content "$content"
  fi
done
```

---

## 最佳实践

### 1. Code 命名规范

```
✅ 推荐做法：
- 使用语义化名称：mem-api-design
- 简洁但清晰：mem-jwt-choice
- 体现内容类型：mem-fix-redis-timeout, mem-template-docker
- 按模块分类：mem-auth-jwt, mem-auth-session

❌ 避免做法：
- 过长：mem-this-is-a-very-long-memory-about-jwt
- 过短：m1, x
- 数字编号：mem-001, memory-123
- 无意义：mem-tmp, mem-test, mem-xxx
```

### 2. 标签使用

```
✅ 推荐做法：
- 3-5 个标签
- 使用英文小写：auth, jwt, security
- 标签反映关键词：redis, troubleshooting, performance
- 支持快速分类

❌ 避免做法：
- 标签过多：10+ 个标签
- 标签过少：1 个标签
- 模糊标签：stuff, things, info
```

### 3. 分类选择

```
推荐分类：
- 架构设计：重大技术决策
- 问题解决：Bug 排查和解决方案
- 代码片段：可复用的代码模式
- 项目规范：团队规范和最佳实践
- 安全：安全漏洞和防护措施
- 性能：性能优化经验
- 工具配置：各种工具配置方案
```

### 4. 内容组织

```
✅ 推荐做法：
- 使用 Markdown 格式化
- 包含代码示例
- 添加参考链接
- 使用清晰的标题层级
- 包含日期或版本信息

❌ 避免做法：
- 纯文本无格式
- 重复记录相似内容
- 缺少上下文信息
- 内容过长无小标题划分
```

---

## 总结

Memory 命令提供了强大的知识管理能力：

| 命令 | 用途 | 频率 |
|------|------|------|
| `create` | 创建新记忆 | 低 (有新发现时) |
| `list` | 查看所有记忆 | 中 (查看知识库时) |
| `search` | 查找记忆 | 高 (遇到问题时) |
| `get` | 查看详情 | 高 (需要具体方案时) |
| `delete` | 删除记忆 | 很低 (清理过时内容时) |

充分利用 Memory 系统，构建属于你的项目知识库！
