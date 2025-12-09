---
name: search-history
description: "历史搜索器 - 搜索之前的记录和经验。适用：用户问'之前怎么做的'、'有没有相关记录'、'历史上...'。不适用：创建新记录用 record-knowledge。"
---

# Search History

搜索之前的记录和经验，快速找到相关知识。

## 触发条件

- 用户问"之前怎么做的"
- 用户说"搜索"、"查找"、"有没有..."

## 操作流程

```bash
# 搜索记忆
llm-memory memory search --keyword "关键词"

# 获取详情
llm-memory memory get --code mem-xxx
```

## CLI 命令清单

- `llm-memory memory search --keyword xxx` - 搜索记忆
- `llm-memory memory get --code xxx` - 获取记忆详情

详见：[使用示例](./references/examples.md)
