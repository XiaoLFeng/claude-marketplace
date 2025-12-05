# 常见错误和解决方案

## Code 格式错误

### 错误信息

```
code 格式错误: 全小写字母，可含连字符，开头末尾必须是字母
```

### 原因分析

使用了大写字母、下划线、数字开头等不符合规则的字符

### 解决方案

1. 检查 code 是否全小写
2. 将下划线（_）替换为连字符（-）
3. 确保开头和末尾是字母
4. 确保长度 >= 3

### 错误示例和修正

```
❌ test_plan_001 → ✅ test-plan-one
❌ Task-001      → ✅ task-one
❌ -my-task      → ✅ my-task
❌ task-         → ✅ task
❌ ab            → ✅ abc
❌ myTask        → ✅ my-task
❌ 123           → ✅ task-one-two-three
```

### 预防措施

- 命名时参考 [Code 格式规则](./code-format.md)
- 使用推荐的命名模式
- 创建前先用正则验证：`^[a-z][a-z-]*[a-z]$`

---

## Code 重复错误

### 错误信息

```
活跃状态中已存在相同的 code，请使用不同的标识码
```

### 原因分析

**Memory：**
- Code 全局唯一，整个系统中不能重复

**Plan/Todo：**
- Code 在活跃状态（pending 和 in_progress）中唯一
- 已完成或已取消的 code 可以重用

### 解决方案

**方案 1：查询现有 code**

```bash
# CLI
./main memory list
./main plan list
./main todo list

# MCP
memory_list({ scope: "all" })
plan_list({ scope: "all" })
todo_list({ scope: "all" })
```

**方案 2：使用更具体的命名**

```
❌ todo-fix-bug → ✅ todo-fix-login-bug
❌ mem-notes    → ✅ mem-api-design-notes
```

**方案 3：完成/删除旧记录**

```bash
# CLI
./main todo complete --code "old-code"
./main todo delete --code "old-code"

# MCP
todo_batch_complete({ codes: ["old-code"] })
```

### 预防措施

- 创建前先查询是否存在
- 使用语义化命名，避免泛泛的名称
- 定期清理已完成的记录

---

## 找不到记录

### 错误信息

```
记录不存在或无权限访问
计划不存在或已完成/取消
待办不存在
记忆不存在
```

### 原因分析

1. Code 拼写错误
2. 作用域不匹配（记录在其他路径/组）
3. 记录已删除
4. 记录已完成或取消（Plan/Todo）

### 解决方案

**步骤 1：确认 code 拼写**

```bash
# CLI
./main [memory/plan/todo] list

# MCP
memory_list({ scope: "all" })
plan_list({ scope: "all" })
todo_list({ scope: "all" })
```

**步骤 2：检查作用域**

**CLI：**
- 默认项目级别，切换目录后看不到
- 使用 `--global` 创建才能全局可见

**MCP：**
- personal - 当前目录私有
- group - 组内共享
- global - 全局可见（仅 Memory）

**步骤 3：检查记录状态**

- Plan/Todo 的已完成（completed）或已取消（cancelled）记录不显示在 list 中

### 预防措施

- 在项目根目录执行命令
- 重要的通用知识使用 `--global`（CLI）或 `global: true`（MCP）
- 创建时记录 code，避免拼写错误

---

## 批量操作部分失败

### 错误信息

```
批量创建部分完成! 成功 8 个，失败 2 个
失败详情:
- todo-001: code 格式错误
- todo-002: 活跃状态中已存在相同的 code
```

### 原因分析

- 部分 code 格式错误
- 部分 code 已存在
- 部分记录无权限操作

### 解决方案

**步骤 1：查看失败详情**

- 批量操作返回结果中包含 failures 列表
- 每个失败项都有 code 和 error 信息

**步骤 2：修正失败的项**

```json
// 原批量操作
[
  {"code":"todo-001","title":"任务1"},  // 失败：格式错误
  {"code":"todo-002","title":"任务2"},  // 失败：已存在
  {"code":"todo-003","title":"任务3"}   // 成功
]

// 修正后重新提交
[
  {"code":"todo-one","title":"任务1"},    // 修正格式
  {"code":"todo-002-new","title":"任务2"} // 修改 code
]
```

**步骤 3：重新提交失败的项**

- 只提交失败的项，不需要重复成功的

### 混合模式结果格式

**全成功：**
```
✅ 批量创建成功! 共处理 10 个待办事项
```

**全失败：**
```
❌ 批量创建失败! 共 10 个项目全部失败
失败详情:
- todo-1: code 格式错误
- todo-2: code 格式错误
...
```

**部分成功：**
```
⚠️ 批量创建部分完成! 成功 8 个，失败 2 个
失败详情:
- todo-1: code 格式错误
- todo-2: 活跃状态中已存在
```

### 预防措施

- 创建前验证 code 格式
- 查询是否已存在
- 分批处理（每批 100 个以内）
- 使用 JSON 文件，方便修改和重试

---

## CLI 特有问题

