# Memory CLI 实践示例

这个文档展示了如何在实际项目中使用 Memory CLI 命令，包括 4 个完整的使用场景。

---

## 示例 1：架构决策记录 - JWT 选型

### 场景描述

项目需要选择认证方式，团队讨论后决定采用 JWT + Refresh Token 方案。需要记录这个架构决策，方便未来新成员理解设计思路。

### 决策背景

```
时间：2024年1月15日
讨论参与者：架构师、后端负责人、安全顾问
备选方案：
1. 传统 Session + Cookie（旧方案）
2. JWT + Refresh Token（新方案）
3. OAuth 2.0（第三方方案）
```

### CLI 命令执行

```bash
./main memory create \
  --code "mem-jwt-choice" \
  --title "JWT 选型决策" \
  --content "# JWT 选型决策

## 背景

项目重构用户认证系统，需要选择合适的令牌机制。经过团队讨论，最终选择 JWT + Refresh Token 方案。

## 备选方案评比

### 方案 1：Session + Cookie（传统方案）
✅ 优点：
- 安全性高（token 无法被盗）
- 实现简单
- 支持会话管理

❌ 缺点：
- 需要服务器端存储
- 水平扩展复杂（需要共享 session）
- 跨域不友好

### 方案 2：JWT + Refresh Token（最终选择）
✅ 优点：
- 无状态（无需服务器存储）
- 易于水平扩展
- 天然支持微服务架构
- 支持跨域和跨域请求

⚠️ 缺点：
- Token 一旦签发无法立即撤销
- 需要妥善管理密钥
- Token 大小较大

### 方案 3：OAuth 2.0
✅ 优点：
- 行业标准
- 支持第三方集成
- 灵活的授权流程

❌ 缺点：
- 实现复杂
- 依赖第三方服务
- 对内部系统来说过度设计

## 最终决策：JWT + Refresh Token

**选择原因：**
1. 微服务架构需求：未来可能拆分多个服务，需要无状态认证
2. 移动应用支持：原生 App 对 cookie 支持有限
3. 跨域需求：前端可能跨域部署
4. 开发效率：库支持完善，实现快速

## 实现方案

### Token 设计

\`\`\`
Access Token:
- 有效期：15 分钟
- 载荷：user_id, email, roles
- 用途：验证用户身份，访问受保护资源

Refresh Token:
- 有效期：7 天
- 载荷：user_id, token_version
- 用途：刷新 access token
- 存储：httpOnly cookie（防止 XSS）
\`\`\`

### 密钥管理

\`\`\`
- JWT 密钥存储在环境变量：JWT_SECRET
- Refresh 密钥单独存储：REFRESH_SECRET
- 支持密钥轮换（通过 token_version）
- 生产环境使用 RSA 非对称密钥
\`\`\`

### 安全考虑

1. **密码安全**：使用 bcrypt (cost=12)
2. **防暴力破解**：登录失败 3 次锁定 15 分钟
3. **HTTPS Only**：生产环境强制 HTTPS
4. **CSRF 保护**：使用双重 cookie 提交
5. **XSS 防护**：Refresh token 使用 httpOnly cookie

## 参考资源

- [JWT 官方文档](https://jwt.io/)
- [Auth0 - JWT 最佳实践](https://auth0.com/blog/json-web-token-jwt-best-practices/)
- [OWASP 认证备忘单](https://cheatsheetseries.owasp.org/cheatsheets/Authentication_Cheat_Sheet.html)
- [RFC 7519 - JSON Web Token (JWT)](https://tools.ietf.org/html/rfc7519)

## 后续相关任务

- todo-design-auth-schema：数据库架构设计
- todo-implement-jwt：JWT 核心实现
- todo-auth-api-endpoints：API 端点开发
" \
  --category "架构设计" \
  --tags "认证,jwt,安全,架构设计"
```

### 查看已创建的记忆

```bash
# 列出所有记忆
./main memory list

# 搜索 JWT 相关的记忆
./main memory search --keyword "jwt"

# 查看完整决策文档
./main memory get --code "mem-jwt-choice"
```

### 预期输出

