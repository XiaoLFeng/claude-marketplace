# Plan MCP 工具详解

## plan_list

列出计划。

```javascript
plan_list({
  scope: "all"  // personal/group/all
})

// 返回
[
  {
    code: "plan-xxx",
    title: "计划标题",
    description: "简短描述",
    progress: 45,
    status: "in_progress",
    created_at: "2024-01-01T00:00:00Z",
    updated_at: "2024-01-15T00:00:00Z"
  }
]
```

## plan_create

创建计划。

```javascript
plan_create({
  code: "plan-xxx",           // 必填：唯一标识
  title: "计划标题",           // 必填：简洁描述
  description: "计划摘要",     // 必填：一句话概括
  content: "详细内容..."       // 必填：Markdown 格式
})

// 返回
{
  success: true,
  code: "plan-xxx",
  message: "Plan created successfully"
}
```

## plan_get

获取计划详情。

```javascript
plan_get({
  code: "plan-xxx"
})

// 返回完整计划
{
  code: "plan-xxx",
  title: "计划标题",
  description: "计划摘要",
  content: "详细内容...",
  progress: 45,
  status: "in_progress",
  created_at: "2024-01-01T00:00:00Z",
  updated_at: "2024-01-15T00:00:00Z"
}
```

## plan_update

更新计划（部分更新）。

```javascript
plan_update({
  code: "plan-xxx",           // 必填
  title: "新标题",             // 可选
  description: "新描述",       // 可选
  content: "新内容",           // 可选
  progress: 60                // 可选：0-100，状态自动转换
})

// 返回
{
  success: true,
  code: "plan-xxx",
  message: "Plan updated successfully"
}
```

## 状态自动转换

设置 `progress` 时，状态自动调整：

| Progress | Status |
|----------|--------|
| 0 | pending（待开始）|
| 1-99 | in_progress（进行中）|
| 100 | completed（已完成）|

## 作用域说明

| Scope | 说明 |
|-------|------|
| personal | 当前目录私有（默认） |
| group | 组内共享（需先加入组） |
| all | 查询时返回全部可见 |
