# 开发优先级和里程碑规划

## 📍 ALPHA 版本 (4-6 周) - MVP 功能集

**目标**: 可运行的最小可行产品，包含核心认证和管理功能

### 第 1 周: 框架和基础设施

#### 后端任务
```
优先级 P1 (必须):
  ✅ Flask 项目结构搭建
     - 目录结构: app/, tests/, config/, migrations/
     - 配置管理: config.py (开发/生产)
     - 日志配置: logging.py
     预期: 2 小时
  
  ✅ SQLAlchemy ORM 配置
     - 数据库连接池
     - 模型基类
     - 关系定义
     预期: 3 小时
  
  ✅ 数据库模型设计和迁移
     - User 表 (用户)
     - Role 表 (角色)
     - Permission 表 (权限)
     - Tool 表 (工具)
     - AuditLog 表 (审计日志)
     - 初始化数据 (admin 角色)
     预期: 4 小时
     
     数据库初始化脚本:
     python scripts/init_db.py --mode development
  
  ✅ 测试框架配置
     - pytest 配置
     - 测试数据库
     - Mock 配置
     预期: 2 小时

优先级 P2 (重要):
  ⚙️ API 基础框架
     - 蓝图系统
     - 错误处理
     - 请求验证 (Marshmallow/Pydantic)
     预期: 3 小时
```

#### 前端任务
```
优先级 P1 (必须):
  ✅ Vue.js 项目搭建 (Vite)
     - 项目目录结构
     - 路由配置
     - 状态管理 (Pinia)
     - 国际化 (i18n) - 中英文
     预期: 4 小时
  
  ✅ 布局框架
     - App.vue 主框架
     - 响应式导航
     - 深色模式切换
     预期: 2 小时
  
  ✅ 组件库配置
     - Element Plus 或 Ant Design
     - 全局样式
     - 变量定义 (主色、字体等)
     预期: 2 小时
  
  ✅ HTTP 客户端
     - axios 配置
     - API 基础类
     - Token 管理
     - 错误处理
     预期: 2 小时

优先级 P2 (重要):
  ⚙️ 页面框架
     - 登陆页面框架
     - 仪表板框架
     - 工具管理页面框架
     预期: 3 小时
```

#### 共同任务
```
优先级 P1 (必须):
  ✅ Docker 环境
     - docker-compose.dev.yml 确认
     - 开发环境快速启动脚本
     - Dockerfile.dev 验证
     预期: 2 小时
  
  ✅ CI/CD 流程
     - GitHub Actions 配置
     - 自动化测试流程
     - 代码质量检查 (pylint/flake8)
     预期: 3 小时
  
  ✅ Git 工作流
     - .gitignore 完善
     - branch 策略 (main, develop, feature/*)
     - Commit 消息规范
     预期: 1 小时
  
  ✅ 代码规范
     - Python: PEP8 + black + isort
     - JavaScript: ESLint + Prettier
     预期: 1 小时

第 1 周总计: 38 小时 (约 5 个开发日)
```

---

### 第 2 周: 用户认证

#### 后端任务
```
优先级 P1 (必须):
  ✅ JWT 认证框架
     - Token 生成和验证
     - Token 刷新机制
     - Token 黑名单 (可选)
     - 加密和签名
     预期: 4 小时
     
     API 端点:
     POST /api/v1/auth/login
       body: { username, password, auth_type: 'ldap'|'local' }
       response: { access_token, refresh_token, user_info }
     
     POST /api/v1/auth/refresh
       body: { refresh_token }
       response: { access_token }
     
     POST /api/v1/auth/logout
       response: { message: 'ok' }
  
  ✅ 本地认证模块
     - 密码哈希 (bcrypt)
     - 密码验证
     - 账户锁定机制 (3 次错误后 30 分钟)
     - 登陆日志记录
     预期: 4 小时
     
     SQL:
     CREATE TABLE login_attempts (
       id INTEGER PRIMARY KEY,
       user_id INTEGER,
       attempt_time DATETIME,
       success BOOLEAN
     );
  
  ✅ LDAP 认证模块 (V1.0 最低要求)
     - LDAP 连接配置
     - 用户查询
     - 密码验证
     - 错误处理
     - 诊断工具
     预期: 6 小时
     
     配置参数:
     LDAP_SERVER_URI = "ldap://ldap.example.com:389"
     LDAP_BIND_DN = "cn=admin,dc=example,dc=com"
     LDAP_BASE_DN = "ou=users,dc=example,dc=com"
     LDAP_USER_SEARCH_FILTER = "(uid=%s)"
  
  ✅ 权限装饰器
     - @login_required 装饰器
     - @role_required 装饰器
     - @permission_required 装饰器
     预期: 2 小时

优先级 P2 (重要):
  ⚙️ 用户信息端点
     GET /api/v1/auth/me
       response: { user_id, username, roles, permissions }
     预期: 1 小时
```