```
✅ 记忆创建成功！标识码: mem-jwt-choice, 标题: JWT 选型决策

[后续查看命令的输出...]

记忆详情:
Code:     mem-jwt-choice
Title:    JWT 选型决策
Category: 架构设计
Tags:     认证, jwt, 安全, 架构设计
Scope:    项目级别
Created:  2024-01-15 10:30:00

[完整的 Markdown 内容...]
```

---

## 示例 2：Bug 解决方案 - Redis 连接问题

### 场景描述

生产环境经常出现 Redis 连接超时错误，导致用户 Session 丢失。经过排查，发现是连接池配置不合理。记录这个解决方案，避免下次遇到同样问题。

### 问题时间线

```
14:30 - 用户报告无法登录
14:35 - 发现日志中大量 Redis 超时错误
14:50 - 排查发现连接池大小不足
15:20 - 调整配置后问题解决
```

### CLI 命令执行

```bash
./main memory create \
  --code "mem-redis-connection-timeout" \
  --title "Redis 连接超时问题解决方案" \
  --content "# Redis 连接超时问题解决方案

## 问题现象

### 症状
- 用户间歇性无法登录
- 日志显示大量 'Redis connection timeout' 错误
- 错误随着并发用户数增加而加剧
- 高峰期错误率达到 20%

### 影响范围
- Session 管理功能
- 缓存系统
- 分布式锁（如果有使用）

## 排查过程

### 第 1 步：查看日志（14:35）
\`\`\`
[ERROR] redis: i/o timeout after 10s idle
[ERROR] NOAUTH Authentication required
[ERROR] connection pool exhausted
\`\`\`

问题特征：
- 不是所有连接都失败，而是间歇性失败
- 主要发生在高并发时
- 超时时间相对固定（10s）

### 第 2 步：检查连接池配置（14:45）

原配置：
\`\`\`go
client := redis.NewClient(&redis.Options{
  Addr:         \"redis:6379\",
  Password:     os.Getenv(\"REDIS_PASSWORD\"),
  DB:           0,
  MaxRetries:   3,
  PoolSize:     10,        // ❌ 太小！
  MinIdleConns: 5,         // ❌ 太小！
})
\`\`\`

并发分析：
- 用户数：1000+ 并发
- 每个用户需要 2-3 个 Redis 连接
- 需要的连接数：2000-3000
- 当前池大小：10（严重不足！）

### 第 3 步：检查 Redis 服务器（14:50）
\`\`\`bash
# 查看 Redis 连接数
redis-cli INFO clients

# 输出示例：
# connected_clients:12
# blocked_clients:0
# maxclients:10000
\`\`\`

Redis 服务器健康，问题确实在客户端侧。

## 根本原因

连接池大小（10）远小于实际需求（2000+），导致：
1. 连接池快速耗尽
2. 后续请求等待可用连接
3. 等待超时，抛出异常
4. 用户请求失败

## 解决方案

### 方案 1：增加连接池大小（推荐）

\`\`\`go
client := redis.NewClient(&redis.Options{
  Addr:              \"redis:6379\",
  Password:          os.Getenv(\"REDIS_PASSWORD\"),
  DB:                0,
  MaxRetries:        3,
  PoolSize:          50,         // ✅ 增加到 50
  MinIdleConns:      20,         // ✅ 增加到 20
  MaxConnAge:        5 * time.Minute,
  PoolTimeout:       4 * time.Second,
  IdleTimeout:       5 * time.Minute,
  ReadTimeout:       3 * time.Second,
  WriteTimeout:      3 * time.Second,
})
\`\`\`

参数说明：
- **PoolSize**: 最大连接数，根据并发数调整
- **MinIdleConns**: 最少空闲连接数，减少连接创建延迟
- **MaxConnAge**: 连接最大生存期，防止连接泄漏
- **PoolTimeout**: 获取连接的超时时间
- **IdleTimeout**: 空闲连接超时
- **ReadTimeout/WriteTimeout**: 单个命令的超时时间

### 方案 2：连接池监控

\`\`\`go
// 定时监控连接池状态
ticker := time.NewTicker(30 * time.Second)
defer ticker.Stop()

for range ticker.C {
  stats := client.PoolStats()
  log.Printf(\"Redis pool - Hits: %d, Misses: %d, Timeouts: %d, Size: %d, Idle: %d\",
    stats.Hits, stats.Misses, stats.Timeouts, stats.TotalConns, stats.IdleConns)
}
\`\`\`

### 方案 3：降级策略

\`\`\`go
// 当 Redis 不可用时，使用本地缓存降级
func GetWithFallback(ctx context.Context, key string) (string, error) {
  // 先尝试 Redis
  val, err := client.Get(ctx, key).Result()
  if err == nil {
    return val, nil
  }

  // Redis 失败，使用本地缓存
  if err == redis.Nil {
    return getFromLocalCache(key)
  }

  // 其他错误也降级
  log.WithError(err).Warn(\"Redis error, using local cache\")
  return getFromLocalCache(key)
}
\`\`\`

## 实施步骤

1. **立即修复**（5 分钟）
   - 更新配置文件，增加 PoolSize 到 50
   - 重启应用服务

2. **性能调优**（1 小时）
   - 添加连接池监控
   - 分析实际连接需求

3. **长期方案**（1 天）
   - 考虑 Redis 集群或哨兵模式
   - 实现更完善的降级策略

## 验证结果

修改前后对比：

\`\`\`
修改前：
- 错误率：20%
- 平均响应时间：500ms
- 超时错误/秒：10-15

修改后：
- 错误率：0.5%（基本正常）
- 平均响应时间：50ms
- 超时错误/秒：0-1
\`\`\`

## 监控告警

添加告警规则：
\`\`\`
- Redis 连接超时 > 1/分钟 → 告警
- 连接池耗尽 → 立即告警
- 响应时间 > 100ms → 监控
\`\`\`

## 参考资源

- [go-redis 连接池文档](https://github.com/redis/go-redis)
- [Redis 连接管理最佳实践](https://redis.io/topics/client-side-caching)
- [连接池配置经验](https://github.com/redis/redis/issues)

## 教训总结

✅ **学到的要点：**
1. 连接池大小要根据实际并发数设置
2. 监控很重要，能及时发现问题
3. 有降级方案会更安全
4. 定期压测有助于发现隐患

⚠️ **改进建议：**
- 开发环境也应该进行并发测试
- 建立性能基准，定期对标
- 自动化告警和响应流程
" \
  --category "问题解决" \
  --tags "redis,连接,性能,troubleshooting"
```

