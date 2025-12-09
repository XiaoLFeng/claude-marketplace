---
name: session-init
description: "新对话初始化器 - 新对话开始时自动加载当前项目状态。运行 llm-memory 命令快速获取进行中的计划和待办，让 Claude 了解项目背景。"
---

# Session Init

新对话开始时自动加载项目状态，快速了解当前进展。

## 触发条件

- 新对话开始
- 用户说"当前状态"、"有什么进展"、"项目情况"

## 操作流程

```bash
# 获取计划概览
llm-memory plan list

# 获取待办概览
llm-memory todo list
```

## 输出示例

```
📊 项目状态概览

📋 计划: 2 个进行中
✅ 待办: 8 个待完成
  - 🔴 紧急: 1 个
  - 🟠 高优先: 2 个
```

## CLI 命令清单

- `llm-memory plan list` - 列出计划
- `llm-memory todo list` - 列出待办

详见：[工作流参考](./references/workflow.md)
