# LLM-Memory 插件

嘿嘿~ 欢迎使用 LLM-Memory 智能工作流管理助手！(´∀`)💖

## 📖 简介

LLM-Memory 是一个为 LLM 设计的统一记忆管理系统，提供 CLI 和 MCP 双模式支持：

- **Memory（记忆）**：持久化存储重要信息、偏好、上下文片段
- **Plan（计划）**：跟踪多步骤目标，支持进度管理和子任务
- **Todo（待办）**：管理短期任务，支持批量操作

## 🎯 两种工作模式

### llm-workflow - CLI 版本

使用 llm-memory CLI 命令行工具进行管理。

**特点**：
- 直接调用系统命令
- 需要提前安装 llm-memory 工具
- 适合终端操作场景

**使用方法**：
```bash
# 创建待办
./llm-memory todo create --code "todo-xxx" --title "任务标题"

# 查看列表
./llm-memory todo list
```

📚 **详细文档**：[skills/llm-workflow/SKILL.md](./skills/llm-workflow/SKILL.md)

### llm-workflow-mcp - MCP 版本 ✨ 推荐

使用 llm-memory MCP 工具直接调用，无需 CLI。

**特点**：
- 原生 MCP 集成，无需终端
- 支持批量操作（最多 100 个）
- 更好的错误处理和返回格式
- 需要配置 MCP Server

**使用方法**：
```javascript
// 批量创建待办
todo_batch_create({
  items: [{
    code: "todo-xxx",
    title: "任务标题",
    priority: 2
  }],
  scope: "personal"
})

// 查看列表
todo_list({ scope: "all" })
```

📚 **详细文档**：[skills/llm-workflow-mcp/SKILL.md](./skills/llm-workflow-mcp/SKILL.md)

## 🚀 快速开始

### 前置要求

根据你选择的版本：

**CLI 版本**：
- 安装 llm-memory CLI 工具
- 确保可以在命令行运行 `llm-memory`

**MCP 版本** （推荐）：
1. 安装 llm-memory MCP Server
2. 配置 Claude Code 的 MCP 设置

### 安装 MCP Server

```bash
# 克隆 llm-memory 项目
git clone https://github.com/andreahaku/llm-memory
cd llm-memory

# 编译（Go 项目）
go build -o llm-memory

# 或使用预编译版本
# 下载对应平台的 release 版本
```

### 配置 Claude Code

方式一：使用插件内置的 `.mcp.json` 配置

插件已经包含了 MCP 配置，但你需要设置正确的路径：

编辑 `llm-memory/.mcp.json`：
```json
{
  "llm-memory": {
    "command": "/absolute/path/to/llm-memory",
    "args": ["mcp"],
    "env": {
      "LLM_MEMORY_ROOT": "${CLAUDE_PLUGIN_ROOT}"
    },
    "cwd": "${CLAUDE_PLUGIN_ROOT}"
  }
}
```

方式二：全局配置（推荐）

编辑 `~/.claude.json`：
```json
{
  "mcpServers": {
    "llm-memory": {
      "command": "/absolute/path/to/llm-memory",
      "args": ["mcp"]
    }
  }
}
```

## 📋 功能对比

| 特性 | CLI 版本 | MCP 版本 |
|------|----------|----------|
| 调用方式 | Bash 命令 | MCP 工具 |
| 集成度 | 需要终端 | 原生集成 |
| 批量操作 | 支持（JSON） | 原生支持 |
| 错误处理 | CLI 输出 | 结构化返回 |
| 性能 | 较慢（进程启动） | 较快（长连接） |
| 推荐场景 | 终端操作 | Claude Code 集成 |

## 📖 核心概念

### Code 格式规则

```
✅ 全小写字母 + 连字符
✅ 开头末尾必须是字母
✅ 最少 3 个字符

示例: todo-fix-bug, plan-user-auth, mem-api-design
```

### 作用域系统

- `personal`：当前目录私有
- `group`：组内路径共享（需先加入组）
- `global`：全局可见（仅 Memory 支持）

### 优先级系统

- `1` 🟢 低：可选改进
- `2` 🟡 中：常规任务（默认）
- `3` 🟠 高：重要功能
- `4` 🔴 紧急：Bug/阻塞

## 📚 文档索引

### 核心文档

- [llm-workflow (CLI 版本) SKILL.md](./skills/llm-workflow/SKILL.md)
- [llm-workflow-mcp (MCP 版本) SKILL.md](./skills/llm-workflow-mcp/SKILL.md)

### 示例文档

**CLI 版本**：
- [示例 1: 用户认证系统重构](./skills/llm-workflow/examples/auth-system-refactor.md)
- [示例 2: 修复登录 Bug](./skills/llm-workflow/examples/fix-login-bug.md)

**MCP 版本**：
- [示例 1: 用户认证系统重构](./skills/llm-workflow-mcp/examples/auth-system-refactor.md)
- [示例 2: 修复登录 Bug](./skills/llm-workflow-mcp/examples/fix-login-bug.md)

## 🎓 学习路径

1. **初学者**：
   - 阅读 [MCP 版本 SKILL.md](./skills/llm-workflow-mcp/SKILL.md)
   - 运行简单示例 [修复登录 Bug](./skills/llm-workflow-mcp/examples/fix-login-bug.md)
   - 创建第一个 Todo

2. **进阶用户**：
   - 学习复杂示例 [认证系统重构](./skills/llm-workflow-mcp/examples/auth-system-refactor.md)
   - 掌握批量操作
   - 使用 Memory 记录知识

3. **高级用户**：
   - 配置 Group 进行团队协作
   - 自定义工作流程
   - 整合到 CI/CD

## 🔧 故障排查

### CLI 版本常见问题

**Q: 命令找不到**
```bash
# 检查 llm-memory 是否在 PATH 中
which llm-memory

# 或使用绝对路径
/absolute/path/to/llm-memory todo list
```

**Q: Code 格式错误**
- 确保全小写
- 使用连字符（-）而非下划线（_）
- 开头末尾必须是字母

### MCP 版本常见问题

**Q: MCP 工具未找到**
- 检查 `~/.claude.json` 配置
- 确认 llm-memory 路径正确
- 重启 Claude Code

**Q: 批量操作失败**
- 检查是否超过 100 个限制
- 查看返回的 failures 列表
- 确认 Code 格式正确

## 📝 版本历史

### v1.1.0 (Current)
- ✨ 新增 llm-workflow-mcp (MCP 版本)
- 📝 更新文档和示例
- 🔧 优化 MCP 配置

### v1.0.0
- 🎉 初始版本
- 📦 llm-workflow (CLI 版本)
- 📚 完整文档和示例

## 🤝 贡献

欢迎提交 Issue 和 Pull Request！

## 📄 许可证

Apache-2.0

## 👤 作者

**xiao_lfeng**
- Email: gm@x-lf.cn
- Website: https://www.x-lf.com

---

呀~ 感谢使用 LLM-Memory 插件！如有问题欢迎反馈~ (´∀`)💖
