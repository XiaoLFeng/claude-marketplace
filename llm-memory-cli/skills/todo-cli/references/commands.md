# Todo CLI 命令完整参考

## 基础命令

### 1. 创建待办 (create)

#### 语法
```bash
./llm-memory todo create \
  --code <code> \
  --title <title> \
  [--description <description>] \
  [--priority <1-4>] \
  [--global]
```

#### 参数详解
- `--code`: 待办标识码（必填）
  - 格式：全小写 + 连字符，字母开头/结尾，≥3 字符
  - 推荐格式：`todo-<动作>-<对象>`
  - 示例：`todo-fix-login-bug`, `todo-update-docs`

- `--title`: 任务标题（必填）
  - 简明扼要，≤100 字符
  - 示例：`修复登录失败 Bug`, `更新用户文档`

- `--description`: 任务详情（可选）
  - 提供更详细的上下文
  - 支持多行文本
  - 示例：`排查并修复用户无法登录的问题，涉及认证逻辑`

- `--priority`: 优先级（可选，默认 2）
  - 4🔴 紧急：Bug/阻塞/安全/24h内
  - 3🟠 高：重要功能/影响体验/3天内
  - 2🟡 中：常规任务（默认）
  - 1🟢 低：可选改进/技术债/长期计划

- `--global`: 全局标记（可选）
  - 标记为全局任务
  - 不受工作流限制

#### 示例

**基础创建（使用默认优先级 2）：**
```bash
./llm-memory todo create \
  --code "todo-update-readme" \
  --title "更新项目 README"
```

**完整创建（包含所有参数）：**
```bash
./llm-memory todo create \
  --code "todo-fix-login-bug" \
  --title "修复登录失败 Bug" \
  --description "排查并修复用户无法登录的问题，涉及认证逻辑" \
  --priority 4
```

**全局任务：**
```bash
./llm-memory todo create \
  --code "todo-security-audit" \
  --title "安全审计" \
  --priority 4 \
  --global
```

#### 错误处理
- **Code 重复**：错误信息 "Code already exists"
  - 解决：使用不同的 code 重新创建

- **Code 格式不合法**：错误信息 "Invalid code format"
  - 解决：确保 code 符合格式要求

- **Priority 超范围**：错误信息 "Priority must be 1-4"
  - 解决：使用 1-4 之间的数字

---

### 2. 查看待办 (list)

#### 语法
```bash
./llm-memory todo list [--filter <status>] [--sort <field>]
```

#### 参数详解
- `--filter`: 按状态过滤（可选）
  - `pending`：待处理
  - `in-progress`：进行中
  - `completed`：已完成

- `--sort`: 按字段排序（可选）
  - `priority`：按优先级（默认）
  - `created`：按创建时间
  - `status`：按状态

#### 示例

**查看所有待办：**
```bash
./llm-memory todo list
```

**只查看进行中的任务：**
```bash
./llm-memory todo list --filter in-progress
```

**按创建时间排序：**
```bash
./llm-memory todo list --sort created
```

#### 输出格式
```
Code                    | Title           | Status       | Priority | Created
todo-fix-login-bug      | 修复登录Bug      | pending      | 4        | 2025-12-05
todo-update-readme      | 更新 README     | in-progress  | 2        | 2025-12-04
```

---

### 3. 开始任务 (start)

#### 语法
```bash
./llm-memory todo start --code <code>
```

#### 参数详解
- `--code`: 待办标识码（必填）
  - 必须是已创建的 code

#### 示例
```bash
./llm-memory todo start --code "todo-fix-login-bug"
# 输出：✅ 待办 todo-fix-login-bug 已开始
```

#### 错误处理
- **Code 不存在**：错误信息 "Code not found"
  - 解决：确认 code 是否正确，使用 list 查看

- **任务已完成**：错误信息 "Cannot start a completed task"
  - 解决：使用 `todo reset` 重置任务（如支持）

---

### 4. 完成任务 (complete)

#### 语法
```bash
./llm-memory todo complete --code <code>
```

#### 参数详解
- `--code`: 待办标识码（必填）

#### 示例
```bash
./llm-memory todo complete --code "todo-fix-login-bug"
# 输出：✅ 待办 todo-fix-login-bug 已完成
```

#### 错误处理
- **Code 不存在**：参考 start 命令

---

### 5. 删除待办 (delete)

#### 语法
```bash
./llm-memory todo delete --code <code> [--force]
```

#### 参数详解
- `--code`: 待办标识码（必填）
- `--force`: 强制删除（可选）
  - 忽略确认提示
  - 不可撤销

#### 示例

**普通删除（需要确认）：**
```bash
./llm-memory todo delete --code "todo-obsolete-task"
# 提示：确定要删除 todo-obsolete-task 吗？(y/n)
```

**强制删除（无需确认）：**
```bash
./llm-memory todo delete --code "todo-obsolete-task" --force
# 输出：✅ 待办 todo-obsolete-task 已删除
```

---

### 6. 标记所有完成 (final)

#### 语法
```bash
./llm-memory todo final [--filter <status>]
```

#### 参数详解
- `--filter`: 只完成特定状态的任务（可选）
  - `pending`：完成所有待处理任务
  - `in-progress`：完成所有进行中的任务
  - 不指定则完成所有

#### 示例

**完成所有任务：**
```bash
./llm-memory todo final
# 输出：✅ 已完成 12 个待办事项
```

**只完成进行中的任务：**
```bash
./llm-memory todo final --filter in-progress
# 输出：✅ 已完成 3 个进行中的待办事项
```

#### 错误处理
- 如果没有匹配的任务：
  - 输出：✅ 没有需要完成的待办

---

## 批量命令

批量命令允许在单次操作中处理最多 100 个待办事项。详细说明参见 [批量操作指南](./batch-operations.md)。

### 批量创建 (batch-create)
```bash
./llm-memory todo batch-create --json '[{...}]'
./llm-memory todo batch-create --json-file ./todos.json
```

### 批量开始 (batch-start)
```bash
./llm-memory todo batch-start --codes "code1,code2,code3"
```

### 批量完成 (batch-complete)
```bash
./llm-memory todo batch-complete --codes "code1,code2,code3"
```

### 批量取消 (batch-cancel)
```bash
./llm-memory todo batch-cancel --codes "code1,code2"
```

### 批量删除 (batch-delete)
```bash
./llm-memory todo batch-delete --codes "code1,code2"
```

### 批量更新 (batch-update)
```bash
./llm-memory todo batch-update --json '[
  {"code":"code1","priority":3},
  {"code":"code2","title":"新标题"}
]'
```

---

## 常见错误排查

| 错误信息 | 原因 | 解决方案 |
|---------|------|--------|
| Code not found | 待办不存在 | 使用 `list` 确认 code |
| Invalid code format | Code 格式错误 | 遵循格式规则：全小写+连字符 |
| Priority must be 1-4 | 优先级超范围 | 使用 1、2、3 或 4 |
| Code already exists | Code 重复 | 使用不同的 code |
| Invalid JSON | JSON 格式错误 | 检查 JSON 语法 |
| File not found | 文件不存在 | 检查文件路径 |

---

## 最佳实践

1. **Code 命名一致性**：使用 `todo-<动作>-<对象>` 格式
2. **优先级准确**：参考 [优先级规则](../shared-references/priority-guide.md)
3. **批量操作**：5 个以上任务使用 batch-create
4. **定期清理**：完成后及时删除已完成的任务
5. **描述完整**：重要任务添加详细 description

更多详情见 [最佳实践指南](../shared-references/best-practices.md)。