#### 前端任务
```
优先级 P1 (必须):
  ✅ 登陆页面
     - 标签页: LDAP 登陆 | 本地登陆
     - 用户名/密码 输入框
     - 记住我功能 (可选)
     - 验证提示和错误信息
     - 加载动画
     预期: 4 小时
     
     UI 规范: 
     - 页面居中，宽度 400px
     - 按钮宽度 100%
     - 输入框高度 40px
     - 字体大小 14px
     - 颜色: 主色 #1890FF
  
  ✅ Token 管理
     - 本地存储 (localStorage 或 sessionStorage)
     - Token 自动刷新
     - 登陆状态检查
     - 自动登出 (token 过期)
     预期: 3 小时
  
  ✅ 路由守卫
     - 页面权限检查
     - 未登陆跳转到登陆页
     - 权限不足提示
     预期: 2 小时
  
  ✅ 用户信息显示
     - 右上角用户名显示
     - 用户菜单 (个人中心、退出登陆)
     - 角色标签显示
     预期: 2 小时

优先级 P2 (重要):
  ⚙️ 登陆失败处理
     - 重试限制提示
     - 账户锁定提示
     - 密码重置链接 (可选)
     预期: 2 小时
```

#### 共同任务
```
优先级 P1 (必须):
  ✅ 集成测试
     - 本地登陆流程测试
     - LDAP 登陆流程测试
     - Token 刷新测试
     - 权限检查测试
     预期: 4 小时
  
  ✅ 文档更新
     - 认证流程文档
     - LDAP 配置指南
     - 本地认证配置
     预期: 2 小时

第 2 周总计: 36 小时 (约 5 个开发日)
```

---

### 第 3 周: 核心功能

#### 后端任务
```
优先级 P1 (必须):
  ✅ 仪表板 API
     GET /api/v1/dashboard
       response: {
         stats: {
           total_tools: 10,
           total_users: 50,
           recent_logins: 5
         },
         activities: [ { timestamp, user, action } ]
       }
     预期: 3 小时
  
  ✅ 工具管理 API
     GET /api/v1/tools - 列表 (分页)
     POST /api/v1/tools - 创建
     GET /api/v1/tools/{id} - 详情
     PUT /api/v1/tools/{id} - 更新
     DELETE /api/v1/tools/{id} - 删除
     预期: 6 小时
     
     字段: id, name, description, type, host, port, protocol, owner_id
  
  ✅ 用户管理 API (基础)
     GET /api/v1/users - 列表 (只有 admin 可见)
     GET /api/v1/users/{id} - 详情
     POST /api/v1/users - 创建 (admin)
     预期: 4 小时
     
     字段: id, username, email, status, auth_type, created_at
  
  ✅ 权限管理 API (基础)
     GET /api/v1/permissions - 权限列表
     PUT /api/v1/users/{id}/roles - 设置角色
     PUT /api/v1/users/{id}/permissions - 设置权限
     预期: 4 小时
  
  ✅ 审计日志 API
     GET /api/v1/audits - 列表 (分页、过滤)
       filters: user_id, action, resource_type, date_range
     预期: 3 小时
     
     自动记录:
     - 用户登陆/登出
     - 资源创建/修改/删除
     - 权限变更

优先级 P2 (重要):
  ⚙️ 搜索功能
     - 工具搜索
     - 用户搜索
     预期: 2 小时
  
  ⚙️ 导出功能
     - CSV 导出
     - 审计日志导出
     预期: 2 小时
```