### 查看记忆

```bash
# 搜索 Redis 问题
./main memory search --keyword "redis"

# 查看详细解决方案
./main memory get --code "mem-redis-connection-timeout"
```

---

## 示例 3：代码片段存储 - 常用配置模板

### 场景描述

团队使用多种框架和工具，每次启动新项目都要写基础配置。整理常用的配置模板作为 Memory，加速项目启动。

### CLI 命令执行

```bash
./main memory create \
  --code "mem-golang-config-template" \
  --title "Go 项目常用配置模板" \
  --content "# Go 项目常用配置模板

## 数据库连接配置

### PostgreSQL 连接池配置

\`\`\`go
import (
  \"database/sql\"
  _ \"github.com/lib/pq\"
)

// 初始化数据库连接
func initDB() (*sql.DB, error) {
  dsn := fmt.Sprintf(
    \"postgres://%s:%s@%s:%d/%s?sslmode=disable\",
    os.Getenv(\"DB_USER\"),
    os.Getenv(\"DB_PASSWORD\"),
    os.Getenv(\"DB_HOST\"),
    5432,
    os.Getenv(\"DB_NAME\"),
  )

  db, err := sql.Open(\"postgres\", dsn)
  if err != nil {
    return nil, err
  }

  // 连接池配置
  db.SetMaxOpenConns(25)        // 最大打开连接数
  db.SetMaxIdleConns(5)         // 最大空闲连接数
  db.SetConnMaxLifetime(5 * time.Minute)  // 连接最大生存期
  db.SetConnMaxIdleTime(10 * time.Minute) // 空闲连接超时

  // 测试连接
  if err := db.Ping(); err != nil {
    return nil, err
  }

  return db, nil
}
\`\`\`

### Redis 连接配置

\`\`\`go
import (
  \"github.com/redis/go-redis/v9\"
)

func initRedis() *redis.Client {
  return redis.NewClient(&redis.Options{
    Addr:              os.Getenv(\"REDIS_ADDR\"),
    Password:          os.Getenv(\"REDIS_PASSWORD\"),
    DB:                0,
    MaxRetries:        3,
    PoolSize:          50,
    MinIdleConns:      20,
    MaxConnAge:        5 * time.Minute,
    PoolTimeout:       4 * time.Second,
    IdleTimeout:        5 * time.Minute,
    ReadTimeout:       3 * time.Second,
    WriteTimeout:      3 * time.Second,
  })
}
\`\`\`

## HTTP 服务器配置

### 标准 HTTP 服务器

\`\`\`go
func setupHTTPServer(handler http.Handler) *http.Server {
  return &http.Server{
    Addr:         \":\" + os.Getenv(\"HTTP_PORT\"),
    Handler:      handler,
    ReadTimeout:  15 * time.Second,
    WriteTimeout: 15 * time.Second,
    IdleTimeout:  60 * time.Second,
    MaxHeaderBytes: 1 << 20, // 1MB
  }
}
\`\`\`

### 优雅关闭

\`\`\`go
func gracefulShutdown(server *http.Server, timeout time.Duration) error {
  ctx, cancel := context.WithTimeout(context.Background(), timeout)
  defer cancel()

  return server.Shutdown(ctx)
}
\`\`\`

## 日志配置

### Structured Logging

\`\`\`go
import (
  \"github.com/sirupsen/logrus\"
)

func setupLogger() *logrus.Logger {
  logger := logrus.New()
  logger.SetFormatter(&logrus.JSONFormatter{
    TimestampFormat: time.RFC3339Nano,
  })

  if os.Getenv(\"ENV\") == \"production\" {
    logger.SetLevel(logrus.InfoLevel)
  } else {
    logger.SetLevel(logrus.DebugLevel)
  }

  return logger
}
\`\`\`

## 环境变量配置

### .env 示例文件

\`\`\`bash
# 数据库
DB_HOST=localhost
DB_PORT=5432
DB_USER=postgres
DB_PASSWORD=password
DB_NAME=myapp

# Redis
REDIS_ADDR=localhost:6379
REDIS_PASSWORD=

# 应用
HTTP_PORT=8080
ENV=development

# JWT
JWT_SECRET=your-secret-key
JWT_EXPIRE=900

# 第三方服务
SENTRY_DSN=
SMTP_HOST=
SMTP_PORT=587
\`\`\`

### 配置加载

\`\`\`go
import (
  \"github.com/joho/godotenv\"
  \"os\"
)

func loadConfig() error {
  if err := godotenv.Load(\".env\"); err != nil {
    log.Warn(\"No .env file found\")
  }

  requiredVars := []string{\"DB_HOST\", \"DB_USER\", \"REDIS_ADDR\"}
  for _, v := range requiredVars {
    if os.Getenv(v) == \"\" {
      return fmt.Errorf(\"required env var not set: %s\", v)
    }
  }

  return nil
}
\`\`\`

## 中间件配置

### CORS 中间件

\`\`\`go
import \"github.com/rs/cors\"

func setupCORS() *cors.Cors {
  return cors.New(cors.Options{
    AllowedOrigins:   []string{\"*\"},
    AllowedMethods:   []string{\"GET\", \"POST\", \"PUT\", \"DELETE\"},
    AllowedHeaders:   []string{\"*\"},
    ExposedHeaders:   []string{\"Content-Length\"},
    MaxAge:           300,
    AllowCredentials: true,
  })
}
\`\`\`

### 请求日志中间件

\`\`\`go
func loggingMiddleware(logger *logrus.Logger) func(http.Handler) http.Handler {
  return func(next http.Handler) http.Handler {
    return http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
      start := time.Now()

      wrapped := &responseWriter{ResponseWriter: w, statusCode: http.StatusOK}
      next.ServeHTTP(wrapped, r)

      duration := time.Since(start)
      logger.WithFields(logrus.Fields{
        \"method\":   r.Method,
        \"path\":     r.RequestURI,
        \"status\":   wrapped.statusCode,
        \"duration\": duration.Milliseconds(),
      }).Info(\"HTTP request\")
    })
  }
}
\`\`\`

## Docker 配置

### Dockerfile 示例

\`\`\`dockerfile
FROM golang:1.21-alpine AS builder

WORKDIR /app
COPY go.mod go.sum ./
RUN go mod download

COPY . .
RUN CGO_ENABLED=0 GOOS=linux go build -a -installsuffix cgo -o main .

FROM alpine:latest
RUN apk --no-cache add ca-certificates

WORKDIR /root/
COPY --from=builder /app/main .

EXPOSE 8080
CMD [\"./main\"]
\`\`\`

### docker-compose 示例

\`\`\`yaml
version: '3.8'

services:
  api:
    build: .
    ports:
      - \"8080:8080\"
    environment:
      - DB_HOST=postgres
      - REDIS_ADDR=redis:6379
    depends_on:
      - postgres
      - redis

  postgres:
    image: postgres:15
    environment:
      - POSTGRES_PASSWORD=password
      - POSTGRES_DB=myapp
    volumes:
      - postgres_data:/var/lib/postgresql/data

  redis:
    image: redis:7
    ports:
      - \"6379:6379\"

volumes:
  postgres_data:
\`\`\`

## 测试配置

### 单元测试模板

\`\`\`go
import (
  \"testing\"
  \"github.com/stretchr/testify/assert\"
)

func TestUserService(t *testing.T) {
  // 准备
  service := NewUserService()

  // 执行
  user, err := service.GetUser(1)

  // 断言
  assert.NoError(t, err)
  assert.NotNil(t, user)
  assert.Equal(t, \"John\", user.Name)
}
\`\`\`

### 集成测试（Docker Compose）

\`\`\`go
func setupTestDB() *sql.DB {
  // 使用 docker-compose 启动的 PostgreSQL
  dsn := \"postgres://test:test@localhost:5432/test_db?sslmode=disable\"

  var db *sql.DB
  var err error

  // 重试连接
  for i := 0; i < 10; i++ {
    db, err = sql.Open(\"postgres\", dsn)
    if err == nil && db.Ping() == nil {
      break
    }
    time.Sleep(time.Second)
  }

  return db
}
\`\`\`

## 快速应用

所有这些配置可以通过以下方式快速应用到新项目：

1. 查看此 Memory：\`./main memory get --code mem-golang-config-template\`
2. 复制所需的代码片段
3. 根据项目需求调整参数
4. 测试确认无误后提交

## 相关 Memory

- mem-error-handling：错误处理最佳实践
- mem-graceful-shutdown：优雅关闭模式
- mem-testing-strategy：测试策略
" \
  --category "代码片段" \
  --tags "golang,配置,模板,最佳实践"
```

