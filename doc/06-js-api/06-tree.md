# 树形结构 (Tree)

## 概述
在Frappe/ERPNext框架中，树形结构（Tree）是一种常见的数据组织方式，用于表示层级关系的数据，如部门结构、账户结构、产品分类等。

## 获取树形结构文档内容

### 核心方法：通过 `frappe.call` 调用服务器端方法

假设您有一个名为 `Department` 的树形结构文档类型（DocType），您可以通过以下客户端 JavaScript 代码获取其树形结构内容：

```javascript
frappe.call({
    method: 'frappe.client.get_tree',
    args: {
        doctype: 'Department', // 替换为您的树形结构文档类型
        parent: 'All Departments' // 可选参数：指定父节点，如不指定则获取所有根节点
    },
    callback: function(response) {
        // 返回的数据存储在 response.message 中
        console.log(response.message);
        // 这里可以处理树形结构数据，例如渲染到前端组件
    }
});
```

### 服务器端 Python 方法

您也可以自定义服务器端 Python 方法来获取树形结构数据。例如，在您的自定义应用中的 Python 文件中（如 `your_app/your_app/api.py`）定义以下方法：

```python
import frappe
from frappe.client import get_tree

@frappe.whitelist()
def get_custom_tree(doctype, parent=None):
    # 调用 Frappe 内置的 get_tree 方法获取树形结构
    return get_tree(doctype, parent)
```

然后在客户端调用这个自定义方法：

```javascript
frappe.call({
    method: 'your_app.your_app.api.get_custom_tree',
    args: {
        doctype: 'Department',
        parent: 'All Departments'
    },
    callback: function(response) {
        console.log(response.message);
    }
});
```

## 树形结构数据格式

服务器返回的树形结构数据通常是一个嵌套的字典列表，每个节点包含以下字段：

- `name`: 文档名称
- `parent_department`: 父节点名称（如果为根节点，则为空或特定值，如 "All Departments"）
- `is_group`: 标记是否为组（父节点）
- `children`: 子节点列表（如果存在）

例如，返回的数据可能如下所示：

```json
[
    {
        "name": "HR",
        "parent_department": "All Departments",
        "is_group": 1,
        "children": [
            {
                "name": "Recruitment",
                "parent_department": "HR",
                "is_group": 0,
                "children": []
            }
        ]
    }
]
```

## 直接使用 Frappe 的 REST API 获取

您还可以通过 Frappe 的 REST API 获取树形结构文档列表。例如，使用以下端点：

```
GET /api/resource/Department?fields=["name", "parent_department", "is_group"]&filters=[["is_group", "=", 1]]
```

然后在客户端解析这些数据并构建树形结构。

## 总结

获取树形结构文档内容的主要步骤包括：

1. **确定文档类型**：确认要获取的树形结构文档类型（如 `Department`）。
2. **选择获取方式**：
   - 使用 `frappe.call` 调用内置的 `frappe.client.get_tree` 方法。
   - 或自定义服务器端 Python 方法。
3. **处理返回数据**：解析返回的嵌套结构数据，并在前端进行渲染或进一步处理。