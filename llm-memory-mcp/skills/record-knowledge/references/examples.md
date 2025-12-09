# Record Knowledge 使用示例

## 示例 1: 技术决策记录

**场景**：讨论后决定使用 JWT 而非 Session

```javascript
memory_create({
  code: "mem-jwt-decision",
  title: "JWT vs Session 技术选型",
  content: `## 决策背景
用户认证方案选型

## 选项对比
| 方案 | 优点 | 缺点 |
|-----|------|------|
| JWT | 无状态、可扩展 | Token 大、无法即时失效 |
| Session | 即时失效、安全 | 需要存储、扩展难 |

## 最终决策
选择 JWT

## 决策理由
1. 微服务架构需要无状态
2. 配合 Redis 实现 Token 黑名单
3. 前端 SPA 更易集成

## 实施要点
- Access Token 有效期 15 分钟
- Refresh Token 有效期 7 天
- 敏感操作需要二次验证`,
  category: "技术决策",
  tags: ["auth", "jwt", "security"],
  global: false
})
```

## 示例 2: Bug 解决记录

**场景**：排查了 2 小时的登录问题

```javascript
memory_create({
  code: "mem-login-cors-bug",
  title: "登录接口 CORS 问题排查",
  content: `## 问题描述
前端调用登录接口返回 CORS 错误

## 排查过程
1. 检查 Nginx 配置 - 正常
2. 检查后端 CORS 配置 - 正常
3. 发现：预检请求被拦截

## 根本原因
Spring Security 过滤器在 CORS 过滤器之前执行

## 解决方案
\`\`\`java
@Bean
public CorsConfigurationSource corsConfigurationSource() {
    // 配置代码...
}

@Bean
public SecurityFilterChain filterChain(HttpSecurity http) {
    http.cors(cors -> cors.configurationSource(corsConfigurationSource()));
}
\`\`\`

## 经验教训
CORS 配置要在安全过滤器之前`,
  category: "Bug解决",
  tags: ["cors", "spring-security", "login"],
  global: false
})
```

## 示例 3: 代码模板记录

**场景**：写了一个通用的分页查询模板

```javascript
memory_create({
  code: "mem-page-query-template",
  title: "通用分页查询模板",
  content: `## 使用场景
需要分页查询时复用此模板

## 代码模板
\`\`\`typescript
interface PageParams {
  page: number;
  size: number;
  sort?: string;
}

interface PageResult<T> {
  content: T[];
  totalElements: number;
  totalPages: number;
  number: number;
}

async function pageQuery<T>(
  url: string,
  params: PageParams
): Promise<PageResult<T>> {
  const response = await fetch(
    \`\${url}?page=\${params.page}&size=\${params.size}\`
  );
  return response.json();
}
\`\`\`

## 使用示例
\`\`\`typescript
const users = await pageQuery<User>('/api/users', {
  page: 0,
  size: 20
});
\`\`\``,
  category: "代码示例",
  tags: ["pagination", "typescript", "template"],
  global: true  // 通用模板设为全局
})
```

## 示例 4: 项目规范记录

**场景**：制定了 Git 提交规范

```javascript
memory_create({
  code: "mem-git-commit-convention",
  title: "Git Commit 提交规范",
  content: `## 格式
\`\`\`
<type>(<scope>): <subject>

<body>

<footer>
\`\`\`

## Type 类型
- feat: 新功能
- fix: Bug 修复
- docs: 文档更新
- style: 代码格式
- refactor: 重构
- test: 测试
- chore: 构建/工具

## 示例
\`\`\`
feat(auth): 添加 JWT 认证

- 实现 JWT 生成和验证
- 添加 Refresh Token 机制
- 集成 Redis Token 黑名单

Closes #123
\`\`\``,
  category: "项目规范",
  tags: ["git", "convention", "commit"],
  global: false
})
```
