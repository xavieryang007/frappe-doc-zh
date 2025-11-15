# 数据库API (Database API)

## 1. `frappe.db.get_list`
**功能**：从指定DocType表中获取记录列表（ORM封装的SELECT查询），并应用当前用户的权限。  
**别名**：`frappe.get_list`  
**参数**：
- `doctype`：目标DocType名称
- `filters`：过滤条件（字典或列表）
- `or_filters`：OR条件的过滤
- `fields`：要返回的字段列表
- `order_by`：排序字段
- `group_by`：分组字段
- `start`：分页起始位置
- `page_length`：每页记录数
- `pluck`：直接提取指定字段值（返回列表而非字典）

**示例**：
```python
# 获取Employee记录（仅返回name字段）
frappe.db.get_list('Employee')

# 使用pluck提取name字段列表
frappe.db.get_list('Employee', pluck='name')

# 复杂查询：筛选状态为Open的Task，返回subject和date字段，按日期降序排列
frappe.db.get_list('Task', 
    filters={'status': 'Open'}, 
    fields=['subject', 'date'], 
    order_by='date desc', 
    start=10, 
    page_length=20, 
    as_list=True
)
```

---

## 2. `frappe.db.get_all`
**功能**：与`get_list`类似，但不应用用户权限。  
**别名**：`frappe.get_all`  
**参数**：同`get_list`  
**示例**：参考`get_list`用法。

---

## 3. `frappe.db.get_value`
**功能**：获取指定文档的字段值。  
**别名**：`frappe.get_value`、`frappe.db.get_values`  
**参数**：
- `doctype`：DocType名称
- `name`：文档名称或过滤条件
- `fieldname`：字段名（字符串或列表）
- `as_dict`：是否以字典形式返回

**示例**：
```python
# 获取单个字段值
subject = frappe.db.get_value('Task', 'TASK00002', 'subject')

# 获取多个字段值（返回元组或字典）
subject, desc = frappe.db.get_value('Task', 'TASK00002', ['subject', 'description'])
task_dict = frappe.db.get_value('Task', 'TASK00002', ['subject', 'description'], as_dict=True)
```

---

## 4. `frappe.db.get_single_value`
**功能**：从Single类型的DocType中获取字段值。  
**参数**：
- `doctype`：Single DocType名称
- `fieldname`：字段名

**示例**：
```python
timezone = frappe.db.get_single_value('System Settings', 'timezone')
```

---

## 5. `frappe.db.set_value`
**功能**：设置数据库中指定文档的字段值（不触发ORM事件，但会更新修改时间戳）。  
**别名**：`frappe.db.update`  
**参数**：
- `doctype`：DocType名称
- `name`：文档名称
- `fieldname`：字段名或字段字典
- `value`：字段值（若fieldname为字典，可省略）
- `update_modified`：是否更新modified时间戳（默认为True）

**示例**：
```python
# 更新单个字段
frappe.db.set_value('Task', 'TASK00002', 'subject', 'New Subject')

# 更新多个字段
frappe.db.set_value('Task', 'TASK00002', {
    'subject': 'New Subject',
    'description': 'New Description'
})

# 不更新modified时间戳
frappe.db.set_value('Task', 'TASK00002', 'subject', 'New Subject', update_modified=False)
```

> **注意**：此方法不会触发`validate`、`on_update`等ORM事件，仅用于更新隐藏字段或明确无需触发事件的场景。

---

## 6. `frappe.db.exists`
**功能**：检查指定文档是否存在。  
**参数**：
- `doctype`：DocType名称
- `name`：文档名称或过滤条件字典

**示例**：
```python
# 通过名称检查
frappe.db.exists("User", "jane@example.org", cache=True)

# 通过过滤条件检查
frappe.db.exists({"doctype": "User", "full_name": "Jane Doe"})
frappe.db.exists("User", {"full_name": "Jane Doe"})
```

---

## 7. `frappe.db.count`
**功能**：返回满足条件的文档数量。  
**参数**：
- `doctype`：DocType名称
- `filters`：过滤条件

**示例**：
```python
# 统计所有Task记录数
frappe.db.count('Task')

# 统计状态为Open的Task数量
frappe.db.count('Task', {'status': 'Open'})
```

---

## 8. `frappe.db.delete`
**功能**：删除满足条件的文档（可回滚的DML操作）。  
**参数**：
- `doctype`：DocType名称或内部表名
- `filters`：过滤条件（若为空则删除所有记录）

