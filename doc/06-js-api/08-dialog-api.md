# 对话框API (Dialog API)

## 概述
Frappe框架提供了丰富的对话框API，用于创建各种交互式对话框和提示，支持表单字段、操作按钮和自定义交互逻辑。

## 核心对话框类型

### 1. `frappe.ui.Dialog` - 自定义对话框
用于创建自定义对话框，支持表单字段和主操作。
```javascript
let d = new frappe.ui.Dialog({
  title: '输入详细信息',
  fields: [
    { label: '姓名', fieldname: 'name', fieldtype: 'Data' }
  ],
  primary_action(values) {
    console.log(values);
    d.hide();
  }
});
d.show();
```

### 2. `frappe.msgprint` - 消息提示框
显示消息提示框，支持标题、颜色指示器和自定义操作。
```javascript
// 简单消息
frappe.msgprint('文档更新成功');

// 带操作的消息框
frappe.msgprint({
  title: '确认',
  message: '是否继续？',
  primary_action: {
    action(values) { /* 处理逻辑 */ }
  }
});
```

### 3. `frappe.throw` - 错误提示
显示错误消息并抛出异常。
```javascript
frappe.throw('这是一个错误信息');
```

### 4. `frappe.prompt` - 输入提示框
提示用户输入单个或多个字段值。
```javascript
// 输入单个值
frappe.prompt('请输入姓名', ({ value }) => console.log(value));

// 输入多个字段
frappe.prompt([
  { label: '姓名', fieldname: 'name', fieldtype: 'Data' },
  { label: '年龄', fieldname: 'age', fieldtype: 'Int' }
], (values) => console.log(values));
```

### 5. `frappe.confirm` - 确认对话框
确认对话框，支持"是/否"回调。
```javascript
frappe.confirm('确定要继续吗？', 
  () => { /* 点击"是" */ },
  () => { /* 点击"否" */ }
);
```

### 6. `frappe.warn` - 警告对话框
警告对话框，可设置为可最小化。
```javascript
frappe.warn('警告', '存在未保存的更改', 
  () => { /* 继续操作 */ }, 
  '继续', 
  true // 可最小化
);
```

### 7. `frappe.show_alert` - 非阻塞提示
非阻塞式提示消息，默认显示7秒。
```javascript
frappe.show_alert({ message: '操作成功', indicator: 'green' }, 5);
```

### 8. `frappe.show_progress` - 进度条
显示进度条。
```javascript
frappe.show_progress('加载中...', 70, 100, '请稍候');
```

### 9. `frappe.new_doc` - 新建文档对话框
快速创建新文档，支持字段初始化和回调。
```javascript
frappe.new_doc('任务', { subject: '新任务' }, (doc) => {
  doc.description = '需要完成的工作';
});
```

### 10. `frappe.ui.form.MultiSelectDialog` - 多选对话框
多选对话框，用于从列表中选择多条记录。
```javascript
new frappe.ui.form.MultiSelectDialog({
  doctype: '物料请求',
  setters: { status: '待处理' },
  action(selections) {
    console.log('已选择：', selections);
  }
});
```

### 11. 对话框中添加表格（Table）
支持在对话框中嵌入表格字段，并支持行操作事件。
```javascript
const dialog = new frappe.ui.Dialog({
  fields: [{
    fieldname: 'logs',
    fieldtype: 'Table',
    label: '日志',
    fields: [
      { fieldname: 'log_type', label: '类型', fieldtype: 'Select' }
    ],
    on_add_row: (idx) => {
      // 添加行时的逻辑
    }
  }]
});
```

## 关键特性
- **灵活性**：几乎所有对话框都支持自定义字段、按钮和回调。
- **集成性**：可与Frappe的Doctype、表单列表等深度集成。
- **国际化**：支持 `__()` 函数进行多语言翻译。