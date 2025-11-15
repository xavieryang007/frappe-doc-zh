# Microsoft Office365 邮件OAuth

## 概述

Microsoft Office365邮件OAuth集成允许Frappe应用安全地连接到Office365邮件服务，实现邮件收发、日历同步等功能。

> **注意**：由于Microsoft Office365邮件OAuth集成文档链接重定向到其他页面，完整的集成指南可能需要参考Frappe官方文档的更新版本。

## 基本功能

- **邮件收发**：通过Office365账户发送和接收邮件
- **日历同步**：同步Office365日历事件
- **联系人管理**：访问和管理Office365联系人
- **安全认证**：使用OAuth 2.0协议进行安全认证

## 配置步骤

### 1. Azure应用注册

1. 登录Azure门户 (portal.azure.com)
2. 注册新应用或使用现有应用
3. 配置应用重定向URI
4. 获取客户端ID和客户端密钥

### 2. API权限配置

需要配置以下Microsoft Graph API权限：
- `Mail.ReadWrite` - 读写邮件权限
- `Mail.Send` - 发送邮件权限
- `Calendars.ReadWrite` - 读写日历权限
- `Contacts.ReadWrite` - 读写联系人权限

### 3. Frappe配置

导航至：**首页 > 集成 > Microsoft Office365设置**

- **客户端ID**：输入从Azure应用注册获取的客户端ID
- **客户端密钥**：输入对应的客户端密钥
- **租户ID**：输入Office365租户ID（或使用"common"）
- **重定向URI**：配置为`[您的域名]/api/method/frappe.integrations.doctype.microsoft_oauth_settings.microsoft_oauth_settings.microsoft_callback`

### 4. 邮件服务配置

- **默认发件人**：设置默认的发件人地址
- **SMTP设置**：配置Office365 SMTP服务器设置
- **同步频率**：设置邮件和日历同步频率

## 使用指南

### 首次连接

1. 在Frappe中配置Microsoft Office365设置
2. 点击"连接Office365"按钮
3. 使用Office365账户登录授权
4. 授权Frappe应用访问所需权限

### 邮件功能

- **发送邮件**：通过Frappe的邮件模板发送邮件
- **接收邮件**：自动同步收件箱邮件
- **邮件归档**：将邮件归档到特定文件夹

### 日历功能

- **事件同步**：同步Office365日历事件到Frappe
- **提醒设置**：配置事件提醒通知
- **会议安排**：通过Frappe安排Office365会议

## 最佳实践

1. **安全性**：
   - 定期轮换客户端密钥
   - 使用条件访问策略
   - 启用多因素认证

2. **性能优化**：
   - 合理设置同步频率
   - 使用增量同步减少API调用
   - 监控API使用配额

3. **错误处理**：
   - 实现重试机制
   - 记录详细的错误日志
   - 设置监控告警

## 故障排除

### 常见问题

- **认证失败**：检查客户端ID、密钥和权限配置
- **权限不足**：验证API权限是否已正确授予
- **连接超时**：检查网络连接和防火墙设置

### 日志查看

集成日志位于：**首页 > 集成 > Microsoft Office365日志**

---

**提示**：完整的Microsoft Office365邮件OAuth集成指南建议参考最新的Frappe官方文档和Microsoft Graph API文档。