# 开发者API (Developer API)

## 概述
当前页面为Frappe框架的**开发者API文档入口**，主要分为Python、JavaScript及其他API三大类。页面提供了各个API模块的快速链接，方便开发者按需查阅。

## 主要内容结构

### Python API
- **核心功能**：提供操作文档、数据库、模板渲染等常用方法，所有功能集中在`frappe`命名空间下。
- **关键子模块**：
  - [Document API](https://docs.frappe.io/framework/v14/user/en/api/document) – 文档对象操作（增删改查）。
  - [Database API](https://docs.frappe.io/framework/v14/user/en/api/database) – 数据库查询与事务管理。
  - [Jinja API](https://docs.frappe.io/framework/v14/user/en/api/jinja) – 模板渲染工具。
  - [REST API](https://docs.frappe.io/framework/v14/user/en/api/rest) – 支持基于Token或OAuth的API认证与数据操作。
  - 其他工具：全文检索、对话框、查询构建器等。

### JavaScript API
- **适用场景**：仅限Frappe桌面端（Desk）使用，通过`window.frappe`全局对象调用。
- **核心功能**：
  - [Form API](https://docs.frappe.io/framework/v14/user/en/api/form) – 表单字段控制与事件处理。
  - [Server Calls](https://docs.frappe.io/framework/v14/user/en/api/server-calls) – 异步请求（AJAX）至后端。
  - [Dialog API](https://docs.frappe.io/framework/v14/user/en/api/dialog) – 弹窗交互组件。
  - 图表、扫描器等扩展工具。

### 其他API
- 包含REST API与Jinja模板的补充说明。

## 使用建议
- **Python开发**：直接导入`frappe`模块，参考对应子模块文档。
- **前端开发**：在浏览器控制台中调试`frappe`对象，结合文档中的JavaScript API链接深入学习。
- **快速检索**：利用页面左侧导航栏（如"Python API""JS API"）快速定位具体功能。

## 注意事项
- 部分链接指向版本`v14`的文档，若使用其他版本需调整路径。
- JavaScript API仅适用于Frappe桌面环境，不适用于纯前端项目。