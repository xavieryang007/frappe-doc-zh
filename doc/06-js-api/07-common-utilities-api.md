# 常用工具API (Common Utilities API)

## 概述
Frappe框架提供了一系列常用的JavaScript工具函数，用于处理路由、格式化、模块加载等常见开发任务。

## 核心函数列表

| 函数 | 说明 | 示例用法 |
|------|------|----------|
| `frappe.get_route()` | 获取当前路由（以数组形式返回） | `frappe.get_route()` → `["List", "Task", "List"]` |
| `frappe.set_route(route)` | 跳转到指定路由（支持多种参数格式） | `frappe.set_route('List', 'Task', 'List')` 或 `frappe.set_route(['List', 'Task', 'Gantt'])` |
| `frappe.format(value, df, options, doc)` | 将原始值格式化为用户可读的格式（如日期、货币） | `frappe.format('2019-09-08', {fieldtype: 'Date'})` → `"09-08-2019"` |
| `frappe.provide(namespace)` | 在全局对象（window）下创建命名空间（若不存在） | `frappe.provide('frappe.ui.form')` 相当于创建 `window.frappe.ui.form` |
| `frappe.require(asset_path, callback)` | 异步加载 JS 或 CSS 资源（适用于不常用的库） | `frappe.require('/assets/frappe/chat.js', callback)` |

## 详细说明

### 路由管理
- `frappe.get_route()` 返回当前页面的路由信息，格式为数组类型
- `frappe.set_route(route)` 用于页面导航，支持两种参数格式：
  - 多参数形式：`frappe.set_route('List', 'Task', 'List')`
  - 数组形式：`frappe.set_route(['List', 'Task', 'Gantt'])`

### 数据格式化
`frappe.format(value, df, options, doc)` 函数根据字段类型自动转换显示格式：
- 参数说明：
  - `value`：原始值
  - `df`：字段定义对象，包含fieldtype等属性
  - `options`：格式化选项（可选）
  - `doc`：文档上下文（可选）

- 示例：将日期格式化为显示格式
  ```javascript
  frappe.format('2019-09-08', {fieldtype: 'Date'}); // 返回 "09-08-2019"
  ```

### 命名空间管理
`frappe.provide(namespace)` 用于创建对象命名空间，避免空指针错误：
```javascript
// 如果frappe.ui.form不存在，则创建它
frappe.provide('frappe.ui.form');
// 现在可以安全地使用frappe.ui.form.someFunction
```

### 动态资源加载
`frappe.require(asset_path, callback)` 用于按需加载JavaScript或CSS文件：
```javascript
// 加载聊天模块
frappe.require('/assets/frappe/chat.js', function() {
    // 资源加载完成后的回调
    initializeChat();
});
```

## 关键点说明
- **路由管理**：`get_route` 和 `set_route` 用于处理前端路由切换。
- **数据格式化**：`format` 函数根据字段类型（如日期、货币）自动转换显示格式。
- **模块加载**：`require` 支持动态加载脚本或样式表，适合按需使用大型库。