### Plan 创建失败（已修复）

**状态：** ✅ 已修复，CLI 已支持 `--content` 参数

**历史问题：**
```
错误信息: 计划内容不能为空
原因: CLI 存在 BUG，无法传递 content 参数
```

**当前使用方式：**
```bash
./main plan create \
  --code "my-plan" \
  --title "计划标题" \
  --description "计划描述" \
  --content "# 详细内容\n\n- 步骤1\n- 步骤2"
```

### 命令行参数问题

**问题：** 多行内容传递

**解决方案：**
```bash
# 方式 1：使用 \n
./main memory create \
  --code "mem-xxx" \
  --content "第一行\n第二行\n第三行"

# 方式 2：使用 heredoc
./main memory create \
  --code "mem-xxx" \
  --content "$(cat <<'EOF'
第一行
第二行
第三行
EOF
)"
```

---

## MCP 特有问题

### 作用域权限问题

**错误信息：**
```
无权限操作此记录
```

**原因：**
- 尝试操作其他路径/组的记录
- 记录的作用域与当前不匹配

**解决方案：**
1. 确认记录的作用域（personal/group/global）
2. 在正确的路径下操作
3. 或使用 group 功能共享

### 批量操作限制

**限制：** 最多 100 个项

**问题：** 超过 100 个项时无法一次处理

**解决方案：**
```javascript
// 分批处理
const items = [...]; // 500 个项
const batchSize = 100;

for (let i = 0; i < items.length; i += batchSize) {
  const batch = items.slice(i, i + batchSize);
  const result = await todo_batch_create({ items: batch });

  // 检查结果
  if (result.failure_count > 0) {
    console.log("失败项:", result.failures);
  }
}
```

---

## 调试技巧

### 1. 使用 list 命令排查

**列出所有记录：**
```bash
# CLI
./main memory list
./main plan list
./main todo list

# MCP
memory_list({ scope: "all" })
plan_list({ scope: "all" })
todo_list({ scope: "all" })
```

**过滤特定作用域：**
```javascript
// MCP
memory_list({ scope: "personal" })
memory_list({ scope: "group" })
memory_list({ scope: "global" })
```

### 2. 检查作用域

**CLI：**
- 检查当前工作目录
- 确认是否使用了 `--global`

**MCP：**
- 检查 scope 参数
- 确认 group 配置

### 3. 验证 code 格式

**在线正则测试：**
- 使用 https://regex101.com/
- 正则表达式：`^[a-z][a-z-]*[a-z]$`
- 测试你的 code 是否匹配

**本地验证：**
```bash
# 使用 grep
echo "your-code" | grep -E '^[a-z][a-z-]*[a-z]$'
# 匹配成功则输出 your-code，失败则无输出
```

### 4. 查看详细错误信息

**CLI：**
- 命令输出中包含详细错误信息

**MCP：**
- 工具返回结果中包含错误详情
- 批量操作的 failures 数组

### 5. 使用 get 命令查看完整信息

```bash
# CLI
./main memory get --code "mem-xxx"
./main plan get --code "plan-xxx"

# MCP
memory_get({ code: "mem-xxx" })
plan_get({ code: "plan-xxx" })
```

---

## 常见问题 FAQ

### Q1: 为什么切换目录后看不到之前的记录？

**A:** CLI 默认项目级别，使用 `--global` 创建全局记录。MCP 使用 scope 参数控制。

### Q2: 如何删除已完成的记录？

**A:** 已完成的 Plan/Todo 不显示在 list 中，但仍然存在。使用 TUI 或直接操作数据库删除。

### Q3: 批量操作可以超过 100 个吗？

**A:** 不可以，最多 100 个。需要分批处理。

### Q4: Memory 的 code 可以修改吗？

**A:** 不可以，code 是唯一标识，不能修改。需要删除后重新创建。

### Q5: Plan/Todo 的已完成记录可以重用 code 吗？

**A:** 可以，活跃状态唯一指的是 pending 和 in_progress。

### Q6: 如何实现多项目管理？

**A:** 使用 Group 功能（MCP）或在 code 中添加项目前缀。

### Q7: 优先级可以修改吗？

**A:** 可以，使用 update 命令（Memory）或 batch-update（Todo）。

### Q8: 搜索支持正则表达式吗？

**A:** 不支持，只支持关键词模糊搜索。

### Q9: 可以导出所有记录吗？

**A:** 使用 list 命令查看，或直接访问数据库文件。

### Q10: 如何备份数据？

**A:** 复制 SQLite 数据库文件（默认在 ~/.llm-memory/）。

---

## 获取帮助

### 文档资源

- [Code 格式规则](./code-format.md)
- [优先级判断指南](./priority-guide.md)
- [最佳实践](./best-practices.md)

### 社区支持

- GitHub Issues
- 技术论坛
- 开发者文档

### 提交 Bug

- 提供完整的错误信息
- 包含复现步骤
- 说明使用环境（CLI/MCP、系统版本）
