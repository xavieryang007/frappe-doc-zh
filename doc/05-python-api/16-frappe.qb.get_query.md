# frappe.qb.get_query

`frappe.qb.get_query` 函数提供了在现代、安全且富有表现力的方式来构建Frappe中的数据库查询。它利用了[Pypika](https://pypika.readthedocs.io/en/latest/)库的强大功能，同时与Frappe的DocType元数据、权限和约定无缝集成。

## 简介
虽然`frappe.db.get_list`和`frappe.db.get_all`多年来一直服务于Frappe并且能够处理复杂的查询，但它们的底层代码随着时间的推移变得越来越复杂，使其难以维护和扩展。`frappe.qb.get_query`代表了一种现代方法，旨在实现类似的结果，同时具有更好的可扩展性和可维护性。

## 入门指南

### 基本查询结构
```python
# 获取'User' DocType的查询构建器实例
query = frappe.qb.get_query("User")

# 执行查询并获取结果
users = query.run(as_dict=True)
```
默认情况下，如果未指定`fields`，它**仅选择`name`字段**。

### 简单示例
```python
# 获取所有用户的姓名和电子邮件
query = frappe.qb.get_query("User", fields=["name", "email"])
users = query.run(as_dict=True)
# users将类似于：[{'name': 'Administrator', 'email': 'admin@example.com'}, ...]
```

## 字段选择

### 基本字段选择
使用`fields`参数指定所需的列。

```python
# 选择特定字段
query = frappe.qb.get_query("User", fields=["name", "email"])

# 也可以将字段作为逗号分隔的字符串传递
query = frappe.qb.get_query("User", fields="name, email")

# 或使用'*'选择所有字段
query = frappe.qb.get_query("User", fields="*")
```

### 使用别名
在字段字符串中使用`as`为输出中的字段赋予别名。

```python
query = frappe.qb.get_query(
    "User",
    fields=["name as user_name", "email as user_email"]
)
users = query.run(as_dict=True)
# users将类似于：[{'user_name': 'Administrator', 'user_email': 'admin@example.com'}, ...]
```

### 链接文档字段
如果有Link字段，可以使用点符号`link_fieldname.target_fieldname`从链接的文档中获取字段。查询构建器会自动添加必要的`LEFT JOIN`。

```python
# 获取销售订单客户名称（假设'customer'是指向Customer DocType的Link字段）
query = frappe.qb.get_query(
    "Sales Order",
    fields=["name", "customer.customer_name as customer_name"],
    filters={"name": "SO-00001"}
)
so_data = query.run(as_dict=True)
# so_data可能为：[{'name': 'SO-00001', 'customer_name': 'Test Customer'}]
```

### 子表字段
可以使用点符号访问子表内的字段：`child_table_fieldname.child_doc_fieldname`。

```python
# 从销售订单项目中获取物料代码（假设'items'是子表字段名）
query = frappe.qb.get_query(
    "Sales Order",
    fields=["name", "items.item_code"],
    filters={"name": "SO-00001"}
)
# 如果SO-00001有多个项目，这可能会返回多行
so_items = query.run(as_dict=True)
# so_items可能为：[{'name': 'SO-00001', 'item_code': 'ItemA'}, {'name': 'SO-00001', 'item_code': 'ItemB'}]
```

**重要提示：** 当以这种方式选择子表中的字段时，查询会执行`LEFT JOIN`，如果子表有多个条目，可能会导致每个父文档有多行。

### 获取子表记录
一种更结构化的获取子表数据的方式：

```python
# 获取销售订单及其关联的项目
query = frappe.qb.get_query(
    "Sales Order",
    fields=[
        "name",
        "customer",
        {"items": ["item_code", "qty", "rate"]} # 从'items'子表获取
    ],
    filters={"docstatus": 1},
    limit=5
)

results = query.run(as_dict=True)

# 示例结果结构：
# [
#   {
#     'name': 'SO-00001',
#     'customer': 'Customer A',
#     'items': [
#       {'item_code': 'ItemA', 'qty': 2.0, 'rate': 100.0},
#       {'item_code': 'ItemB', 'qty': 1.0, 'rate': 50.0}
#     ]
#   },
#   {
#     'name': 'SO-00002',
#     'customer': 'Customer B',
#     'items': [
#       {'item_code': 'ItemC', 'qty': 5.0, 'rate': 20.0}
#     ]
#   }
# ]
```

这种方法在获取父记录后对子记录执行单独的查询，有效地按父记录对它们进行分组。

### 字段中的SQL函数
要在select子句中使用SQL函数，可以在`fields`参数中使用基于字典的语法。

#### 聚合函数
```python
# COUNT - 计算行数或非空值
query = frappe.qb.get_query(
    "User",
    fields=["user_type", {"COUNT": "name", "as": "total_users"}],
    group_by="user_type"
)

# COUNT(*) - 计算所有行（包括空值）
query = frappe.qb.get_query(
    "Sales Invoice",
    fields=[{"COUNT": "'*'", "as": "total_invoices"}]
)

# SUM - 计算数值总和
query = frappe.qb.get_query(
    "Sales Invoice",
    fields=[{"SUM": "grand_total", "as": "total_sales"}],
    filters={"docstatus": 1}
)

# AVG - 计算数值平均值
query = frappe.qb.get_query(
    "Sales Invoice",
    fields=[{"AVG": "grand_total", "as": "avg_invoice_amount"}],
    group_by="customer"
)

# MAX - 查找最大值
query = frappe.qb.get_query(
    "User",
    fields=[{"MAX": "creation", "as": "latest_user_date"}]
)

# MIN - 查找最小值
query = frappe.qb.get_query(
    "User",
    fields=[{"MIN": "creation", "as": "earliest_user_date"}]
)
```

#### 标量函数
```python
# ABS - 绝对值
query = frappe.qb.get_query(
    "Journal Entry Account",
    fields=[{"ABS": "debit_in_account_currency", "as": "abs_amount"}]
)

# IFNULL - 处理空值（接受两个参数）
query = frappe.qb.get_query(
    "User",
    fields=[{"IFNULL": ["first_name", "'Unknown'"], "as": "display_name"}]
)

# CONCAT - 连接字符串（接受多个参数）
query = frappe.qb.get_query(
    "User",
    fields=[{"CONCAT": ["first_name", "' '", "last_name"], "as": "full_name"}]
)

# EXTRACT - 提取日期/时间部分（接受两个参数：单位和日期）
query = frappe.qb.get_query(
    "User",
    fields=[{"EXTRACT": ["'YEAR'", "creation"], "as": "creation_year"}]
)

# NOW - 当前时间戳（无参数）
query = frappe.qb.get_query(
    "User",
    fields=[{"NOW": None, "as": "current_time"}]
)
```

## 过滤

### 基本过滤器
`filters`参数允许您添加`WHERE`子句。

#### 字典过滤器（相等性）
这是最简单且通常首选用于相等性检查的方式。

```python
# 获取名字为'Admin'的用户
query = frappe.qb.get_query(
    "User",
    fields=["name", "email"],
    filters={"first_name": "Admin"} # 简单的键值对表示'='
)
```

#### 字典过滤器（其他操作符）
对于`=`以外的操作符，使用字典，其中值是一个列表`[operator, value]`。

```python
# 获取在某个日期之后创建的用户
query = frappe.qb.get_query(
    "User",
    fields=["name", "creation"],
    filters={"creation": [">", "2023-01-01 00:00:00"]}
)

# 获取名字类似'test%'的用户
query = frappe.qb.get_query(
    "User",
    fields=["name"],
    filters={"name": ["like", "test%"]}
)

# 获取名字在列表中的用户
query = frappe.qb.get_query(
    "User",
    fields=["name"],
    filters={"name": ["in", ["test1@example.com", "test2@example.com"]]}
)

# 获取名字不在列表中的用户
query = frappe.qb.get_query(
    "User",
    fields=["name"],
    filters={"name": ["not in", ["Administrator", "Guest"]]}
)
```

#### 列表过滤器（替代格式）
您也可以提供一个包含过滤器列表`[[fieldname, operator, value]]`的列表。

```python
# 获取在某个日期之后创建的用户（使用列表格式）
query = frappe.qb.get_query(
    "User",
    fields=["name", "creation"],
    filters=[["creation", ">", "2023-01-01 00:00:00"]]
)

# 多个过滤器（用AND组合）
query = frappe.qb.get_query(
    "User",
    fields=["name"],
    filters=[
        ["enabled", "=", 1],
        ["user_type", "=", "System User"]
    ]
)
```

### 过滤器操作符
`frappe.qb.get_query`支持各种操作符：

| 操作符字符串 | SQL等效 | 示例用法（字典格式） | 示例用法（列表格式） |
|-------------|---------|---------------------|-------------------|
| `=` | `=` | `{"name": "Test"}` | `[["name", "=", "Test"]]` |
| `!=` | `!=` | `{"name": ["!=", "Test"]}` | `[["name", "!=", "Test"]]` |
| `>` | `>` | `{"creation": [">", "2023-01-01"]}` | `[["creation", ">", "2023-01-01"]]` |
| `<` | `<` | `{"creation": ["<", "2023-01-01"]}` | `[["creation", "<", "2023-01-01"]]` |
| `>=` | `>=` | `{"age": [">=", 18]}` | `[["age", ">=", 18]]` |
| `<=` | `<=` | `{"age": ["<=", 65]}` | `[["age", "<=", 65]]` |
| `like` | `LIKE` | `{"subject": ["like", "%urgent%"]}` | `[["subject", "like", "%urgent%"]]` |
| `not like` | `NOT LIKE` | `{"subject": ["not like", "spam%"]}` | `[["subject", "not like", "spam%"]]` |
| `in` | `IN` | `{"status": ["in", ["Open", "Pending"]]}` | `[["status", "in", ["Open", "Pending"]]]` |
| `not in` | `NOT IN` | `{"role": ["not in", ["Guest"]]}` | `[["role", "not in", ["Guest"]]]` |
| `is` | `IS NULL` 或 `IS NOT NULL` | `{"customer": ["is", "set"]}` 或 `{"email": ["is", "not set"]}` | `[["customer", "is", "set"]]` 或 `[["email", "is", "not set"]]` |
| `descendants of` | （嵌套集） | `{"parent_account": ["descendants of", "Assets"]}` | `[["parent_account", "descendants of", "Assets"]]` |
| `ancestors of` | （嵌套集） | `{"location": ["ancestors of", "Room 101"]}` | `[["location", "ancestors of", "Room 101"]]` |
| `not descendants of` | （嵌套集） | `{"category": ["not descendants of", "Internal"]}` | `[["category", "not descendants of", "Internal"]]` |
| `not ancestors of` | （嵌套集） | `{"territory": ["not ancestors of", "West Coast"]}` | `[["territory", "not ancestors of", "West Coast"]]` |

**关于`is set` / `is not set`的说明：** 这些检查字段是否有值（分别为`IS NOT NULL`和`IS NULL`）。

### 基于链接文档字段的过滤
可以使用点符号基于链接文档中的字段进行过滤：`link_fieldname.target_fieldname`。

```python
# 获取链接客户的地区为'North America'的销售订单
query = frappe.qb.get_query(
    "Sales Order",
    fields=["name", "customer"],
    filters={"customer.territory": "North America"} # 基于链接字段过滤
)
north_america_orders = query.run(as_dict=True)
```

### 基于子表字段的过滤
可以使用点符号基于其子表记录中的值过滤父记录：`child_table_fieldname.target_fieldname`。

```python
# 获取在其项目表中包含'Item A'的销售订单
# 使用distinct=True确保每个销售订单只出现一次
query = frappe.qb.get_query(
    "Sales Order",
    fields=["name", "customer"],
    filters={"items.item_code": "Item A"}, # 基于子表字段过滤
    distinct=True
)
orders_with_item_a = query.run(as_dict=True)
```

**重要提示：** 当基于子表字段过滤时，如果只需要唯一的父记录，请使用`distinct=True`。

### 嵌套集过滤器
对于是树形结构的DocType（使用`lft`和`rgt`列，如Account、Territory、Warehouse等），可以使用特殊过滤器：

```python
# 获取'Assets'下的所有账户
query = frappe.qb.get_query(
    "Account",
    fields=["name"],
    filters={"parent_account": ["descendants of", "Assets"]}
)

# 获取'West Coast'的父地区
query = frappe.qb.get_query(
    "Territory",
    fields=["name"],
    filters={"parent_territory": ["ancestors of", "West Coast"]}
)
```

### 逻辑操作符（AND/OR）
对于复杂的条件，将`filters`构造为一个列表，使用`'and'`或`'or'`组合条件。

```python
# 查找已启用且名字为'Admin'的用户
filters_and = [
    ["enabled", "=", 1],
    "and",
    ["first_name", "=", "Admin"],
]
query = frappe.qb.get_query("User", filters=filters_and)

# 查找名字为'Admin'或'Guest'的用户
filters_or = [
    ["first_name", "=", "Admin"],
    "or",
    ["first_name", "=", "Guest"],
]
query = frappe.qb.get_query("User", filters=filters_or)

# 组合AND和OR（使用嵌套列表进行分组）
# 查找已启用且（名字为'Admin'或'Guest'）的用户
filters_nested = [
    ["enabled", "=", 1],
    "and",
    [
        ["first_name", "=", "Admin"],
        "or",
        ["first_name", "=", "Guest"],
    ]
]
query = frappe.qb.get_query("User", filters=filters_nested)
```

## 查询执行

### 基本执行
获得`query`对象后，使用`.run()`执行它：

```python
# 默认返回元组列表
results = query.run()

# 返回字典列表
results = query.run(as_dict=True)

# 返回列表的列表
results = query.run(as_list=True)

# 如果选择单个字段，返回值的平面列表
results = query.run(pluck=True)

# 打印生成的SQL查询和执行时间
results = query.run(debug=True)
```

### 获取SQL字符串
您可以获取生成的SQL字符串而不执行：

```python
# 获取直接替换值的SQL字符串（用于调试）
sql_string = query.get_sql()
print(sql_string)
# 示例输出：SELECT `name`, `email` FROM `tabUser` WHERE `first_name`='Admin'
```

### 对大型数据集使用迭代器
处理大型数据集时，使用`as_iterator=True`逐行处理结果，而无需将所有内容加载到内存中：

```python
# 处理大量任务而不将所有内容加载到内存中
query = frappe.qb.get_query(
    "Task",
    fields=["name", "subject", "status"],
    filters={"status": "Open"}
)

# 使用无缓冲游标以优化迭代器的内存使用
with frappe.db.unbuffered_cursor():
    task_iterator = query.run(as_iterator=True, as_dict=True)

    processed_count = 0
    for task in task_iterator:
        # 逐个处理每个任务字典
        print(f"Processing Task: {task['name']} - {task['subject']}")
        processed_count += 1
        if processed_count % 1000 == 0:
            print(f"Processed {processed_count} tasks...")
```

**要求：**
- 必须将`as_iterator=True`与`as_dict=True`或`as_list=True`一起使用
- 为了获得最佳内存效率，请在`frappe.db.unbuffered_cursor()`上下文管理器中使用

## 排序、分组和分页

### 排序结果
使用`order_by`参数对结果进行排序：

```python
# 按创建日期升序排序用户
query = frappe.qb.get_query("User", fields=["name", "creation"], order_by="creation asc")

# 按多个字段排序
query = frappe.qb.get_query(
    "Sales Invoice",
    fields=["name", "customer", "grand_total"],
    order_by="customer asc, grand_total desc"
)
```

### 分组结果
使用`group_by`进行聚合：

```python
# 计算每个客户的发票数量
query = frappe.qb.get_query(
    "Sales Invoice",
    fields=["customer", {"COUNT": "'*'", "as": "invoice_count"}],
    filters={"docstatus": 1},
    group_by="customer"
)
results = query.run(as_dict=True)
# results: [{'customer': 'Cust A', 'invoice_count': 5}, {'customer': 'Cust B', 'invoice_count': 3}, ...]
```

### 分页
使用`limit`和`offset`进行分页：

```python
# 获取前10个用户
query = frappe.qb.get_query("User", limit=10)

# 获取接下来的10个用户（第2页）
query = frappe.qb.get_query("User", limit=10, offset=10)
```

### 去重结果
使用`distinct=True`获取唯一的行：

```python
# 从已提交的销售发票中获取不同的客户
query = frappe.qb.get_query(
    "Sales Invoice",
    fields=["customer"],
    filters={"docstatus": 1},
    distinct=True
)
```

## 权限

### `ignore_permissions`标志
默认情况下，`frappe.qb.get_query`**忽略**权限（`ignore_permissions=True`）。要强制执行权限，请设置`ignore_permissions=False`：

```python
# 此查询绕过所有权限检查（默认行为）
query_ignore = frappe.qb.get_query("DocType", fields=["name"], filters={"istable": 1})

# 此查询为当前用户强制执行标准Frappe权限
query_enforce = frappe.qb.get_query(
    "DocType",
    fields=["name"],
    filters={"istable": 1},
    ignore_permissions=False # 显式启用权限检查
)

try:
    results = query_enforce.run()
except frappe.PermissionError:
    print("User does not have permission to read DocType!")
```

### 权限如何应用
当`ignore_permissions=False`时：

1. **角色权限：** 检查用户是否基于其角色具有'read'或'select'权限。
2. **用户权限：** 应用为DocType和链接的DocTypes定义的用户权限（允许/限制）。
3. **共享：** 包括明确与用户共享的文档。
4. **所有者约束：** 如果角色权限仅授予`if_owner`访问权限，则查询将结果限制为用户拥有的文档。
5. **权限查询条件：** 应用通过Hooks或服务器脚本定义的条件。
6. **字段级安全性：** 过滤选定的`fields`，如果用户没有permlevel访问权限，则不允许在`filters`、`group_by`、`order_by`中使用的字段。还会检查`link_field.target_field`和`child_field.target_field`符号中的字段。

### 字段级安全性
当`ignore_permissions=False`时：

- **`fields`：** 仅包含用户最大允许permlevel可访问的字段。请求不可访问的字段将**静默移除**该字段。
- **`filters`：** 仅允许对用户有权访问的字段进行过滤。尝试对不可访问的字段进行过滤**将引发**`frappe.PermissionError`。
- **`group_by`：** 仅允许对用户有权访问的字段进行分组。尝试对不可访问的字段进行分组**将引发**`frappe.PermissionError`。
- **`order_by`：** 仅允许对用户有权访问的字段进行排序。尝试对不可访问的字段进行排序**将引发**`frappe.PermissionError`。
- **链接和子表字段：** 在任何上述子句中使用`link_field.target_field`或`child_field.target_field`符号时，系统会检查链接/子字段本身以及链接/子DocType中目标字段的权限。

```python
# 假设Blog Post中的'published'字段具有permlevel 1
# 用户'test@example.com'只有permlevel 0访问权限

# 这可以工作，但'published'字段被静默从结果中移除
query = frappe.qb.get_query(
    "Blog Post",
    fields=["name", "title", "published"], # 请求了'published'但不可访问
    ignore_permissions=False,
    user="test@example.com"
)
# 结果将包含'name'和'title'，但不包含'published'

# 这失败，因为不允许为此用户过滤'published'
try:
    query = frappe.qb.get_query(
        "Blog Post",
        fields=["name"],
        filters={"published": 1}, # 在受限字段上过滤
        ignore_permissions=False,
        user="test@example.com"
    )
    query.run()
except frappe.PermissionError as e:
    print(f"Permission error: {e}")
```

### 指定用户和父上下文
```python
# 检查特定用户的权限
query = frappe.qb.get_query(
    "Task",
    ignore_permissions=False,
    user="test@example.com" # 检查此用户的权限
)

# 为子DocTypes提供父上下文
query = frappe.qb.get_query(
    "Sales Order Item",
    fields=["item_code", "qty"],
    filters={"parent": "SO-00001"},
    ignore_permissions=False,
    parent_doctype="Sales Order" # 指定权限检查的父上下文
)
```

## 高级功能

### 使用Pypika对象
对于涉及子查询、高级条件或字典语法中不可用的函数的复杂场景，可以直接使用Pypika对象。

#### 字段中的Pypika对象
```python
from frappe.query_builder import Field, functions, Query

# 定义Pypika对象
user_table = frappe.qb.DocType("User")
todo_table = frappe.qb.DocType("ToDo")

# 构建一个子查询来计算每个用户的未完成待办事项数量
open_todo_subquery = (
    Query.from_(todo_table)
    .select(functions.Count("*"))
    .where(todo_table.owner == user_table.name) # 相关子查询
    .where(todo_table.status == "Open")
).as_("open_todos_count")

# 在字段中使用Pypika对象
query = frappe.qb.get_query(
    "User",
    fields=[
        user_table.name,
        user_table.email,
        open_todo_subquery # 使用子查询对象
    ],
    filters={"user_type": "System User"}
)
users_with_counts = query.run(as_dict=True)
```

#### 过滤器中的Pypika对象
```python
from frappe.query_builder import Field, functions

# 定义Pypika对象
task_table = frappe.qb.DocType("Task")
modified_field = task_table.modified
creation_field = task_table.creation
subject_field = task_table.subject
status_field = task_table.status

# 构建复杂的条件
complex_filter = (
    (modified_field > creation_field) & (functions.Length(subject_field) > 10)
) | (status_field == "Cancelled")

# 在过滤器中使用条件对象
query = frappe.qb.get_query(
    "Task",
    fields=["name", "subject", "status", "creation", "modified"],
    filters=complex_filter
)
results = query.run(as_dict=True)
```

### 记录锁定
用于需要防止其他事务修改特定行的数据库事务中：

#### 基本锁定
```python
# 锁定特定的库存分类账条目
query = frappe.qb.get_query(
    "Stock Ledger Entry",
    fields=["name", "qty_after_transaction"],
    filters={"item_code": "ITEM001", "warehouse": "WH001"},
    for_update=True # 添加FOR UPDATE子句，如果行被锁定将等待
)
entries = query.run(as_dict=True)
```

#### 跳过锁定的行
```python
# 跳过已被其他事务锁定的行
query = frappe.qb.get_query(
    "ToDo",
    fields=["name", "description"],
    filters={"status": "Pending"},
    limit=5,
    order_by="creation asc",
    for_update=True,
    skip_locked=True # 跳过锁定的行
)
available_tasks = query.run(as_dict=True)
```

#### 非阻塞锁定尝试
```python
# 如果行已被锁定，立即失败
try:
    query = frappe.qb.get_query(
        "System Settings",
        fields=["name"],
        filters={"name": "System Settings"},
        for_update=True,
        wait=False # 不等待锁
    )
    settings = query.run(as_dict=True)
except Exception as e:
    print(f"Could not acquire lock immediately: {e}")
```

## 安全考虑
`frappe.qb.get_query`在设计时考虑了安全性：

- **字段验证：** 字段名根据严格模式进行验证，以防止SQL注入。
- **参数化：** 过滤器值由数据库驱动程序进行参数化。
- **权限强制执行：** 使用`ignore_permissions=False`利用了Frappe强大的权限系统。

始终确保用于构造过滤器键或字段名的任何动态值（如果它们来自不受信任的源）都经过适当清理。**始终依赖将用户输入作为过滤器值传递。**