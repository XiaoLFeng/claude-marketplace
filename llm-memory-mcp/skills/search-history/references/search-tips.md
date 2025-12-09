# 搜索技巧

## 关键词选择

### 使用具体词汇

```
✅ "jwt 认证"     具体
✅ "登录超时"     具体问题
✅ "api 规范"     具体主题

❌ "问题"         太宽泛
❌ "代码"         太宽泛
❌ "功能"         太宽泛
```

### 尝试同义词

```
认证 / auth / authentication
登录 / login / signin
配置 / config / setting
```

### 尝试中英文

```
memory_search({ keyword: "认证" })
memory_search({ keyword: "auth" })
```

## 搜索流程

### 标准流程

```
用户问题
    ↓
提取关键词
    ↓
memory_search(keyword)
    ↓
有结果？
    ↓
├── 是 → memory_get 获取详情 → 展示
└── 否 → 尝试其他关键词 / 告知无结果
```

### 多轮搜索

```javascript
// 第一轮：原始关键词
let results = await memory_search({ keyword: "jwt认证" });

// 没找到？第二轮：拆分关键词
if (results.length === 0) {
  results = await memory_search({ keyword: "jwt" });
}

// 还没找到？第三轮：同义词
if (results.length === 0) {
  results = await memory_search({ keyword: "token" });
}
```

## 结果处理

### 结果排序

通常按相关性和更新时间排序：
1. 标题匹配 > 内容匹配
2. 最近更新 > 较早更新

### 结果筛选

```javascript
// 只看技术决策
results.filter(r => r.category === "技术决策")

// 只看最近的
results.filter(r => isRecent(r.updated_at))
```

### 展示策略

```
结果数量  展示策略
───────────────────
0         告知无结果，建议其他关键词
1-3       展示摘要，询问是否查看详情
4-10      列表展示，让用户选择
>10       按相关性取前10，提示更多
```

## 常见搜索场景

### 技术决策

```
关键词: 技术名称 + "决策/选型/方案"
示例: "数据库 选型", "框架 决策"
```

### Bug 修复

```
关键词: 问题描述 + "修复/解决/bug"
示例: "超时 修复", "内存泄漏"
```

### 设计规范

```
关键词: 主题 + "规范/标准/约定"
示例: "api 规范", "代码 标准"
```

### 配置信息

```
关键词: 环境 + "配置/部署"
示例: "生产 配置", "k8s 部署"
```

## 无结果处理

```
🔍 搜索结果: "xxx"

未找到相关记录。

建议:
1. 尝试其他关键词: [建议词1], [建议词2]
2. 使用更宽泛的搜索词
3. 如果这是新知识，可以用 record-decision 记录
```