**示例**：
```python
# 删除符合条件的记录
frappe.db.delete("Route History", {
    "modified": ("<=", last_record_to_keep[0].modified),
    "user": user
})

# 删除所有记录（谨慎使用）
frappe.db.delete("Error Log")
frappe.db.delete("__Test Table")  # 内部表名
```

---

## 9. `frappe.db.truncate`
**功能**：清空表（DDL操作，不可回滚）。  
**参数**：`doctype`：DocType名称或内部表名  
**示例**：
```python
frappe.db.truncate("Error Log")
frappe.db.truncate("__Test Table")
```

---

## 10. `frappe.db.commit`
**功能**：提交当前事务（执行SQL COMMIT）。  
**注意**：在大多数情况下无需手动调用，Frappe自动管理事务。

---

## 11. `frappe.db.savepoint`
**功能**：创建命名保存点，用于后续回滚。  
**参数**：`save_point`：保存点名称

---

## 12. `frappe.db.rollback`
**功能**：回滚当前事务（执行SQL ROLLBACK）。  
**参数**：`save_point`（可选）：回滚到指定保存点  
**注意**：Frappe在POST/PUT请求异常时自动回滚。

---

## 13. `frappe.db.sql`
**功能**：执行原生SQL查询（谨慎使用）。  
**参数**：
- `query`：SQL语句
- `values`：查询参数（字典）
- `as_dict`：是否返回字典形式结果

**示例**：
```python
data = frappe.db.sql("""
    SELECT acc.account_number, gl.debit, gl.credit
    FROM `tabGL Entry` gl
    LEFT JOIN `tabAccount` acc ON gl.account = acc.name
    WHERE gl.company = %(company)s
""", values={'company': 'Frappe Technologies Inc'}, as_dict=False)
```

> **建议**：优先使用ORM方法（如`get_list`）以避免绕过验证。

---

## 14. `frappe.db.multisql`
**功能**：根据数据库类型执行对应的SQL语句（支持MariaDB/PostgreSQL）。  
**参数**：字典形式，键为数据库类型，值为对应SQL语句  
**示例**：
```python
frappe.db.multisql({
    'mariadb': 'SELECT * FROM `tabTable`',
    'postgres': 'SELECT * FROM "tabTable"'
})
```

---

## 15. `frappe.db.rename_table`
**功能**：重命名表（仅用于内部表，DocType表请使用`frappe.rename_doc`）。  
**示例**：
```python
frappe.db.rename_table("__internal_cache", "__temporary_cache")
```

---

## 16. `frappe.db.describe`
**功能**：返回表的字段描述信息。  
**参数**：`doctype`：DocType名称  
**返回**：字段描述的元组

---

## 17. `frappe.db.change_column_type`
**功能**：修改表中列的数据类型。  
**参数**：
- `doctype`：DocType名称
- `column`：列名
- `new_type`：新数据类型

---

## 18. `frappe.db.add_index`
**功能**：为表添加索引。  
**参数**：
- `doctype`：DocType名称
- `fields`：字段列表（对TEXT/BLOB字段需指定长度）
- `index_name`：索引名称（可选）

**示例**：
```python
frappe.db.add_index("Notes", ["id(10)", "content(500)"], "my_index")
```

---

## 19. `frappe.db.add_unique`
**功能**：为表添加唯一约束。  
**参数**：
- `doctype`：DocType名称
- `fields`：字段列表
- `constraint_name`：约束名称（可选）

**示例**：
```python
frappe.db.add_unique("DoctypeName", ["field1", "field2"])
```

---

## 数据库事务钩子（v15+）
Frappe提供在事务提交/回滚前后执行回调的钩子，用于处理非数据库操作（如文件系统）的同步回滚或提交。  
**可用钩子**：
- `frappe.db.before_commit.add(回调函数)`
- `frappe.db.after_commit.add(回调函数)`
- `frappe.db.before_rollback.add(回调函数)`
- `frappe.db.after_rollback.add(回调函数)`

**示例**：
```python
def create_file(self):
    self.write_file()
    frappe.db.after_rollback.add(self.rollback_file)  # 事务回滚时删除文件

def rollback_file(self):
    self.delete_file()
```

---

## 数据库事务模型
Frappe自动管理事务，规则如下：
- **Web请求**（POST/PUT）：成功完成后自动提交；未捕获异常时回滚。
- **后台任务**：成功完成后提交；异常时回滚。
- **数据迁移补丁**：`execute`方法成功则提交，异常则回滚。
- **单元测试**：每个测试模块完成后提交；未捕获异常导致测试退出时不提交。

> **注意**：若代码中捕获了异常，需手动处理回滚逻辑。