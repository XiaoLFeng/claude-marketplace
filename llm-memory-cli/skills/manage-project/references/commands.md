# Plan CLI 命令详解

## plan add

创建计划。

```bash
llm-memory plan add <code> <title> [options]

# 参数
<code>        计划唯一标识，格式: plan-xxx
<title>       计划标题

# 选项
--description  计划描述

# 示例
llm-memory plan add plan-user-auth "用户认证系统" \
  --description "实现完整的用户认证功能"
```

## plan list

列出计划。

```bash
llm-memory plan list [options]

# 选项
--scope       作用域: personal/group/all (默认 all)

# 示例
llm-memory plan list
llm-memory plan list --scope personal
```

## plan show

获取计划详情。

```bash
llm-memory plan show <code>

# 示例
llm-memory plan show plan-user-auth
```

## plan update

更新计划。

```bash
llm-memory plan update <code> [options]

# 选项
--title        新标题
--description  新描述
--progress     进度 0-100 (状态自动转换)

# 示例
llm-memory plan update plan-user-auth --progress 45
llm-memory plan update plan-user-auth --title "新标题"
```

## 状态自动转换

设置 `--progress` 时，状态自动调整：

| Progress | Status |
|----------|--------|
| 0 | pending（待开始）|
| 1-99 | in_progress（进行中）|
| 100 | completed（已完成）|
