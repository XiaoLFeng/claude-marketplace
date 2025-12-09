# 搜索技巧

## 关键词选择

### 使用具体词汇

```
✅ "jwt 认证"     具体
✅ "登录超时"     具体问题

❌ "问题"         太宽泛
❌ "代码"         太宽泛
```

### 尝试同义词

```
认证 / auth / authentication
登录 / login / signin
```

## 搜索流程

```
用户问题
    ↓
提取关键词
    ↓
llm-memory memory search "keyword"
    ↓
有结果？
    ↓
├── 是 → llm-memory memory show xxx → 展示
└── 否 → 尝试其他关键词 / 告知无结果
```

## CLI 命令

```bash
# 搜索
llm-memory memory search "jwt"

# 详情
llm-memory memory show mem-auth-jwt-decision

# 列出全部
llm-memory memory list
```

## 无结果处理

```
🔍 搜索结果: "xxx"

未找到相关记录。

建议:
1. 尝试其他关键词
2. 如果这是新知识，可以用 record-decision 记录
```
