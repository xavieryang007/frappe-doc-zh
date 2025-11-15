# RazorPay集成

## 概述

RazorPay是一个流行的支付网关，Frappe框架支持与RazorPay的无缝集成，使您能够轻松处理在线支付事务。

> **注意**：由于RazorPay集成文档链接重定向到其他页面，完整的集成指南可能需要参考Frappe官方文档的更新版本或RazorPay官方集成文档。

## 基本功能

- **支付处理**：支持多种支付方式，包括信用卡、借记卡、网上银行等
- **退款管理**：处理支付退款操作
- **支付状态跟踪**：实时监控支付状态
- **安全认证**：符合PCI DSS安全标准

## 配置步骤

### 1. 获取RazorPay凭据

1. 登录RazorPay商户后台
2. 获取API密钥和API密钥密码
3. 配置Webhook用于支付状态回调

### 2. Frappe配置

导航至：**首页 > 集成 > RazorPay设置**

- **API密钥**：输入从RazorPay获取的API密钥
- **API密钥密码**：输入对应的密钥密码
- **Webhook密钥**：配置Webhook验证密钥
- **启用测试模式**：开发环境使用测试模式

### 3. 支付配置

- **支付超时设置**：配置支付会话超时时间
- **货币设置**：支持多种货币支付
- **支付方式限制**：配置可用的支付方式

## 使用指南

### 创建支付请求

```python
# 示例代码
payment_request = frappe.new_doc("Payment Request")
payment_request.payment_gateway = "RazorPay"
payment_request.amount = 1000
payment_request.reference_doctype = "Sales Invoice"
payment_request.reference_name = "SI-001"
payment_request.save()
```

### 处理支付回调

RazorPay支付完成后，通过Webhook回调通知Frappe系统：

- 验证Webhook签名
- 更新支付状态
- 触发相关业务逻辑

## 最佳实践

1. **安全性**：
   - 定期轮换API密钥
   - 验证所有Webhook请求
   - 使用HTTPS连接

2. **错误处理**：
   - 实现重试机制
   - 记录详细的错误日志
   - 设置监控告警

3. **合规性**：
   - 遵守当地支付法规
   - 实施适当的退款政策
   - 保持审计日志

## 故障排除

### 常见问题

- **支付失败**：检查网络连接和API配额
- **Webhook未触发**：验证Webhook配置和网络可达性
- **签名验证失败**：检查密钥配置和时间同步

### 日志查看

集成日志位于：**首页 > 集成 > RazorPay日志**

---

**提示**：完整的RazorPay集成指南建议参考最新的Frappe官方文档和RazorPay开发者文档。