### 使用模板

```bash
# 查看 Go 配置模板
./main memory get --code "mem-golang-config-template"

# 搜索所有配置相关的记忆
./main memory search --keyword "template"
./main memory search --keyword "配置"
```

---

## 示例 4：项目规范 - 代码风格约定

### 场景描述

新成员加入项目，需要了解团队的代码风格规范。整理成 Memory，便于新成员快速上手，也方便代码审查时参考。

### CLI 命令执行

```bash
./main memory create \
  --code "mem-code-style-guide" \
  --title "项目代码风格约定" \
  --content "# 项目代码风格约定

## 概述

本文档定义了本项目的代码风格、命名规范、注释规范等规范要求。所有贡献者应严格遵守这些规范。

## 为什么需要规范

✅ **规范的好处：**
- 代码易读性高：统一的风格让新人快速理解
- 维护成本低：风格统一减少理解成本
- Code Review 高效：减少关于风格的讨论
- Bug 更少：规范化的代码结构减少错误
- 团队协作顺畅：减少沟通成本

## Go 代码规范

### 文件和包结构

\`\`\`
myapp/
├── cmd/
│   └── main.go              # 应用程序入口
├── internal/
│   ├── app/
│   │   └── app.go           # 应用程序主逻辑
│   ├── api/
│   │   ├── handlers/        # HTTP 处理器
│   │   └── middleware/      # 中间件
│   ├── repository/          # 数据访问层
│   ├── service/             # 业务逻辑层
│   └── models/              # 数据模型
├── pkg/                      # 可导出的包
├── config/                   # 配置文件
├── migrations/              # 数据库迁移
└── tests/                    # 测试
\`\`\`

### 命名规范

**包名**
\`\`\`
✅ 建议：
- 全小写：user, product, auth
- 简洁有意义：不使用 pkg_, types_ 前缀
- 避免下划线和驼峰

❌ 避免：
- pkg_user, types_user
- userService (应该用包组织)
- utils_helper
\`\`\`

**函数名**
\`\`\`
✅ 建议：
- 使用驼峰命名：GetUser, CreateOrder
- 导出函数大写开头：func GetUser()
- 私有函数小写开头：func getUser()
- 使用清晰的动词：Get, Create, Update, Delete, List

示例：
- GetUserByID(id int) (*User, error)
- CreateUser(ctx context.Context, req *CreateUserReq) (*User, error)
- DeleteUser(ctx context.Context, id int) error
\`\`\`

**变量名**
\`\`\`
✅ 建议：
- 简洁但有意义：user 而不是 u
- 缩写谨慎使用：i, j, k（循环变量）可用，其他避免
- 避免通用词：data, result, value（除非必要）
- 错误变量用 err：err := DoSomething()

示例：
- user := &User{}           ✅ 清晰
- usr := &User{}            ❌ 不必要缩写
- u := &User{}              ❌ 太模糊
- id := user.ID             ✅ 清晰
- uid := user.ID            ❌ 不必要缩写
\`\`\`

**常量和类型**
\`\`\`
✅ 建议：
- 常量全大写，单词用下划线分隔：MAX_RETRY_TIMES
- 类型名使用驼峰：type User struct { ... }

示例：
- const MAX_TIMEOUT = 30
- type UserService struct { ... }
\`\`\`

### 代码格式

**缩进和行长**
\`\`\`
- 使用 tab 缩进（不是空格）
- 一行代码不超过 100 个字符
- 使用 gofmt 格式化代码
- 使用 goimports 整理导入

工具配置：
\`\`\`bash
# VSCode settings.json
{
  \"[go]\": {
    \"editor.formatOnSave\": true,
    \"editor.defaultFormatter\": \"golang.go\"
  }
}
\`\`\`
\`\`\`

**函数长度**
\`\`\`
- 优先保持函数短小（建议 < 30 行）
- 复杂逻辑拆分成多个小函数
- 每个函数一个职责

示例：
\`\`\`go
// ❌ 太长，职责混乱
func ProcessUser(id int) (*User, error) {
  user := getFromDB(id)
  validateUser(user)
  updateCache(user)
  sendNotification(user)
  logAction(user)
  return user, nil
}

// ✅ 职责清晰，易于测试
func GetUser(id int) (*User, error) {
  return getFromDB(id)
}

func ValidateAndCache(user *User) error {
  if err := validateUser(user); err != nil {
    return err
  }
  return updateCache(user)
}
\`\`\`
\`\`\`

### 错误处理

\`\`\`
✅ 规范做法：

// 立即返回错误，不隐藏
if err != nil {
  return err
}

// 使用 errors.New 或 fmt.Errorf 创建自定义错误
if user == nil {
  return errors.New(\"user not found\")
}

// 对错误进行上下文注解
if err != nil {
  return fmt.Errorf(\"failed to get user %d: %w\", id, err)
}

❌ 避免做法：
- 忽略错误：result, _ := DoSomething()
- panic 用于错误处理
- 返回 (nil, nil)
- 模糊的错误信息
\`\`\`

### 注释规范

\`\`\`
✅ 规范做法：
- 导出的函数必须有注释
- 注释用英文，且以 // 开头
- 注释应说明 why 而非 what

// GetUser retrieves a user by ID from the database.
// It returns ErrUserNotFound if the user does not exist.
func GetUser(ctx context.Context, id int) (*User, error) {
  ...
}

❌ 避免做法：
- 无用的注释：get user（代码已说明）
- 中文注释混用英文代码
- 过时的注释
- 代码前面的大量注释（应该使代码更清晰）
\`\`\`

## 代码审查清单

提交 PR 前，检查以下项目：

\`\`\`
□ 代码风格
  □ gofmt 已运行
  □ 函数名符合命名规范
  □ 变量名清晰

□ 错误处理
  □ 所有错误都被检查
  □ 错误信息清晰
  □ 使用 errors.Is/As 进行错误判断

□ 函数质量
  □ 函数长度合理（< 30 行）
  □ 每个函数只有一个职责
  □ 导出的函数有注释

□ 测试
  □ 新功能有单元测试
  □ 测试覆盖率 > 80%
  □ 测试名清晰，说明测试场景

□ 文档
  □ 复杂逻辑有注释解释
  □ API 改动有文档更新
  □ README 已更新（如需要）
\`\`\`

## 常见错误和改进

### 错误 1：变量名太模糊

\`\`\`go
// ❌ 不好
res, err := getUser()
if err != nil {
  return err
}

// ✅ 好
user, err := getUser()
if err != nil {
  return fmt.Errorf(\"failed to get user: %w\", err)
}
\`\`\`

### 错误 2：函数职责不清

\`\`\`go
// ❌ 不好：函数做了太多事情
func ProcessOrderAndNotify(order *Order) error {
  // 保存订单
  // 更新库存
  // 发送邮件
  // 记录日志
  // ...
}

// ✅ 好：职责明确
func SaveOrder(ctx context.Context, order *Order) error {
  // 只负责保存
}

func UpdateInventory(ctx context.Context, order *Order) error {
  // 只负责库存
}

func NotifyCustomer(ctx context.Context, order *Order) error {
  // 只负责通知
}
\`\`\`

### 错误 3：忽略上下文

\`\`\`go
// ❌ 不好
func GetUser(id int) (*User, error) {
  return database.Query(\"SELECT * FROM users WHERE id = ?\", id)
}

// ✅ 好
func GetUser(ctx context.Context, id int) (*User, error) {
  return database.QueryContext(ctx, \"SELECT * FROM users WHERE id = ?\", id)
}
\`\`\`

## 工具和自动化

### 必需工具

\`\`\`bash
go install github.com/golangci/golangci-lint/cmd/golangci-lint@latest
golangci-lint run ./...
\`\`\`

### Git 钩子

在 .git/hooks/pre-commit：
\`\`\`bash
#!/bin/bash
gofmt -w .
go vet ./...
\`\`\`

### CI/CD 检查

在 GitHub Actions 中运行：
\`\`\`yaml
- name: Lint
  run: golangci-lint run ./...

- name: Test
  run: go test -v -cover ./...
\`\`\`

## 相关文档

- [Effective Go](https://golang.org/doc/effective_go)
- [Go Code Review Comments](https://github.com/golang/go/wiki/CodeReviewComments)
- [本项目代码审查流程](./code-review-process.md)

## 更新日志

- 2024-01-15：创建初始版本
- 2024-01-20：添加 CI/CD 检查部分
" \
  --category "项目规范" \
  --tags "代码风格,golang,规范,最佳实践"
```

