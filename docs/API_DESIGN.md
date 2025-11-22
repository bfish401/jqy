# JQY API 设计规范

## API 概览

### 基础信息
- **协议**: REST + JSON
- **基础 URL**: `http://your-domain/api/v1`
- **认证**: JWT Bearer Token
- **版本管理**: URL 路径版本化 (v1, v2, ...)

## 请求规范

### 通用请求头
```http
GET /api/v1/tools HTTP/1.1
Host: your-domain
Content-Type: application/json
Authorization: Bearer <jwt_token>
X-Request-ID: unique-request-id
User-Agent: JQY-Client/1.0
```

### 分页参数
```json
GET /api/v1/tools?page=1&per_page=20&sort=-created_at

参数:
- page: 页码 (default: 1)
- per_page: 每页数量 (default: 20, max: 100)
- sort: 排序字段 (+ 升序, - 降序)
```

### 过滤参数
```json
GET /api/v1/tools?status=enabled&category=database

支持的操作符:
- 相等: ?field=value
- 不等: ?field!=value
- 包含: ?field~=value
- 大于: ?field>value
- 小于: ?field<value
- 在范围内: ?field[]=val1&field[]=val2
```

## 响应规范

### 成功响应 (2xx)

#### 单个资源
```json
{
  "code": 200,
  "message": "success",
  "data": {
    "id": "tool-001",
    "name": "生产 MySQL",
    "type": "database",
    "status": "enabled",
    "created_at": "2024-01-01T10:00:00Z",
    "updated_at": "2024-01-15T15:30:00Z"
  },
  "timestamp": "2024-01-16T12:00:00Z",
  "request_id": "req-uuid-here"
}
```

#### 列表资源（带分页）
```json
{
  "code": 200,
  "message": "success",
  "data": [
    { "id": "tool-001", "name": "MySQL", ... },
    { "id": "tool-002", "name": "PostgreSQL", ... }
  ],
  "pagination": {
    "page": 1,
    "per_page": 20,
    "total": 150,
    "total_pages": 8,
    "has_next": true,
    "has_prev": false
  },
  "timestamp": "2024-01-16T12:00:00Z",
  "request_id": "req-uuid-here"
}
```

### 错误响应 (4xx, 5xx)

```json
{
  "code": 400,
  "message": "Invalid request parameters",
  "error": {
    "type": "validation_error",
    "details": [
      {
        "field": "email",
        "message": "Invalid email format"
      },
      {
        "field": "password",
        "message": "Password must be at least 8 characters"
      }
    ]
  },
  "timestamp": "2024-01-16T12:00:00Z",
  "request_id": "req-uuid-here"
}
```

### HTTP 状态码映射

```
200 OK              - 请求成功
201 Created         - 资源创建成功
204 No Content      - 删除成功，无返回内容
400 Bad Request     - 请求参数错误
401 Unauthorized    - 未认证或 Token 过期
403 Forbidden       - 权限不足
404 Not Found       - 资源不存在
409 Conflict        - 资源冲突
422 Unprocessable   - 请求处理失败
429 Too Many        - 请求过于频繁
500 Server Error    - 服务器内部错误
503 Unavailable     - 服务不可用
```

## API 端点设计

### 认证相关 (Auth)

#### 登陆
```http
POST /api/v1/auth/login
Content-Type: application/json

{
  "username": "user@example.com",
  "password": "password123",
  "auth_type": "ldap"  // or "local"
}

响应:
{
  "code": 200,
  "data": {
    "token": "eyJhbGc...",
    "expires_in": 3600,
    "user": {
      "id": "user-001",
      "username": "user",
      "email": "user@example.com",
      "avatar": "https://...",
      "roles": ["user"]
    }
  }
}
```

#### 刷新 Token
```http
POST /api/v1/auth/refresh
Authorization: Bearer <expired_token>

响应:
{
  "code": 200,
  "data": {
    "token": "eyJhbGc...",
    "expires_in": 3600
  }
}
```

#### 登出
```http
POST /api/v1/auth/logout
Authorization: Bearer <token>

响应:
{
  "code": 200,
  "message": "Logged out successfully"
}
```