#### 前端任务
```
优先级 P1 (必须):
  ✅ 仪表板页面
     - 统计卡片 (工具数、用户数、活跃度)
     - 最近活动列表
     - 状态图表 (工具在线/离线)
     预期: 4 小时
     
     图表库: Chart.js 或 ECharts
  
  ✅ 工具管理页面
     - 工具列表 (表格)
     - 创建工具表单
     - 编辑工具表单
     - 删除确认对话框
     预期: 6 小时
     
     表格列: 名称, 类型, 主机, 端口, 所有者, 创建时间, 操作
  
  ✅ 用户管理页面 (管理员)
     - 用户列表
     - 用户详情
     - 创建用户表单
     预期: 4 小时
  
  ✅ 权限管理页面 (管理员)
     - 权限矩阵 (用户 x 权限)
     - 批量设置权限
     预期: 4 小时
  
  ✅ 审计日志页面
     - 日志列表 (表格、分页)
     - 过滤条件 (日期、用户、操作类型)
     - 导出功能
     预期: 4 小时

优先级 P2 (重要):
  ⚙️ 搜索和过滤优化
     预期: 2 小时
  
  ⚙️ 表单验证
     预期: 2 小时
```

#### 共同任务
```
优先级 P1 (必须):
  ✅ 性能测试
     - API 响应时间测试 (目标 < 500ms)
     - 数据库查询优化
     - 索引添加
     预期: 3 小时
  
  ✅ 集成测试
     - CRUD 操作测试
     - 权限检查测试
     - 审计日志测试
     预期: 4 小时
  
  ✅ 文档更新
     - API 文档更新
     - 用户使用指南
     预期: 2 小时

第 3 周总计: 46 小时 (约 6 个开发日)
```

---

### 第 4 周: 完善和测试

#### 任务
```
优先级 P1 (必须):
  ✅ UI/UX 优化
     - 响应式设计验证 (移动/平板/桌面)
     - 加载状态优化
     - 错误提示完善
     - 深色模式测试
     预期: 4 小时
  
  ✅ 性能优化
     - 前端: 代码分割、懒加载、缓存
     - 后端: 数据库优化、缓存、索引
     预期: 4 小时
  
  ✅ 安全加固
     - HTTPS 配置
     - CORS 配置
     - SQL 注入防护验证
     - XSS 防护验证
     - CSRF 防护
     预期: 3 小时
  
  ✅ 测试覆盖
     - 单元测试覆盖率 > 80%
     - 集成测试覆盖关键路径
     - E2E 测试 (Cypress 或 Playwright)
     预期: 6 小时
  
  ✅ 生产部署准备
     - Docker 镜像构建和测试
     - 配置管理验证
     - 数据备份方案
     - 监控告警配置 (可选)
     预期: 3 小时
  
  ✅ 文档完善
     - README 更新 (使用说明)
     - API 文档 (OpenAPI/Swagger)
     - 部署文档
     - 运维手册
     预期: 3 小时
  
  ✅ Bug 修复
     - 测试发现的 bug 修复
     - 边界情况处理
     预期: 4 小时

优先级 P2 (重要):
  ⚙️ 用户反馈处理
     - 早期用户反馈
     - 小范围 Beta 测试
     预期: 2 小时

第 4 周总计: 29 小时 (约 4 个开发日)
```

---

## 📊 里程碑总结

### ALPHA 版本总计

