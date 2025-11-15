# OAuth2

## 概述

Frappe框架使用`oauthlib`库来管理OAuth2需求。一个Frappe实例可以充当以下三种角色：

1. **资源服务器（Resource Server）**：存储资源，例如DocType中的数据。
2. **授权服务器（Authorization Server）**：颁发访问令牌以访问资源。
3. **客户端（Client）**：需要访问资源服务器上资源的应用程序。

## 相关DocType

根据角色，Frappe提供了以下DocType：

### 1. 通用（Common）

- **OAuth Settings**：用于配置与OAuth2相关的功能。

### 2. 授权服务器（Authorization Server）

- **OAuth Client**：记录已注册的客户端信息。
- **OAuth Bearer Token**：存储颁发给已注册客户端的令牌。
- **OAuth Authorization Code**：跟踪客户端用于交换令牌的授权码。
- **OAuth Provider Settings**：允许跳过授权（已弃用，建议使用OAuth Settings）。

### 3. 客户端（Client）

- **Connected App**：记录Frappe实例作为客户端注册的授权服务器信息，用于访问资源（例如用户的Google Drive账户）。
- **Social Login Key**：类似于Connected App，但用于登录目的（例如"使用Google登录"）。
- **Token Cache**：存储Frappe实例访问Connected App时收到的令牌。

## 扩展功能

Frappe框架在`oauthlib`基础上实现了以下额外功能：

- **动态客户端注册（Dynamic Client Registration）**：允许客户端无需手动配置即可注册（遵循RFC 7591）。
- **授权服务器元数据发现（Authorization Server Metadata Discovery）**：允许客户端查看授权服务器的元数据（如认证端点，遵循RFC 8414）。
- **资源服务器元数据发现（Resource Server Metadata Discovery）**：允许客户端查看资源服务器的元数据（如文档、授权服务器等，遵循RFC 9728）。

## OAuth设置（OAuth Settings）

OAuth Settings是一个单例DocType，用于配置OAuth2相关功能。建议直接查看该DocType页面，每个字段都有详细的帮助文本。

### 主要配置选项

- **跳过授权（Skip Authorization）**：当存在有效令牌时跳过授权检查（生产环境中不建议启用）。
- **显示授权服务器元数据（Show Auth Server Metadata）**：启用授权服务器元数据发现功能。
- **启用动态客户端注册（Enable Dynamic Client Registration）**：允许动态注册客户端。
- **显示受保护资源元数据（Show Protected Resource Metadata）**：启用资源服务器元数据发现功能。

### 其他配置

其余字段（在"资源"部分）仅用于响应`/.well-known/oauth-protected-resource`请求。

> **关于公共客户端（Public Clients）**：
> 公共客户端（如单页应用SPA）默认受到CORS限制。如需为受信任的客户端绕过此限制，可在"允许的公共客户端来源（Allowed Public Client Origins）"字段中添加其主机名。

## 相关文档链接

- [如何设置OAuth 2？](https://docs.frappe.io/framework/guides/integration/how-to-setup-oauth-2)
- [OAuth 2](https://docs.frappe.io/framework/integration/rest-api/oauth-2)
- [基于令牌的认证](https://docs.frappe.io/framework/integration/rest-api/token-based-authentication)
- [使用Frappe作为OAuth服务](https://docs.frappe.io/framework/guides/integration/using-frappe-as-oauth-service)
- [Social Login Key](https://docs.frappe.io/framework/guides/integration/social-login-key)
- [Connected App](https://docs.frappe.io/framework/guides/integration/connected-app)

> **注意**：部分文档可能已过时，建议在实际使用时参考代码确认。

## 使用场景

### 1. Frappe作为授权服务器

当您希望其他应用能够访问您的Frappe实例数据时：

1. 配置OAuth Settings
2. 创建OAuth Client记录
3. 为客户端颁发访问令牌
4. 客户端使用令牌访问API

### 2. Frappe作为客户端

当您希望Frappe应用能够访问外部服务时：

1. 创建Connected App或Social Login Key
2. 配置外部服务的OAuth2设置
3. 用户授权后获取访问令牌
4. 使用令牌访问外部API

### 3. 社交登录

使用Social Login Key实现第三方登录：

- 支持Google、Facebook、GitHub等平台
- 简化用户注册和登录流程
- 自动创建用户账户

## 安全最佳实践

1. **令牌管理**：
   - 使用短期访问令牌
   - 实施令牌刷新机制
   - 定期撤销不必要的令牌

2. **客户端安全**：
   - 验证客户端身份
   - 限制客户端权限范围
   - 监控异常API调用

3. **通信安全**：
   - 始终使用HTTPS
   - 验证重定向URI
   - 实施适当的CORS策略

## 故障排除

### 常见问题

- **令牌无效**：检查令牌是否过期或已被撤销
- **权限不足**：验证客户端权限范围
- **重定向URI不匹配**：确保配置的URI与请求一致

### 调试工具

- 查看OAuth日志：**首页 > 集成 > OAuth日志**
- 检查令牌状态：**首页 > 集成 > OAuth令牌**
- 监控API调用：系统日志和错误日志

---

**总结**：Frappe框架提供了完整的OAuth2实现，支持多种使用场景和高级功能，能够满足现代应用的安全认证需求。