#### 获取当前用户信息
```http
GET /api/v1/auth/me
Authorization: Bearer <token>

响应:
{
  "code": 200,
  "data": {
    "id": "user-001",
    "username": "admin",
    "email": "admin@example.com",
    "avatar": "https://...",
    "roles": ["admin"],
    "permissions": ["user:create", "user:update", ...]
  }
}
```

### 工具/资源管理 (Tools)

#### 获取工具列表
```http
GET /api/v1/tools?page=1&per_page=20&status=enabled
Authorization: Bearer <token>

响应: 分页列表
```

#### 获取工具详情
```http
GET /api/v1/tools/{tool_id}
Authorization: Bearer <token>

响应:
{
  "code": 200,
  "data": {
    "id": "tool-001",
    "name": "生产 MySQL",
    "type": "database",
    "description": "主数据库",
    "status": "enabled",
    "owner": {
      "id": "user-001",
      "username": "admin"
    },
    "category": "database",
    "config": {
      "host": "192.168.1.1",
      "port": 3306,
      "username": "root"
    },
    "permissions": {
      "users": ["user-001", "user-002"],
      "groups": ["group-001"]
    },
    "created_at": "2024-01-01T10:00:00Z",
    "updated_at": "2024-01-15T15:30:00Z"
  }
}
```

#### 创建工具
```http
POST /api/v1/tools
Authorization: Bearer <token>
Content-Type: application/json

{
  "name": "新工具",
  "type": "database",
  "description": "工具描述",
  "category": "database",
  "config": {
    "host": "192.168.1.1",
    "port": 3306
  }
}

响应: 201 Created
```

#### 更新工具
```http
PUT /api/v1/tools/{tool_id}
Authorization: Bearer <token>
Content-Type: application/json

{
  "name": "更新后的名称",
  "status": "disabled"
}

响应: 200 OK
```

#### 删除工具
```http
DELETE /api/v1/tools/{tool_id}
Authorization: Bearer <token>

响应: 204 No Content
```

### 用户管理 (Users)

#### 获取用户列表
```http
GET /api/v1/users?page=1&per_page=20&role=user
Authorization: Bearer <token>

响应: 分页列表
```

#### 创建用户
```http
POST /api/v1/users
Authorization: Bearer <token>
Content-Type: application/json

{
  "username": "newuser",
  "email": "newuser@example.com",
  "password": "password123",
  "roles": ["user"],
  "groups": ["group-001"]
}

响应: 201 Created
```

#### 批量分配权限
```http
PUT /api/v1/users/{user_id}/permissions
Authorization: Bearer <token>
Content-Type: application/json

{
  "tools": ["tool-001", "tool-002"],
  "groups": ["group-001"]
}

响应: 200 OK
```

### 权限管理 (Permissions)

#### 获取权限列表
```http
GET /api/v1/permissions?resource_type=tool
Authorization: Bearer <token>

响应:
{
  "code": 200,
  "data": [
    {
      "id": "perm-001",
      "resource_type": "tool",
      "action": "create",
      "description": "创建工具"
    },
    {
      "id": "perm-002",
      "resource_type": "tool",
      "action": "read",
      "description": "查看工具"
    },
    ...
  ]
}
```

### 审计日志 (Audit)

#### 获取审计日志
```http
GET /api/v1/audits?user_id=user-001&action=login&page=1
Authorization: Bearer <token>

响应:
{
  "code": 200,
  "data": [
    {
      "id": "audit-001",
      "timestamp": "2024-01-16T12:00:00Z",
      "user_id": "user-001",
      "username": "admin",
      "action": "login",
      "resource_type": "auth",
      "resource_id": null,
      "result": "success",
      "ip": "192.168.1.100",
      "user_agent": "Mozilla/5.0..."
    }
  ],
  "pagination": {
    "page": 1,
    "per_page": 20,
    "total": 1024,
    "total_pages": 52
  }
}
```

## 认证机制

