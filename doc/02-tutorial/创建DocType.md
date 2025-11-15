# 创建DocType (Create a DocType)

## 🧩 创建 DocType 教程摘要

### 1. **启用开发者模式**

在创建 DocType 之前，需确保在 bench 环境中启用开发者模式，以便生成相关代码文件并纳入版本控制。

**操作步骤：**
```bash
# 停止 bench 服务（如正在运行）
bench set-config -g developer_mode true
bench start
```

### 2. **进入 DocType 列表**

- 登录 Frappe Desk，使用顶部搜索栏（Awesomebar）输入"DocType List"并进入。
- 点击 **New** 创建新的 DocType。

### 3. **创建 Article DocType**

教程中以"Article"为例，具体配置如下：

| 字段名 | 类型 | 必填/选项 |
|--------|------|-----------|
| Article Name | Data | ✅ 必填 |
| Image | Attach Image | 可选 |
| Author | Data | 可选 |
| Description | Text Editor | 可选 |
| ISBN | Data | 可选 |
| Status | Select | 选项：Issued、Available |
| Publisher | Data | 可选 |

**操作提示：**
- 在"Module"字段选择"Library Management"（如已创建对应模块）。
- 字段顺序将直接影响表单布局。
- 完成后点击 **Save**。

### 4. **验证创建结果**

保存后系统将自动生成：
- **数据库表**：`tabArticle`，包含所有定义字段及系统标准字段（如 `name`, `creation`, `modified` 等）。
- **表单视图**（Form View）和**列表视图**（List View）。
- **前端/后端代码文件**（位于应用目录下）：
  - `article.json`：定义 DocType 结构
  - `article.js`：客户端控制器
  - `article.py`：Python 控制器
  - `test_article.py`：单元测试模板

> ⚠️ **注意**：创建 DocType 时请确保未勾选"Custom？"选项，否则不会生成代码文件。

### 5. **测试功能**

- 在列表中点击 **New** 创建第一条 Article 记录。
- 如未显示"New"按钮，请点击右上角 **Settings → Reload** 刷新缓存。

## 🔍 关键要点总结

- **DocType 是 Frappe 中的核心数据模型**，不仅定义数据库结构，还控制前后端行为。
- **启用开发者模式**是生成代码文件的前提。
- 创建完成后，系统会自动生成**数据库表、视图、以及Python/JS代码模板**，方便进一步定制。

如需继续了解 DocType 的高级功能（如字段类型、权限控制、表单脚本等），可查阅后续章节《DocType Features》或《Controller Methods》。