# 文档API (Document API)

## 1. 什么是 Document？
- 在 Frappe 框架中，**Document** 是 DocType 的一个实例，继承自 `frappe.model.Document` 类。
- 它代表数据库表中的一条记录。

## 2. 核心 API 方法

### 2.1 `frappe.get_doc`
用于获取或创建一个文档对象。

**语法：**
```python
# 获取现有文档
doc = frappe.get_doc('DocType', '文档名称')

# 创建新文档（传入字典）
doc = frappe.get_doc({
    'doctype': 'DocType',
    '字段名': '值'
})

# 使用关键字参数创建
doc = frappe.get_doc(doctype='DocType', field1=value1, ...)
```

**示例：**
```python
# 获取一个任务文档
task = frappe.get_doc('Task', 'TASK00001')
task.title = '新标题'
task.save()

# 创建新任务
new_task = frappe.get_doc({
    'doctype': 'Task',
    'title': '新任务'
})
new_task.insert()
```

### 2.2 `frappe.get_last_doc`
获取指定 DocType 最后创建的文档。

**语法：**
```python
doc = frappe.get_last_doc('DocType', filters={}, order_by="creation desc")
```

**示例：**
```python
# 获取最后一个任务
last_task = frappe.get_last_doc('Task')

# 获取最后一个状态为"取消"的任务
last_cancelled = frappe.get_last_doc('Task', filters={"status": "Cancelled"})
```

### 2.3 `frappe.get_cached_doc`
与 `get_doc` 类似，但会优先从缓存中读取。

### 2.4 `frappe.new_doc`
创建新文档的另一种方式。

**示例：**
```python
doc = frappe.new_doc('Task')
doc.title = '新任务'
doc.insert()
```

### 2.5 `frappe.delete_doc`
删除指定文档。

**语法：**
```python
frappe.delete_doc('DocType', '文档名称')
```

### 2.6 `frappe.rename_doc`
重命名文档。

**语法：**
```python
frappe.rename_doc('DocType', '旧名称', '新名称', merge=False)
```

> **注意**：必须在 DocType 中启用"允许重命名"选项。

### 2.7 `frappe.get_meta`
获取 DocType 的元数据（包括自定义字段、属性设置器等）。

**示例：**
```python
meta = frappe.get_meta('Task')
meta.has_field('status')  # 返回 True/False
```

---

## 3. Document 对象常用方法

### 3.1 `doc.insert()`
将新文档插入数据库。会触发权限检查及 `before_insert`、`validate`、`on_update`、`after_insert` 等控制器方法。

**可选参数：**
- `ignore_permissions`：忽略写入权限
- `ignore_links`：忽略链接验证
- `ignore_if_duplicate`：遇到重复记录时不插入
- `ignore_mandatory`：忽略必填字段

### 3.2 `doc.save()`
保存对现有文档的修改。会触发 `validate` 和 `on_update` 方法。

**可选参数：**
- `ignore_permissions`
- `ignore_version`：不创建版本记录

### 3.3 `doc.delete()`
删除当前文档（等价于 `frappe.delete_doc`）。

### 3.4 `doc.get_doc_before_save()`
获取保存前的文档状态，用于比较字段变更。

### 3.5 `doc.has_value_changed(fieldname)`
检查指定字段的值是否发生变化。

### 3.6 `doc.reload()`
从数据库重新加载文档的最新状态。

### 3.7 `doc.db_set(fieldname, value, **kwargs)`
直接更新数据库中的字段值（**不触发控制器方法**，慎用）。

**参数：**
- `notify`：是否触发前端更新
- `commit`：是否立即提交事务
- `update_modified`：是否更新修改时间戳

### 3.8 `doc.append()`
向子表（Child Table）添加新行。

**示例：**
```python
doc.append("items", {
    "item_name": "产品A",
    "qty": 10
})
```

### 3.9 `doc.get_url()`
返回该文档在 Desk 中的访问 URL（如：`/app/task/TASK00001`）。

### 3.10 评论、标签、查看记录等相关方法
- `doc.add_comment()`：添加评论
- `doc.add_seen()`：标记为已读（需启用"Track Seen"）
- `doc.add_viewed()`：记录查看日志（需启用"Track Views"）
- `doc.add_tag()` / `doc.get_tags()`：管理标签

### 3.11 其他方法
- `doc.run_method('方法名')`：执行控制器中定义的方法
- `doc.queue_action('方法名', ...)`：在后台执行控制器方法
- `doc.get_children()` / `doc.get_parent()`：用于树形结构 DocType

---

## 4. 注意事项
- 使用 `db_set`、`db_insert`、`db_update` 等方法会**绕过控制器验证**，应谨慎使用。
- 涉及权限、业务逻辑时建议使用标准的 `insert`/`save` 方法。

如果需要更详细的使用示例或特定场景的指导，请参考官方文档或社区教程。