### JWT Token 结构
```
Header:
{
  "alg": "HS256",
  "typ": "JWT"
}

Payload:
{
  "user_id": "user-001",
  "username": "admin",
  "roles": ["admin"],
  "permissions": ["user:*", "tool:*"],
  "exp": 1705416000,
  "iat": 1705412400,
  "iss": "jqy",
  "aud": "jqy-client"
}

Signature:
HMACSHA256(base64UrlEncode(header) + "." + base64UrlEncode(payload), secret)
```

### 请求认证流程
```
1. 用户调用 POST /api/v1/auth/login
2. 后端验证凭证（LDAP 或本地）
3. 生成 JWT Token
4. 返回 Token 给客户端
5. 客户端在后续请求的 Authorization header 中包含 Token
6. 后端验证 Token 的有效性
7. 如果 Token 过期，调用 POST /api/v1/auth/refresh 获取新 Token
```

## 速率限制

### 限制规则
```
默认限制: 1000 请求/小时/IP
认证端点: 10 请求/分钟/IP
文件上传: 100 MB/请求
```

### 限制响应
```http
HTTP/1.1 429 Too Many Requests
X-RateLimit-Limit: 1000
X-RateLimit-Remaining: 5
X-RateLimit-Reset: 1705416000

{
  "code": 429,
  "message": "Rate limit exceeded",
  "retry_after": 60
}
```

## 错误代码

### 通用错误代码

| 代码 | 含义 | 说明 |
|-----|------|------|
| 400 | BAD_REQUEST | 请求参数错误 |
| 401 | UNAUTHORIZED | 未认证或 Token 过期 |
| 403 | FORBIDDEN | 权限不足 |
| 404 | NOT_FOUND | 资源不存在 |
| 409 | CONFLICT | 资源已存在 |
| 422 | UNPROCESSABLE | 无法处理的请求 |
| 429 | RATE_LIMIT | 请求过于频繁 |
| 500 | INTERNAL_ERROR | 服务器错误 |
| 503 | UNAVAILABLE | 服务不可用 |

## 最佳实践

### 1. 版本管理
- 在 URL 中使用版本号: `/api/v1/`, `/api/v2/`
- 向后兼容性：保留旧版本 API 至少 6 个月

### 2. 文档化
- 使用 OpenAPI 3.0 / Swagger 规范
- 提供详细的错误说明
- 包含请求和响应示例

### 3. 日志记录
- 记录所有 API 调用
- 包含请求 ID 便于追踪
- 敏感信息脱敏（密码、token等）

### 4. 安全
- 使用 HTTPS 加密传输
- 验证所有输入
- 实现 CORS 跨域策略
- 防止 SQL 注入和 XSS

### 5. 性能优化
- 实现缓存机制
- 使用分页避免一次性加载过多数据
- 提供字段过滤选项
- 使用 ETag 和 Last-Modified

### 6. 幂等性
- 创建资源时使用 POST
- 更新资源时使用 PUT (完整) 或 PATCH (部分)
- 删除资源时使用 DELETE
- 支持 Idempotency-Key 防止重复请求

## API 客户端库

### Python
```python
import requests

response = requests.get(
    'http://your-domain/api/v1/tools',
    headers={
        'Authorization': 'Bearer <token>',
        'Content-Type': 'application/json'
    },
    params={'page': 1, 'per_page': 20}
)

data = response.json()
```

### JavaScript
```javascript
const response = await fetch('http://your-domain/api/v1/tools', {
  method: 'GET',
  headers: {
    'Authorization': `Bearer ${token}`,
    'Content-Type': 'application/json'
  },
  params: { page: 1, per_page: 20 }
});

const data = await response.json();
```

### cURL
```bash
curl -X GET "http://your-domain/api/v1/tools?page=1&per_page=20" \
  -H "Authorization: Bearer <token>" \
  -H "Content-Type: application/json"
```

## 更多资源

- [OpenAPI 3.0 规范](https://spec.openapis.org/oas/v3.0.3)
- [RESTful API 最佳实践](https://restfulapi.net/)
- [API 安全指南](https://owasp.org/www-project-api-security/)
