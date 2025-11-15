# 页面API (Page API)

## 概述
在Frappe框架的Desk（工作台）中，每个屏幕都是通过`frappe.ui.Page`对象来渲染的。Page API提供了一系列方法，用于动态创建和配置页面元素，例如标题、按钮、菜单、工具栏等。

## 创建页面
使用`frappe.ui.make_app_page`方法创建一个新的页面实例：
```javascript
let page = frappe.ui.make_app_page({
    title: '页面标题',
    parent: wrapper, // 父级DOM元素或jQuery对象
    single_column: true // 是否创建无侧边栏的单栏页面
});
```

## 主要方法列表

### 设置页面标题
- `page.set_title('标题')`：设置页面主标题（同时更新浏览器标签页标题）。
- `page.set_title_sub('副标题')`：设置页面右侧的副标题。

### 状态指示器
- `page.set_indicator('状态文本', '颜色')`：设置页面顶部的状态指示器（如"Pending"，颜色可选orange、red、green等）。
- `page.clear_indicator()`：清除状态指示器。

### 主要操作按钮
- `page.set_primary_action('按钮文本', 回调函数, '图标类名')`：设置页面右上角的主操作按钮（如"新建"）。
- `page.clear_primary_action()`：清除主操作按钮。

### 次要操作按钮
- `page.set_secondary_action('按钮文本', 回调函数, '图标类名')`：设置次要操作按钮（如"刷新"）。
- `page.clear_secondary_action()`：清除次要操作按钮。

### 菜单项
- `page.add_menu_item('菜单项文本', 回调函数, isStandard)`：在"菜单"下拉框中添加项（`isStandard`为true时标记为标准项）。
- `page.clear_menu()`：清除所有菜单项。

### 操作项
- `page.add_action_item('操作文本', 回调函数)`：在"操作"下拉框中添加项（如"删除"）。
- `page.clear_actions_menu()`：清除操作菜单。

### 内嵌工具栏按钮
- `page.add_inner_button('按钮文本', 回调函数, '分组名')`：在页面内容区域上方添加按钮（可分组）。
- `page.remove_inner_button('按钮文本', '分组名')`：移除指定按钮。
- `page.clear_inner_toolbar()`：清除整个内嵌工具栏。

### 表单字段
- `page.add_field({ ... })`：在页面工具栏中添加表单控件（如Select、Data等类型），可定义`change`事件。
- `page.get_form_values()`：获取所有表单字段的当前值（返回对象）。
- `page.clear_fields()`：清除所有表单字段。

## 使用示例
例如，添加一个选择字段并监听变化：
```javascript
let statusField = page.add_field({
    label: '状态',
    fieldtype: 'Select',
    fieldname: 'status',
    options: ['开启', '关闭', '取消'],
    change() {
        console.log('当前状态：', statusField.get_value());
    }
});
```

## 总结
Page API是Frappe框架中用于动态构建和交互Desk页面的核心工具，涵盖了页面结构、按钮、菜单、表单控件等常见元素的配置方法。通过上述方法，开发者可以灵活定制页面布局和交互逻辑。