```
总工作量: 149 小时 (约 19 个开发日)
建议周期: 4-5 周 (2-3 个开发人员)

按角色分配:

后端开发 (1 人):
  第 1 周: 12 小时 (框架、数据库、测试框架)
  第 2 周: 16 小时 (认证、本地、LDAP、权限)
  第 3 周: 17 小时 (API 开发)
  第 4 周: 10 小时 (优化、测试、文档)
  小计: 55 小时

前端开发 (1 人):
  第 1 周: 10 小时 (项目搭建、框架、工具链)
  第 2 周: 11 小时 (登陆页面、Token 管理)
  第 3 周: 22 小时 (页面开发)
  第 4 周: 10 小时 (优化、测试)
  小计: 53 小时

共同任务 (1 人):
  第 1 周: 10 小时 (Docker、CI/CD、规范)
  第 2 周: 6 小时 (集成测试、文档)
  第 3 周: 10 小时 (性能测试、文档)
  第 4 周: 9 小时 (完善、部署)
  小计: 35 小时

QA / 测试 (0.5 人):
  第 2-4 周: 6 小时

DevOps (0.5 人):
  第 1, 4 周: 3 小时
```

### 预期交付成果

```
✅ 可运行的 MVP 应用
  - 用户认证 (本地 + LDAP)
  - 仪表板和工具管理
  - 用户和权限管理
  - 审计日志记录

✅ 完整的 API 文档
  - 30+ 个已实现的 API 端点

✅ 部署工件
  - Docker 镜像 (生产和开发)
  - docker-compose 配置
  - 初始化脚本

✅ 文档和指南
  - 用户使用指南
  - API 文档
  - 部署运维手册
  - 开发贡献指南

✅ 测试和质量
  - 单元测试 (>80% 覆盖)
  - 集成测试
  - 性能测试报告
```

---

## 🎯 成功标准

```
功能完成度:
  ✅ 所有 ALPHA 功能完成并可用
  ✅ 无 P1 级 Bug
  ✅ 权限控制正确
  ✅ 审计日志完整

性能指标:
  ✅ API 响应时间 < 500ms (99%)
  ✅ 页面加载时间 < 3s
  ✅ 支持 100+ 并发用户
  ✅ 数据库查询 < 100ms

质量指标:
  ✅ 单元测试覆盖率 >= 80%
  ✅ E2E 测试覆盖关键路径
  ✅ 代码审查通过
  ✅ 无安全漏洞

文档完整:
  ✅ API 文档 100% 覆盖
  ✅ 用户指南完成
  ✅ 部署指南完成
  ✅ 开发指南完成
```

---

## 📅 建议时间表

```
周一-周五第 1 周:
  ✅ 完成所有框架搭建
  ✅ 数据库初始化
  ✅ CI/CD 流程就绪

周一-周五第 2 周:
  ✅ 认证功能完成
  ✅ 登陆页面完成
  ✅ 用户信息 API 完成

周一-周五第 3 周:
  ✅ 核心功能 API 完成
  ✅ 前端页面完成
  ✅ 基本集成测试通过

周一-周五第 4 周:
  ✅ 性能优化完成
  ✅ 安全加固完成
  ✅ 文档更新完成
  ✅ 生产部署测试通过

周一 第 5 周:
  ✅ 最终 Bug 修复和冒烟测试
  ✅ ALPHA 版本发布
```

---

## ⚠️ 风险和缓解

```
风险 1: LDAP 集成延期
  -> 缓解: 优先完成本地认证，LDAP 作为可选
  -> 备选: 使用默认 LDAP 配置模板

风险 2: 前后端不同步
  -> 缓解: 明确的 API 接口定义在第 1 周完成
  -> 备选: 使用 Mock API 支持并行开发

风险 3: 性能问题发现太晚
  -> 缓解: 第 3 周进行性能测试
  -> 备选: 性能优化纳入第 4 周

风险 4: 人员不足
  -> 缓解: 优先级明确，可减少功能
  -> 备选: 推迟非核心功能到 BETA
```

---

## 🔗 关键依赖

```
必须先完成:
  ✅ 技术栈决策 (前端框架、组件库、ORM)
  ✅ 团队人员到位
  ✅ 开发环境搭建

应该准备:
  ✅ LDAP 服务器 (测试环境)
  ✅ Git 仓库和分支策略
  ✅ CI/CD 环境

可以并行:
  ✅ UI/UX 设计评审
  ✅ API 接口评审
  ✅ 数据库设计评审
```

这是开发的**最小化可行路线**，严格按照这个计划应该能在 4-5 周内交付 ALPHA 版本！
