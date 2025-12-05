# Memory MCP 使用示例

嘿嘿~ 这里是 Memory MCP 工具在实际项目中的应用示例！(´∀`)💖

使用 MCP 工具调用方式，展示如何在真实场景中管理知识和记忆。

---

## 示例 1：架构决策记录

### 场景描述

在进行项目技术架构选型时，需要记录为什么选择 JWT 而不是 Session，以便团队成员理解设计决策。

### 实现方案

```javascript
// 创建架构决策记忆
memory_create({
  code: "mem-jwt-vs-session-decision",
  title: "JWT vs Session 选型分析",
  content: `# 技术选型分析

## 背景
项目需要选择一个认证机制，考虑 JWT 和 Session 两个方案。

## 为什么选择 JWT

### 优点
1. **无状态**: 服务器无需存储会话信息，便于负载均衡
2. **可扩展**: 易于水平扩展，无需共享会话存储
3. **跨域友好**: 通过 HTTP Header 传递，支持多域名部署
4. **移动端友好**: 适合 RESTful API 和移动应用

### JWT 的缺点和解决方案
1. **无法主动失效**
   - 问题：Token 在过期前无法主动撤销（如用户登出）
   - 解决：使用 Token 黑名单 (Redis) 存储被撤销的 Token

2. **Token 载荷较大**
   - 问题：每次请求都需要携带完整 Token
   - 解决：使用双 Token 机制（短期 Access + 长期 Refresh）

## 技术方案

### 双 Token 机制
\`\`\`
Access Token:
- 有效期：15 分钟
- 存储：JavaScript 内存
- 用途：API 请求认证

Refresh Token:
- 有效期：7 天
- 存储：HttpOnly Cookie
- 用途：更新 Access Token
\`\`\`

### 安全措施
1. 强制 HTTPS 传输
2. Refresh Token 使用 HttpOnly + Secure + SameSite Cookie
3. Access Token 存储在 JavaScript 内存中（非 localStorage）
4. 实现 Token 黑名单机制处理注销场景
5. 实现登录失败限制（5次后锁定15分钟）

## 对比表

| 特性 | JWT | Session |
|------|-----|---------|
| 无状态 | ✅ | ❌ |
| 可扩展性 | ✅ | ❌ (需要 Redis) |
| 跨域支持 | ✅ | ❌ |
| 主动失效 | ❌ | ✅ |
| Token 大小 | 较大 | 很小 |
| 实现复杂度 | 中 | 简单 |

## 决策依据

我们选择 JWT 是因为：
- 项目使用微服务架构，需要无状态认证
- 前后端完全分离，需要跨域认证
- 有移动 APP，需要 RESTful API 支持
- 通过双 Token 机制和黑名单可以解决 JWT 的缺点

## 参考资源
- [JWT.io - JWT 官方网站](https://jwt.io)
- [Auth0 - JWT Handbook](https://auth0.com/blog/jwt-handbook/)
- [OWASP - Authentication Cheat Sheet](https://cheatsheetseries.owasp.org/)`,
  category: "架构决策",
  tags: ["auth", "jwt", "architecture", "decision"],
  priority: 3,
  global: true,  // 全局可见
  scope: "personal"
})

// 返回示例：
// {
//   success: true,
//   data: {
//     code: "mem-jwt-vs-session-decision",
//     title: "JWT vs Session 选型分析",
//     created_at: "2024-12-05T10:30:00Z"
//   }
// }
```

### 后续查询

```javascript
// 查看这条决策记录
memory_get({ code: "mem-jwt-vs-session-decision" })

// 搜索相关的认证记忆
memory_search({
  keyword: "JWT",
  scope: "global"
})

// 如果有新发现需要更新
memory_update({
  code: "mem-jwt-vs-session-decision",
  content: `# 更新的技术选型分析\n\n...更新的内容...`,
  priority: 3
})
```

---

## 示例 2：Bug 解决方案记录

### 场景描述

在修复一个认证 Bug 时，发现了根因和完整的解决方案，需要记录下来防止团队其他成员重复踩坑。

### 实现方案

```javascript
// 记录 Bug 排查和解决过程
memory_create({
  code: "mem-login-bug-fix-guide",
  title: "登录失败 Bug 排查和修复指南",
  content: `# 登录 Bug 排查和修复指南

## 问题描述

用户反馈：账户密码正确但无法登录，显示"认证失败"错误。

## 根因分析

### 排查步骤
1. 查看错误日志，找到相关异常堆栈
2. 在本地重现问题，发现登录总是失败
3. 检查最近的代码变更日志
4. 定位到 auth.service.ts 中的密码比对逻辑

### 根本原因

提交 commit abc1234 中修改了密码验证逻辑，将 bcrypt.compare() 的参数顺序写反了。

\`\`\`javascript
// ❌ 错误写法 (commit abc1234)
public async validatePassword(plainPassword: string, hashedPassword: string): Promise<boolean> {
  return await bcrypt.compare(hashedPassword, plainPassword)  // 参数顺序错误！
}

// ✅ 正确写法
public async validatePassword(plainPassword: string, hashedPassword: string): Promise<boolean> {
  return await bcrypt.compare(plainPassword, hashedPassword)  // 第一个参数是明文，第二个是哈希
}
\`\`\`

## 修复方案

### 代码修复
\`\`\`javascript
// auth.service.ts
public async validatePassword(
  plainPassword: string,
  hashedPassword: string
): Promise<boolean> {
  // bcrypt.compare(data, encrypted) - 参数顺序很重要！
  return await bcrypt.compare(plainPassword, hashedPassword)
}

// 添加单元测试确保不会再出现此问题
describe('AuthService', () => {
  describe('validatePassword', () => {
    it('应该验证正确的密码', async () => {
      const plainPassword = 'myPassword123'
      const hashedPassword = await bcrypt.hash(plainPassword, 10)

      const result = await authService.validatePassword(
        plainPassword,
        hashedPassword
      )

      expect(result).toBe(true)
    })

    it('应该拒绝错误的密码', async () => {
      const plainPassword = 'myPassword123'
      const wrongPassword = 'wrongPassword'
      const hashedPassword = await bcrypt.hash(plainPassword, 10)

      const result = await authService.validatePassword(
        wrongPassword,
        hashedPassword
      )

      expect(result).toBe(false)
    })
  })
})
\`\`\`

## 教训总结

### 1. 参数顺序很重要
bcrypt.compare() 的签名是 \`compare(data, encrypted)\`：
- 第一个参数：明文密码
- 第二个参数：哈希后的密码

记忆技巧：Compare 是把"数据"与"加密"进行比对。

### 2. 关键认证逻辑需要完整的单元测试
- 不能只依赖集成测试
- 必须覆盖正确密码和错误密码的情况
- 考虑边界情况（空密码、特殊字符等）

### 3. Code Review 需要更仔细
- 认证相关的改动需要特别关注
- 需要验证参数顺序是否正确
- 应该检查是否有单元测试

### 4. 考虑添加自动化测试
\`\`\`bash
# 在 CI/CD 中添加登录测试
npm run test:auth

# 部署前进行冒烟测试
npm run test:smoke
\`\`\`

## 影响范围

- **发生时间**：2024-12-04 15:30 - 17:30 (2小时)
- **影响用户**：约 50 人尝试登录，全部失败
- **影响系统**：只影响登录功能，其他功能正常
- **数据安全**：无数据泄露或丢失风险
- **补救时间**：修复+部署+验证，共30分钟

## 预防措施

1. 在 auth.service.ts 添加详细注释
2. 补充完整的单元测试
3. 添加集成测试验证登录流程
4. Code Review Checklist 中添加"验证密码比对参数"

## 参考资源
- [bcrypt.js 文档](https://github.com/dcodeIO/bcrypt.js)
- [OWASP 密码存储指南](https://cheatsheetseries.owasp.org/cheatsheets/Password_Storage_Cheat_Sheet.html)`,
  category: "问题排查",
  tags: ["debug", "auth", "password", "bug-fix", "lessons-learned"],
  priority: 3,
  global: false,
  scope: "personal"
})

// 返回示例：
// {
//   success: true,
//   data: {
//     code: "mem-login-bug-fix-guide",
//     title: "登录失败 Bug 排查和修复指南",
//     created_at: "2024-12-05T17:45:00Z"
//   }
// }
```

### 后续使用

```javascript
// 当新的开发人员遇到类似问题时
memory_search({
  keyword: "登录失败",
  scope: "personal"
})

// 如果又发现了相关信息，可以更新
memory_update({
  code: "mem-login-bug-fix-guide",
  content: `# 更新版本...\n\n...新增补充内容...`,
  priority: 3
})
```

---

## 示例 3：代码片段库

### 场景描述

收集项目中经常使用的代码片段和配置模板，便于快速参考和复用。

### 实现方案

```javascript
// 创建 JWT Token 实现示例
memory_create({
  code: "mem-jwt-implementation-example",
  title: "JWT Token 签发和验证实现示例",
  content: `# JWT Token 实现示例

## Token 签发（Issue Token）

\`\`\`javascript
import jwt from 'jsonwebtoken'

class TokenService {
  private readonly accessTokenSecret = process.env.ACCESS_TOKEN_SECRET
  private readonly refreshTokenSecret = process.env.REFRESH_TOKEN_SECRET

  // 签发 Access Token
  issueAccessToken(userId: string): string {
    return jwt.sign(
      { userId, type: 'access' },
      this.accessTokenSecret,
      { expiresIn: '15m' }  // 15分钟有效期
    )
  }

  // 签发 Refresh Token
  issueRefreshToken(userId: string): string {
    return jwt.sign(
      { userId, type: 'refresh' },
      this.refreshTokenSecret,
      { expiresIn: '7d' }  // 7天有效期
    )
  }

  // 同时签发两个 Token
  issueTokenPair(userId: string): { accessToken: string; refreshToken: string } {
    return {
      accessToken: this.issueAccessToken(userId),
      refreshToken: this.issueRefreshToken(userId)
    }
  }
}
\`\`\`

## Token 验证（Verify Token）

\`\`\`javascript
class TokenService {
  // 验证 Access Token
  verifyAccessToken(token: string): { userId: string } | null {
    try {
      const decoded = jwt.verify(token, this.accessTokenSecret)
      if (decoded.type !== 'access') {
        throw new Error('Invalid token type')
      }
      return decoded as { userId: string }
    } catch (error) {
      console.error('Token verification failed:', error)
      return null
    }
  }

  // 验证 Refresh Token
  verifyRefreshToken(token: string): { userId: string } | null {
    try {
      const decoded = jwt.verify(token, this.refreshTokenSecret)
      if (decoded.type !== 'refresh') {
        throw new Error('Invalid token type')
      }
      return decoded as { userId: string }
    } catch (error) {
      console.error('Token verification failed:', error)
      return null
    }
  }
}
\`\`\`

## Token 刷新（Refresh Token）

\`\`\`javascript
class AuthController {
  async refreshToken(req: Request, res: Response) {
    const refreshToken = req.cookies.refreshToken

    if (!refreshToken) {
      return res.status(401).json({ error: 'No refresh token' })
    }

    // 验证 Refresh Token
    const decoded = this.tokenService.verifyRefreshToken(refreshToken)
    if (!decoded) {
      return res.status(401).json({ error: 'Invalid refresh token' })
    }

    // 检查黑名单
    const isBlacklisted = await this.tokenBlacklist.isBlacklisted(refreshToken)
    if (isBlacklisted) {
      return res.status(401).json({ error: 'Token has been revoked' })
    }

    // 签发新的 Token 对
    const { accessToken, refreshToken: newRefreshToken } =
      this.tokenService.issueTokenPair(decoded.userId)

    // 设置新的 Refresh Token Cookie
    res.cookie('refreshToken', newRefreshToken, {
      httpOnly: true,
      secure: process.env.NODE_ENV === 'production',
      sameSite: 'strict'
    })

    return res.json({ accessToken })
  }
}
\`\`\`

## 认证中间件

\`\`\`javascript
// Express 认证中间件
function authMiddleware(req: Request, res: Response, next: NextFunction) {
  const authHeader = req.headers.authorization

  if (!authHeader || !authHeader.startsWith('Bearer ')) {
    return res.status(401).json({ error: 'Missing or invalid token' })
  }

  const token = authHeader.substring(7)  // 移除 "Bearer " 前缀

  const decoded = tokenService.verifyAccessToken(token)
  if (!decoded) {
    return res.status(401).json({ error: 'Invalid or expired token' })
  }

  // 将用户信息存储在请求对象中
  req.userId = decoded.userId
  next()
}

// 使用中间件
app.get('/api/profile', authMiddleware, (req, res) => {
  res.json({ userId: req.userId })
})
\`\`\`

## 使用示例

\`\`\`javascript
// 1. 登录 - 签发 Token
POST /api/auth/login
{
  "email": "user@example.com",
  "password": "password123"
}

Response:
{
  "accessToken": "eyJhbGc...",
  "refreshToken": "eyJhbGc..."  // 存储在 Cookie 中
}

// 2. 访问受保护资源
GET /api/profile
Authorization: Bearer eyJhbGc...

// 3. Token 过期 - 使用 Refresh Token 获取新的
POST /api/auth/refresh
Cookie: refreshToken=eyJhbGc...

Response:
{
  "accessToken": "eyJhbGc..."  // 新的 Access Token
}

// 4. 登出 - Token 加入黑名单
POST /api/auth/logout
\`\`\``,
  category: "代码示例",
  tags: ["jwt", "token", "auth", "implementation", "code-snippet"],
  priority: 2,
  global: true,  // 全局可见，其他项目也能参考
  scope: "personal"
})

// 返回示例：
// {
//   success: true,
//   data: {
//     code: "mem-jwt-implementation-example",
//     created_at: "2024-12-05T18:20:00Z"
//   }
// }
```

---

## 示例 4：项目规范记录

### 场景描述

记录项目的编码规范、命名约定和代码风格要求，确保整个团队遵循相同的标准。

### 实现方案

```javascript
// 创建项目编码规范
memory_create({
  code: "mem-project-coding-standards",
  title: "项目编码规范和命名约定",
  content: `# 项目编码规范

## 文件和目录命名

### 目录结构
\`\`\`
src/
├── controllers/       # API 控制器
├── services/         # 业务逻辑服务
├── models/           # 数据模型
├── middleware/       # 中间件
├── utils/            # 工具函数
├── config/           # 配置文件
├── types/            # TypeScript 类型定义
└── constants/        # 常量定义
\`\`\`

### 命名规则

**文件命名**
- 使用 kebab-case（单词用连字符分隔）
- 示例：
  - \`user-controller.ts\`
  - \`auth-service.ts\`
  - \`database-config.ts\`
  - \`error-handler.middleware.ts\`

**导出类/接口命名**
- 使用 PascalCase（帕斯卡命名法）
- 示例：
  - \`class UserController\`
  - \`interface IUserService\`
  - \`type UserRequest\`

**函数和变量命名**
- 使用 camelCase（驼峰命名法）
- 示例：
  - \`function getUserById()\`
  - \`const maxRetries = 3\`
  - \`let isAuthenticated = false\`

**常量命名**
- 使用 UPPER_SNAKE_CASE
- 示例：
  - \`const MAX_LOGIN_ATTEMPTS = 5\`
  - \`const TOKEN_EXPIRY_TIME = 900000\`

## TypeScript 代码规范

### 类型定义

\`\`\`typescript
// ✅ 好的做法
interface IUser {
  id: string
  email: string
  name: string
  createdAt: Date
}

type UserRequest = {
  email: string
  password: string
}

// ❌ 避免
type User = {
  id: any
  email: string
  name: any
}

interface user {  // 大小写错误
  id: String
}
\`\`\`

### 函数签名

\`\`\`typescript
// ✅ 好的做法 - 完整的类型注解
async function getUserById(userId: string): Promise<IUser | null> {
  // 实现
}

function validateEmail(email: string): boolean {
  // 实现
}

// ❌ 避免 - 返回类型为 any
function getUserById(userId: string): any {
  // 实现
}
\`\`\`

## 错误处理规范

### 自定义错误类

\`\`\`typescript
class AppError extends Error {
  constructor(
    public statusCode: number,
    message: string,
    public code?: string
  ) {
    super(message)
    this.name = 'AppError'
  }
}

// 使用示例
throw new AppError(401, 'Invalid credentials', 'INVALID_CREDENTIALS')
\`\`\`

## 日志规范

\`\`\`typescript
// 日志级别
logger.debug('调试信息')      // 开发调试
logger.info('一般信息')       // 正常流程
logger.warn('警告信息')       // 需要关注的问题
logger.error('错误信息')      // 需要立即处理
\`\`\`

## 测试规范

### 单元测试

\`\`\`typescript
describe('UserService', () => {
  describe('getUserById', () => {
    it('应该根据 ID 返回用户', async () => {
      // Arrange - 准备测试数据
      const userId = 'test-user-id'

      // Act - 执行测试
      const user = await userService.getUserById(userId)

      // Assert - 验证结果
      expect(user).toBeDefined()
      expect(user.id).toBe(userId)
    })

    it('用户不存在时应该返回 null', async () => {
      const user = await userService.getUserById('non-existent')
      expect(user).toBeNull()
    })
  })
})
\`\`\`

## 代码审查检查清单

- [ ] 所有代码都有类型注解（TypeScript）
- [ ] 函数长度不超过 30 行
- [ ] 圈复杂度不超过 10
- [ ] 有完整的单元测试（覆盖率 >= 80%）
- [ ] 错误处理完善
- [ ] 没有 console.log 调试代码
- [ ] 没有注释掉的代码
- [ ] 遵循命名规范
- [ ] 没有硬编码的值（应使用常量或配置）`,
  category: "项目规范",
  tags: ["coding-standards", "typescript", "naming-conventions", "best-practice"],
  priority: 2,
  global: false,
  scope: "personal"
})

// 返回示例：
// {
//   success: true,
//   data: {
//     code: "mem-project-coding-standards",
//     created_at: "2024-12-05T18:35:00Z"
//   }
// }
```

---

## 示例 5：数据库设计记录

### 场景描述

记录数据库表结构设计、索引策略和迁移脚本，便于团队理解数据模型。

### 实现方案

```javascript
// 创建数据库设计文档
memory_create({
  code: "mem-refresh-token-schema",
  title: "Refresh Token 表结构设计",
  content: `# Refresh Token 表结构设计

## 表设计

\`\`\`sql
CREATE TABLE refresh_tokens (
  id BIGSERIAL PRIMARY KEY,
  user_id BIGINT NOT NULL REFERENCES users(id) ON DELETE CASCADE,
  token_hash VARCHAR(255) NOT NULL UNIQUE,
  device_info VARCHAR(255),
  ip_address VARCHAR(45),
  expires_at TIMESTAMP NOT NULL,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  revoked_at TIMESTAMP,

  CONSTRAINT check_timestamp CHECK (created_at <= expires_at),

  INDEX idx_user_id (user_id),
  INDEX idx_token_hash (token_hash),
  INDEX idx_expires_at (expires_at),
  INDEX idx_revoked_at (revoked_at)
);
\`\`\`

## 字段说明

| 字段 | 类型 | 说明 |
|------|------|------|
| id | BIGSERIAL | 主键，自增 |
| user_id | BIGINT | 用户 ID，关联 users 表 |
| token_hash | VARCHAR(255) | Token 的哈希值（重要！不存储明文） |
| device_info | VARCHAR(255) | 设备信息（可选），用于多设备管理 |
| ip_address | VARCHAR(45) | 登录的 IP 地址，用于安全审计 |
| expires_at | TIMESTAMP | Token 过期时间 |
| created_at | TIMESTAMP | Token 创建时间，默认当前时间 |
| revoked_at | TIMESTAMP | Token 撤销时间，登出时设置 |

## 索引策略

### 必要索引
1. **idx_user_id**: 快速查询用户的所有 Token
2. **idx_token_hash**: 验证 Token 时快速查询
3. **idx_expires_at**: 定期清理过期 Token

### 可选索引
1. **idx_revoked_at**: 查询已撤销的 Token，用于审计

## 数据库迁移脚本

### 创建表
\`\`\`sql
-- 创建 Refresh Token 表
CREATE TABLE refresh_tokens (
  id BIGSERIAL PRIMARY KEY,
  user_id BIGINT NOT NULL REFERENCES users(id) ON DELETE CASCADE,
  token_hash VARCHAR(255) NOT NULL UNIQUE,
  device_info VARCHAR(255),
  ip_address VARCHAR(45),
  expires_at TIMESTAMP NOT NULL,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  revoked_at TIMESTAMP,

  CONSTRAINT check_timestamp CHECK (created_at <= expires_at)
);

-- 创建索引
CREATE INDEX idx_user_id ON refresh_tokens(user_id);
CREATE INDEX idx_token_hash ON refresh_tokens(token_hash);
CREATE INDEX idx_expires_at ON refresh_tokens(expires_at);
CREATE INDEX idx_revoked_at ON refresh_tokens(revoked_at);

-- 创建清理过期 Token 的触发器（可选）
CREATE OR REPLACE FUNCTION cleanup_expired_tokens()
RETURNS void AS $$
BEGIN
  DELETE FROM refresh_tokens WHERE expires_at < NOW();
END;
$$ LANGUAGE plpgsql;
\`\`\`

### 定期清理任务
\`\`\`bash
# 在 cron 中添加每天凌晨2点执行清理
0 2 * * * psql -d mydb -c "DELETE FROM refresh_tokens WHERE expires_at < NOW();"
\`\`\`

## 使用示例

### 存储 Token
\`\`\`javascript
async function storeRefreshToken(userId: string, refreshToken: string) {
  const tokenHash = await bcrypt.hash(refreshToken, 10)

  await db.query(
    'INSERT INTO refresh_tokens (user_id, token_hash, expires_at, device_info, ip_address) ' +
    'VALUES ($1, $2, $3, $4, $5)',
    [
      userId,
      tokenHash,
      new Date(Date.now() + 7 * 24 * 60 * 60 * 1000),  // 7天后
      req.headers['user-agent'],
      req.ip
    ]
  )
}
\`\`\`

### 验证 Token
\`\`\`javascript
async function verifyRefreshToken(userId: string, refreshToken: string) {
  const result = await db.query(
    'SELECT token_hash FROM refresh_tokens ' +
    'WHERE user_id = $1 AND expires_at > NOW() AND revoked_at IS NULL',
    [userId]
  )

  if (result.rows.length === 0) return false

  return await bcrypt.compare(refreshToken, result.rows[0].token_hash)
}
\`\`\`

### 撤销 Token
\`\`\`javascript
async function revokeRefreshToken(userId: string, tokenHash: string) {
  await db.query(
    'UPDATE refresh_tokens SET revoked_at = NOW() WHERE user_id = $1 AND token_hash = $2',
    [userId, tokenHash]
  )
}
\`\`\`

## 性能优化

### 查询优化
- 使用索引快速查询
- 定期清理过期数据
- 使用连接池提高并发

### 容量规划
- 假设每用户平均 5 个 Token（多设备）
- 100万用户 × 5 Token ≈ 500万条记录
- 每条记录约 500 字节
- 总容量约 2.5GB

## 安全考虑

1. **绝不存储明文 Token**: 使用 bcrypt 哈希
2. **使用 HTTPS**: 传输 Token 时必须加密
3. **设置合理的过期时间**: 权衡安全和用户体验
4. **实现 Token 黑名单**: 支持主动撤销
5. **记录 IP 和设备**: 支持异常检测`,
  category: "数据库设计",
  tags: ["database", "auth", "schema", "migration", "sql"],
  priority: 2,
  global: true,
  scope: "personal"
})

// 返回示例：
// {
//   success: true,
//   data: {
//     code: "mem-refresh-token-schema",
//     created_at: "2024-12-05T19:00:00Z"
//   }
// }
```

---

## 快速实践指南

### 1. 创建架构决策记忆

```javascript
memory_create({
  code: "mem-<技术选型>-decision",
  title: "<技术> vs <技术> 选型分析",
  content: `# 选型分析\n\n## 背景\n...\n## 优缺点对比\n...\n## 决策依据\n...`,
  category: "架构决策",
  tags: ["decision", "architecture"],
  priority: 3,
  global: true
})
```

### 2. 记录问题解决方案

```javascript
memory_create({
  code: "mem-<问题>-solution",
  title: "<问题> 排查和修复指南",
  content: `# 问题解决指南\n\n## 问题描述\n...\n## 根因分析\n...\n## 解决方案\n...`,
  category: "问题排查",
  tags: ["debug", "lesson-learned"],
  priority: 3,
  global: false
})
```

### 3. 保存代码片段

```javascript
memory_create({
  code: "mem-<功能>-implementation",
  title: "<功能> 实现示例",
  content: "```javascript\n// 代码...\n```\n\n## 使用示例\n...",
  category: "代码示例",
  tags: ["code-snippet", "example"],
  priority: 2,
  global: true
})
```

### 4. 搜索相关知识

```javascript
// 搜索所有 JWT 相关的记忆
memory_search({
  keyword: "JWT",
  scope: "global"
})

// 搜索私有项目记忆
memory_search({
  keyword: "认证",
  scope: "personal"
})
```

### 5. 更新和维护

```javascript
// 发现新信息后更新
memory_update({
  code: "mem-xxx",
  content: "更新的内容...",
  priority: 3
})

// 定期审查和清理过时记忆
memory_delete({ code: "mem-old-outdated-info" })
```

---

呀~ 这就是 Memory 工具的实际应用示例！希望这些例子能帮助你更好地管理项目知识！(´∀`)💖
