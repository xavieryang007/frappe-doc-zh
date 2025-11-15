# Google Drive集成

## 概述

Google Drive集成允许您在Frappe应用中直接连接和使用Google Drive服务，实现文件存储、同步和协作功能。

## 配置步骤

### 1. 启用Google Drive集成

- 导航至：**首页 > 集成 > Google Drive设置**
- 勾选"启用"复选框以激活集成功能

### 2. API凭证配置

- **客户端ID**：输入从Google Cloud Console获取的OAuth 2.0客户端ID
- **客户端密钥**：输入对应的客户端密钥
- **重定向URI**：配置为`[您的域名]/api/method/frappe.integrations.doctype.google_drive_settings.google_drive_settings.google_callback`

### 3. 权限范围设置

需要配置以下Google API权限：
- `https://www.googleapis.com/auth/drive.file`（访问和管理特定文件）
- `https://www.googleapis.com/auth/drive.metadata.readonly`（读取文件元数据）

### 4. 文件夹映射

- **默认存储文件夹**：设置Google Drive中用于存储Frappe文件的默认文件夹
- **同步设置**：配置自动同步频率和冲突解决策略

## 功能特性

### 文件操作

- **上传文件**：直接将文件从Frappe上传到Google Drive
- **下载文件**：从Google Drive下载文件到Frappe系统
- **文件同步**：保持Frappe和Google Drive之间的文件同步
- **版本控制**：支持文件版本管理和历史记录追踪

### 权限管理

- 继承Google Drive的共享和权限设置
- 支持团队协作和文件共享
- 可配置访问权限级别（查看、评论、编辑）

### 搜索功能

- 在Google Drive中搜索文件和文件夹
- 支持按文件名、类型、修改日期等条件筛选

## 使用指南

### 首次配置

1. 在Google Cloud Console创建项目并启用Drive API
2. 配置OAuth同意屏幕
3. 创建凭据并获取客户端ID和密钥
4. 在Frappe中完成上述配置步骤

### 日常使用

- 在文档附件中选择"保存到Google Drive"
- 通过文件管理器查看和管理Google Drive文件
- 设置自动备份和同步规则

## 故障排除

### 常见问题

- **认证失败**：检查客户端ID和密钥是否正确
- **权限错误**：验证API权限范围配置
- **同步失败**：检查网络连接和API配额

### 日志查看

- 集成日志位于：**首页 > 集成 > Google Drive日志**
- 可查看详细的API调用记录和错误信息

## 最佳实践

1. 定期检查API使用配额
2. 配置适当的文件保留策略
3. 使用服务账号进行服务器间通信
4. 实施适当的备份和灾难恢复方案

## 注意事项

- 需要稳定的网络连接以确保同步功能正常
- 大文件上传可能需要较长时间
- 建议在生产环境使用前进行充分测试