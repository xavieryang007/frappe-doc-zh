# 表单脚本 (Form Scripts)

## 概述
表单脚本用于在Frappe框架的表单（Form）中添加客户端逻辑，例如自动获取值、添加验证或上下文操作。

## 标准表单脚本
- 位置：创建DocType时自动生成 `{doctype}.js` 文件。
- 语法示例：
  ```javascript
  frappe.ui.form.on('ToDo', {
      refresh(frm) {
          // 在表单刷新时执行
          if (frm.doc.reference_type && frm.doc.reference_name) {
              frm.add_custom_button(__(frm.doc.reference_name), () => {
                  frappe.set_route("Form", frm.doc.reference_type, frm.doc.reference_name);
              });
          }
      }
  });
  ```

## 子表格脚本（Child Table Scripts）
- 应在父DocType的同一脚本文件中编写。
- 示例：
  ```javascript
  frappe.ui.form.on('Quotation Item', {
      item_code(frm, cdt, cdn) {
          let row = frappe.get_doc(cdt, cdn);
      }
  });
  ```

## 自定义表单脚本（Custom Form Scripts）
- 通过Client Script创建，适用于特定站点的逻辑。
- 路径：`Home > Customization > Client Script > New`

## 表单事件（Form Events）
表单脚本通过事件触发。常用事件包括：

| 事件名 | 描述 |
|--------|------|
| `setup` | 表单首次创建时触发 |
| `onload` | 表单加载并即将渲染时触发 |
| `refresh` | 表单加载并渲染完成后触发 |
| `validate` | 保存前触发验证 |
| `before_save` / `after_save` | 保存前后触发 |
| `{fieldname}` | 字段值更改时触发 |

### 子表格事件示例：
```javascript
frappe.ui.form.on('Dynamic Link', {
    links_add(frm, cdt, cdn) {
        frappe.msgprint('新行已添加到链接表格！');
    }
});
```

## 表单API（Form API）常用方法
| 方法 | 描述 |
|------|------|
| `frm.set_value()` | 设置字段值，触发字段变更事件 |
| `frm.refresh()` | 刷新表单（从服务器重新加载） |
| `frm.save()` | 保存表单（支持Submit/Cancel/Update） |
| `frm.add_custom_button()` | 添加自定义按钮 |
| `frm.set_query()` | 为Link字段设置筛选条件 |
| `frm.call()` | 调用服务器端方法（需配合`@frappe.whitelist`） |
| `frm.toggle_display()` | 根据条件显示/隐藏字段 |

示例：
```javascript
// 设置字段为必填
frm.toggle_reqd('priority', frm.doc.status === 'Open');

// 为Link字段添加筛选
frm.set_query('customer', () => {
    return {
        filters: { territory: 'India' }
    };
});
```

## 注意事项
- 使用`frm.call()`调用服务器方法时，必须在Python方法前添加`@frappe.whitelist`装饰器。
- 子表格事件命名格式为：`{fieldname}_add`、`{fieldname}_remove`等。
- 可通过`frm.trigger()`手动触发事件。