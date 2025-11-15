# 13.7 自定义Web表单 (Customizing Web Forms)

## 自定义Web表单

Web表单是一种强大的方式，可以将表单添加到您的网站。Web表单功能丰富且可编写脚本，从版本7.1+开始，它们还支持表格、分页和其他实用功能。

### 标准Web表单
如果勾选"Is Standard"复选框，系统会在该Web表单所属的模块中创建一个新文件夹。在此文件夹中，您会看到一个`.py`和一个`.js`文件，您可以通过这些文件来自定义Web表单。

### Web表单设置
*   **Allow Edit（允许编辑）**：允许每个用户拥有一个实例并进行编辑。
*   **Allow Multiple（允许多个）**：允许用户查看和编辑Web表单的多个实例。
*   **Show as Grid（以网格形式显示）**：显示Web表单值的表格视图（仅在设置了"Allow Multiple"时有效）。
*   **Allow Incomplete Forms（允许不完整表单）**：对于非常长的表单，您可以允许用户保存而不对必填字段进行验证。用户仍会看到这些字段是必填的。

### 客户端脚本
您还可以向Web表单添加自定义客户端脚本。

### API

#### 事件处理程序
编写事件处理程序，以便在字段更改时执行操作。
```javascript
frappe.web_form.on([fieldname], [handler]);
```

#### 获取值
获取特定字段的值。
```javascript
value = frappe.web_form.get_value([fieldname]);
```

#### 设置值
设置特定字段的值。
```javascript
frappe.web_form.set_value([fieldname], [value])
```

#### 验证
`frappe.web_form.validate` 在Web表单保存之前被调用。通过覆盖此方法添加自定义验证。如果验证不通过，返回 `false` 以阻止保存。
```javascript
frappe.web_form.validate = () => {
    // 如果无效，返回 false
}
```

#### 设置字段属性
```javascript
frappe.web_form.set_df_property([fieldname], [property], [value]);
```

#### 在表单加载时触发脚本
在表单加载后，使用自定义初始化脚本。
```javascript
frappe.web_form.after_load = () => {
    // 在此处初始化脚本
}
```

### 示例

#### 如果无效则重置值
```javascript
frappe.web_form.on('amount', (field, value) => {
    if (value < 1000) {
        frappe.msgprint('值必须大于1000');
        field.set_value(0);
    }
});
```

#### 自定义验证
```javascript
frappe.web_form.validate = () => {
    let data = frappe.web_form.get_values();
    if (data.amount < 1000) {
        frappe.msgprint('值必须大于1000');
        return false;
    }
});
```

#### 根据值隐藏字段
```javascript
frappe.web_form.on('amount', (field, value) => {
    if (value < 1000) {
        frappe.web_form.set_df_property('rate', 'hidden', 1);
    }
});
```

#### 在启动时显示消息
```javascript
frappe.web_form.after_load = () => {
    frappe.msgprint('请仔细填写所有值');
}
```

### 面包屑导航
您可以通过添加JSON对象来自定义Web表单中的面包屑导航。

示例：
```json
[{"label": "Home", "route":"/" }]
```