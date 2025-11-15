# Frappe框架教程 (Frappe Framework Tutorial)

## 概述

Frappe框架是一个用于构建Web应用的全栈框架，基于Python、JavaScript和Redis等技术。本教程以构建一个简单的"图书馆管理系统"为例，引导开发者从零开始创建应用。

## 核心章节与关键概念

### 1. 创建Bench

Bench是Frappe框架的开发环境管理工具，用于管理多个应用（Apps）和站点（Sites）。
- 通过Bench可以初始化开发环境、安装依赖、管理应用生命周期。

### 2. 创建应用（App）

应用是Frappe中的功能模块，可以独立开发、安装或卸载。
- 使用Bench命令创建新应用，例如：`bench new-app library_management`。
- 应用目录结构包含Doctype、网页视图、脚本、配置等核心文件。

### 3. 创建站点（Site）

站点是应用的部署实例，每个站点有独立的数据库和配置。
- 通过Bench命令创建站点：`bench new-site library.test`。
- 站点可以绑定域名、配置HTTPS，并支持多租户架构。

### 4. 创建DocType

DocType是Frappe的核心数据模型，用于定义数据结构（类似数据库表）。
- 支持的DocType类型包括：
  - **主文档类型**（如Article、Library Member）
  - **子表类型**（Child DocType，用于嵌套数据）
  - **单文档类型**（Single DocType，用于存储全局设置）
- 通过可视化界面或JSON文件定义字段、权限、行为。

### 5. DocType功能扩展

- 可配置字段类型（文本、链接、日期等）、校验规则、默认值。
- 支持权限控制（基于角色设置可读/写权限）。
- 提供版本记录（Audit Trail）和数据归档功能。

### 6. 控制器方法（Controller Methods）

在DocType的Python控制器中编写业务逻辑，例如验证数据、计算字段、触发工作流。
- 支持事件钩子（如`before_save`、`after_insert`）。

### 7. 表单脚本（Form Scripts）

使用JavaScript为前端表单添加动态行为，如字段联动、实时校验。
- 可通过客户端脚本（Client Script）或服务器端脚本（Server Script）实现。

### 8. 网页视图（Web View Pages）

为外部用户提供公开访问的页面（如图书馆会员查看可借阅文章）。
- 使用Jinja模板引擎渲染页面，支持动态数据加载。

### 9. 后续步骤

- 部署到生产环境（配置数据库、HTTPS、定时任务）。
- 扩展功能：集成REST API、打印格式、报表、移动端支持。

## 关键总结

- **应用（App）**是功能模块的容器，**站点（Site）**是运行实例。
- **DocType**是数据模型的核心，支持灵活定义结构和行为。
- 教程通过实战项目（图书馆管理）串联起环境搭建、数据建模、权限控制和前后端交互的全流程。

如果需要具体操作步骤或代码示例，可以进一步查阅教程中的对应章节（如"Create a DocType""Form Scripts"）。