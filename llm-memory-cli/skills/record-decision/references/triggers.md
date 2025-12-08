# Record Decision - 触发场景详解

## 自动识别场景

### 场景 1：技术方案选型

**触发关键词**：

```
- 选择...框架/库/工具
- 决定使用...
- 对比...方案
- 采用...技术
- 引入...库
- ...vs...
```

**示例对话**：

```
用户："我们需要选择一个状态管理方案，Redux 还是 Zustand？"
↓
识别到：技术选型场景
↓
讨论后记录决策过程和最终选择
```

**识别逻辑**：

```bash
# 检测关键词
is_tech_selection() {
  local input="$1"

  if [[ "$input" =~ 选择.*(框架|库|工具|方案) ]] || \
     [[ "$input" =~ 决定使用 ]] || \
     [[ "$input" =~ 对比.*方案 ]] || \
     [[ "$input" =~ 采用.*技术 ]] || \
     [[ "$input" =~ 引入.*库 ]] || \
     [[ "$input" =~ [a-zA-Z]+[[:space:]]*vs[[:space:]]*[a-zA-Z]+ ]]; then
    return 0
  fi
  return 1
}
```

---

### 场景 2：复杂 Bug 排查

**触发条件**：

- 排查时间 > 30 分钟
- 涉及多个系统/组件
- 问题根因不明显

**触发关键词**：

```
- 排查...bug/问题
- 解决了...问题
- 发现...根因/原因
- 修复...原因是
- 终于找到...
```

**示例对话**：

```
用户："终于找到内存泄漏的原因了，是 WebSocket 连接没有正确清理"
↓
识别到：Bug 排查场景
↓
记录排查过程和解决方案
```

**识别逻辑**：

```bash
is_bug_fix() {
  local input="$1"

  if [[ "$input" =~ 排查.*bug ]] || \
     [[ "$input" =~ 解决了.*问题 ]] || \
     [[ "$input" =~ 发现.*根因 ]] || \
     [[ "$input" =~ 修复.*原因 ]] || \
     [[ "$input" =~ 终于找到 ]] || \
     [[ "$input" =~ 原来是.*导致 ]]; then
    return 0
  fi
  return 1
}
```

---

### 场景 3：项目规范制定

**触发关键词**：

```
- 制定...规范
- 约定...风格
- 统一...标准
- 规范...格式
- 定义...约定
```

**示例对话**：

```
用户："我们统一 API 响应格式，都使用 { code, data, message } 结构"
↓
识别到：规范制定场景
↓
记录规范内容
```

**识别逻辑**：

```bash
is_convention() {
  local input="$1"

  if [[ "$input" =~ 制定.*规范 ]] || \
     [[ "$input" =~ 约定.*风格 ]] || \
     [[ "$input" =~ 统一.*标准 ]] || \
     [[ "$input" =~ 规范.*格式 ]] || \
     [[ "$input" =~ 定义.*约定 ]]; then
    return 0
  fi
  return 1
}
```

---

### 场景 4：可复用代码模式

**触发关键词**：

```
- 封装...方法/函数
- 抽象...逻辑
- 可复用.*模式
- 通用...组件
- 公共...工具
```

**示例对话**：

```
用户："我封装了一个通用的错误处理中间件，可以统一处理所有异常"
↓
识别到：代码模式场景
↓
记录代码模式和使用方法
```

**识别逻辑**：

```bash
is_pattern() {
  local input="$1"

  if [[ "$input" =~ 封装.*(方法|函数|组件) ]] || \
     [[ "$input" =~ 抽象.*逻辑 ]] || \
     [[ "$input" =~ 可复用.*模式 ]] || \
     [[ "$input" =~ 通用.*(组件|方法) ]] || \
     [[ "$input" =~ 公共.*(工具|函数) ]]; then
    return 0
  fi
  return 1
}
```

---

## 手动触发

### 触发关键词

```
- 记录这个决策
- 保存这个方案
- 把这个记下来
- 记录一下
```

### 示例对话

```
用户："记录这个决策，我们决定使用 PostgreSQL 而不是 MySQL"
↓
识别到：手动触发
↓
提取决策信息并记录
```

---

## 不触发场景

### 简单问答

```
用户："JWT 是什么？"
↓
不触发（只是询问信息）
```

### 临时记录

```
用户："记住我明天要提交代码"
↓
不触发（使用 todo 而非 memory）
```

### 一般性讨论

```
用户："我觉得 React 挺好用的"
↓
不触发（没有明确决策）
```

---

## 识别优先级

当多个场景同时匹配时，按以下优先级处理：

1. **手动触发** - 用户明确要求记录
2. **技术选型** - 架构决策
3. **Bug 排查** - 问题解决
4. **规范制定** - 团队约定
5. **代码模式** - 最佳实践

---

## 完整识别脚本

```bash
#!/bin/bash
# identify-decision.sh

identify_decision_scenario() {
  local context="$1"

  # 手动触发
  if [[ "$context" =~ 记录.*(决策|方案) ]] || \
     [[ "$context" =~ 保存.*方案 ]] || \
     [[ "$context" =~ 把.*记下来 ]]; then
    echo "manual|手动记录"
    return
  fi

  # 技术选型
  if [[ "$context" =~ 选择.*(框架|库|工具) ]] || \
     [[ "$context" =~ 决定使用 ]] || \
     [[ "$context" =~ 对比.*方案 ]]; then
    echo "tech_selection|架构决策"
    return
  fi

  # Bug 排查
  if [[ "$context" =~ 排查.*bug ]] || \
     [[ "$context" =~ 解决了.*问题 ]] || \
     [[ "$context" =~ 发现.*根因 ]]; then
    echo "bug_fix|问题排查"
    return
  fi

  # 规范制定
  if [[ "$context" =~ 制定.*规范 ]] || \
     [[ "$context" =~ 约定.*风格 ]] || \
     [[ "$context" =~ 统一.*标准 ]]; then
    echo "convention|设计规范"
    return
  fi

  # 代码模式
  if [[ "$context" =~ 封装.*方法 ]] || \
     [[ "$context" =~ 抽象.*逻辑 ]] || \
     [[ "$context" =~ 可复用.*模式 ]]; then
    echo "pattern|最佳实践"
    return
  fi

  echo "none|"
}

# 使用示例
result=$(identify_decision_scenario "我们决定使用 JWT 认证")
type=$(echo "$result" | cut -d'|' -f1)
category=$(echo "$result" | cut -d'|' -f2)

if [ "$type" != "none" ]; then
  echo "识别场景: $type"
  echo "建议分类: $category"
fi
```