### 应用规范

```bash
# 新成员入职，查看规范
./main memory get --code "mem-code-style-guide"

# 搜索特定规范
./main memory search --keyword "命名规范"
./main memory search --keyword "错误处理"
```

---

## 使用建议总结

### 什么时候创建 Memory

| 场景 | 优先级 | 示例 |
|------|--------|------|
| 架构决策 | 🔴 高 | JWT vs Session, 数据库选型 |
| Bug 解决方案 | 🟠 中 | Redis 连接问题, 并发 Bug |
| 代码片段 | 🟡 中 | 配置模板, 中间件代码 |
| 项目规范 | 🟠 中 | 代码风格, Git 工作流 |
| 踩坑记录 | 🟢 低 | 临时性的问题解决 |

### 最佳实践

```
✅ DO:
- 使用 Markdown 格式化内容
- 包含参考链接和关键文献
- 定期审视和更新过时内容
- 鼓励团队成员添加新 Memory

❌ DON'T:
- 记录过于简短的信息（一句话不值得记录）
- 重复记录类似内容
- 记录个人临时笔记
- 在 Memory 中存储敏感信息（密钥、密码）
```

### 查询技巧

```bash
# 列出所有记忆，了解知识库规模
./main memory list

# 按关键词搜索
./main memory search --keyword "auth"

# 搜索分类（通过分类字段）
./main memory search --keyword "架构设计"

# 获取特定记忆的完整内容
./main memory get --code "mem-jwt-choice"
```

---

这 4 个示例展示了 Memory 系统的完整应用能力，从架构决策、问题解决、代码复用到团队规范，都能有效地构建和维护项目知识库！
