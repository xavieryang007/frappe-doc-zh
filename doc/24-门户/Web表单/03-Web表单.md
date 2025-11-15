# Web表单 (Web Form)

## 24.1 Web表单

### 概述
Frappe 框架提供了一种简单的方法来为网站生成表单，只需少量配置即可。这些表单可以是公开的（任何人都可以填写），也可以配置为需要登录才能访问。

### 创建 Web Form
要创建 Web Form，请按照以下步骤操作：
1. 在 Awesome Bar 中输入 `new web form` 并按 Enter。
2. 填写表单标题。
3. 选择要创建记录的 DocType。
4. 可选的：添加介绍文本。
5. 点击 **"Get Fields"** 按钮，系统会自动从所选 DocType 中获取所有字段，你也可以手动选择字段。
6. 发布表单，即可使用。

### 标准 Web Forms
- 如果勾选 **"Is Standard"** 复选框，系统会在 Web Form 所属的模块中创建一个新文件夹。
- 该文件夹包含 `.py` 和 `.js` 文件，可用于配置 Web Form。
- 这些文件需要与你的自定义应用一起纳入版本控制，并可以在任何站点上安装。
- **注意**：仅当处于开发者模式时，"Is Standard" 字段才会显示。

### 自定义 Web Form
页面提供了 **"Customize Web Form"** 选项，允许进一步调整表单的外观和行为。

### 其他相关功能
- **Web Form 设置**：可以配置路由、隐藏侧边栏、预览和保存草稿等。
- **权限管理**：支持设置表单的访问权限（公开或需登录）。
- **版本控制**：页面支持查看修订记录和管理更改。

### 相关链接
- **Portal - Web Form**：包含以下子章节：
  - Web Form
  - Settings
  - Customization
- **Guides - Portal Development**：包含与 Web Form 相关的指南