# 服务器调用 (Server Calls)

## 概述
Frappe提供了一系列JavaScript API，用于从前端（客户端）通过AJAX调用与后端（服务器）进行交互。这些API主要用于对数据库中的文档（DocType）进行增删改查操作。

## 核心API方法

### 1. `frappe.call`
- **用途**：向服务器发送AJAX请求，执行指定的Python方法。
- **语法**：
  ```javascript
  frappe.call({method: '方法路径', args: {参数}, freeze: true, callback: function(r) { ... }})
  ```
- **示例**：
  ```javascript
  frappe.call('frappe.core.doctype.user.user.get_role_profile', {role_profile: 'Test'})
    .then(r => console.log(r.message));
  ```

### 2. `frappe.db.get_doc`
- **用途**：根据文档类型（Doctype）和名称获取单个文档对象。
- **语法**：`frappe.db.get_doc(doctype, name)`
- **示例**：
  ```javascript
  frappe.db.get_doc('Task', 'TASK00002').then(doc => console.log(doc));
  ```

### 3. `frappe.db.get_list`
- **用途**：获取符合筛选条件的文档列表。
- **语法**：`frappe.db.get_list(doctype, {fields: [...], filters: {...}})`
- **示例**：
  ```javascript
  frappe.db.get_list('Task', {fields: ['subject'], filters: {status: 'Open'}})
    .then(records => console.log(records));
  ```

### 4. `frappe.db.get_value`
- **用途**：获取文档的特定字段值。
- **语法**：`frappe.db.get_value(doctype, name, fieldname)`
- **示例**：
  ```javascript
  frappe.db.get_value('Task', 'TASK00004', 'status').then(r => console.log(r.message.status));
  ```

### 5. `frappe.db.get_single_value`
- **用途**：从"单文档类型"（Single DocType）中获取字段值。
- **语法**：`frappe.db.get_single_value(doctype, fieldname)`
- **示例**：
  ```javascript
  frappe.db.get_single_value('System Settings', 'time_zone').then(tz => console.log(tz));
  ```

### 6. `frappe.db.set_value`
- **用途**：更新文档的字段值。
- **语法**：`frappe.db.set_value(doctype, docname, fieldname, value)`
- **示例**：
  ```javascript
  frappe.db.set_value('Task', 'TASK00004', 'status', 'Open').then(r => console.log(r.message));
  ```

### 7. `frappe.db.insert`
- **用途**：插入新文档。
- **语法**：`frappe.db.insert({doctype: '...', field1: value1, ...})`
- **示例**：
  ```javascript
  frappe.db.insert({doctype: 'Task', subject: 'New Task'}).then(doc => console.log(doc));
  ```

### 8. `frappe.db.count`
- **用途**：统计符合筛选条件的文档数量。
- **语法**：`frappe.db.count(doctype, filters)`
- **示例**：
  ```javascript
  frappe.db.count('Task', {status: 'Open'}).then(count => console.log(count));
  ```

### 9. `frappe.db.delete_doc`
- **用途**：删除指定文档。
- **语法**：`frappe.db.delete_doc(doctype, name)`
- **示例**：
  ```javascript
  frappe.db.delete_doc('Task', 'TASK00004');
  ```

### 10. `frappe.db.exists`
- **用途**：检查文档是否存在。
- **语法**：`frappe.db.exists(doctype, name)`
- **示例**：
  ```javascript
  frappe.db.exists('Task', 'TASK00004').then(exists => console.log(exists));
  ```

## 高级用法

### 组合查询
```javascript
frappe.db.get_list('Task', {
    fields: ['subject', 'status', 'priority'],
    filters: [
        ['status', '!=', 'Closed'],
        ['priority', '=', 'High']
    ],
    limit_page_length: 20
}).then(tasks => {
    // 处理任务列表
});
```

### 批量操作
```javascript
// 批量更新多个任务
const tasks = ['TASK00001', 'TASK00002', 'TASK00003'];
tasks.forEach(task => {
    frappe.db.set_value('Task', task, 'status', 'In Progress');
});
```

## 关键注意事项
- 所有`frappe.db`方法均返回Promise，可使用`.then()`处理响应。
- 使用`frappe.call`时可配置`freeze: true`以冻结界面，防止用户重复操作。
- 确保调用的服务器方法已在后端正确实现并允许前端访问。
- 跨域调用需要在后端使用`@frappe.whitelist()`装饰器暴露方法。