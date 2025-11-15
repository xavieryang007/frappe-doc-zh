# 控件 (Controls)

## 概述
该页面属于 Frappe Framework 官方文档的"控件（Controls）"章节，主要介绍了如何在 Frappe 框架中创建和使用各种表单控件。

## `frappe.ui.form.make_control` 方法
- **功能**：根据字段定义（`df`）动态创建 Frappe 控件，并添加到指定父容器中。
- **参数说明**：
  - `parent`：控件插入的父级容器（jQuery 对象）。
  - `df`：字段定义对象，包含控件的属性（如标签、字段名、类型等）。
  - `render_input`（可选）：是否渲染输入区域。

- **示例代码**：
  ```javascript
  frappe.ui.form.make_control({
    parent: $wrapper.find('.my-control'),
    df: {
      label: 'Due Date',
      fieldname: 'due_date',
      fieldtype: 'Date'
    },
    render_input: true
  });
  ```

## 支持的控件类型及字段定义（`df`）示例
页面详细列出了常见控件的 `df` 配置，涵盖以下类型：

### 基础输入控件
- Attach、Attach Image、Barcode、Check、Code、Color、Currency、Data、Date、Datetime 等。

### 高级控件
- Dynamic Link、Float、Geolocation、HTML Editor、Int、Link、Markdown Editor 等。

### 交互控件
- Button、Icon、MultiCheck、MultiSelect、Password、Rating、Select、Signature、Text Editor、Time 等。

每个控件均提供完整的 `df` 属性示例（如 `fieldtype`、`options`、`label` 等），方便直接复用。

## 自定义格式化器（Custom Formatters）
- 允许为文本类控件（如 Data、Select）添加自定义显示格式。
- **示例**：通过修改 `frappe.meta.docfield_map` 中的 `formatter` 属性，实现值显示的自定义逻辑。
- **代码示例**：
  ```javascript
  frappe.meta.docfield_map['DocField'].fieldtype.formatter = (value) => {
    if (value === 'Section Break') return '🔵 Section Break';
    return value;
  };
  ```

## 关键用途
- **动态控件生成**：适用于需要运行时动态添加表单控件的场景（如自定义页面、向导等）。
- **控件配置参考**：提供完整的 `df` 属性清单，帮助开发者快速配置各类控件。
- **个性化显示**：通过格式化器定制控件值的展示方式，提升用户体验。

## 注意事项
- 页面内容基于 Frappe Framework 的 JavaScript API，确保在 Frappe 环境中使用。
- 部分控件（如 Code 编辑器）支持高级配置（如语法高亮、行数限制），需仔细参考对应属性。