# 子表/子文档类型 (Child / Table DocType)

## 核心概念：什么是子表 DocType？

在 Frappe 框架中，**子表 DocType**（也称为 **子文档类型** 或 **子表**）是一种特殊类型的 DocType，其主要用途是**在父文档中存储多条相关记录**，实现"一对多"的关系。

- **类比理解**：想象一个"销售订单"（父文档），它下面会有多条"订单商品"（子文档）。一个订单对应多个商品，这就是典型的一对多关系。"订单商品"就是一个子表 DocType。
- **核心特性**：子表 DocType 的记录**不能独立存在**，它们必须依附于一个父文档。当父文档被保存、提交、取消或删除时，其下的所有子表记录也会被相应地处理。

## 如何创建子表 DocType？

创建过程与普通 DocType 相似，但有一个关键区别：

1. 进入 Frappe Desk 的 "DocType List" 页面。
2. 点击 "New" 创建一个新的 DocType。
3. 在设置中，找到并勾选 **"Is Child Table"**（是子表）选项。这是将普通 DocType 定义为子表的关键步骤。
4. 像设计普通 DocType 一样，为你的子表添加所需的字段（例如，`item_code`, `qty`, `rate` 等）。

## 如何在父 DocType 中链接子表？

创建好子表 DocType 后，你需要将其链接到父 DocType 上：

1. 编辑你的**父 DocType**（例如 "Sales Order"）。
2. 在 "Fields" 部分，添加一个新字段。
3. 将这个字段的 **"Field Type"** 设置为 **"Table"**。
4. 在 **"Options"**（选项）一栏中，填写你刚刚创建的子表 DocType 的名称（例如 "Order Item"）。
5. 保存父 DocType。

现在，当你创建或编辑一个父文档（如销售订单）时，表单中会出现一个表格区域，你可以在其中动态地添加、编辑和删除子表记录。

## 数据结构与关键属性

子表记录在数据库和 Python API 中具有一些特殊的元数据字段，用以维护它们与父文档的关系。

**示例代码（Python API 获取数据）：**
```python
# 获取一个父文档（例如 'Person'）
person = frappe.get_doc('Person', '000001')

# 将文档转换为字典查看结构
person_dict = person.as_dict()
print(person_dict)
```

**输出的数据结构可能如下：**
```python
{
    'first_name': 'John',
    'last_name': 'Doe',
    # 'qualifications' 是父文档中类型为 Table 的字段名
    'qualifications': [
        {
            'title': 'Frontend Architect',
            'year': '2017',
            # 以下是系统自动管理的子表属性
            'name': 'a1b2c3d4e5', # 子文档的唯一标识
            'parent': '000001',    # 父文档的名称
            'parenttype': 'Person', # 父文档的 DocType
            'parentfield': 'qualifications', # 父文档中链接此子表的字段名
            'idx': 1               # 此子文档在表格中的行序号（从1开始）
        },
        {
            'title': 'DevOps Engineer',
            'year': '2016',
            'name': 'f6g7h8i9j0',
            'parent': '000001',
            'parenttype': 'Person',
            'parentfield': 'qualifications',
            'idx': 2
        },
    ]
}
```

### 关键属性详解：

- `parent`：**父文档名称**。标明这条子记录属于哪个具体的父文档（例如 `'000001'`）。
- `parenttype`：**父文档类型**。标明父文档的 DocType（例如 `'Person'`）。这确保了子记录与正确的"父类"关联。
- `parentfield`：**父文档中的字段名**。标明父文档中是哪个"Table"字段链接了这个子表（例如 `'qualifications'`）。一个父文档可以有多个不同的子表字段。
- `idx`：**行序号**。表示该子记录在父文档表格中的显示和存储顺序（从 1 开始）。

> **注意**：在大多数日常操作中，你不需要手动设置这些属性。Frappe 框架会在你通过标准界面或 `frappe.get_doc()` 等方法操作文档时自动管理它们。

## 核心特点总结

1. **依附性**：子表记录的生命周期完全由其父文档管理。没有父文档，子记录无法存在。
2. **表字段（Table Field）**：在父文档中，子表通过特定字段类型为 **"Table"** 的字段引入。
3. **自动关系管理**：系统通过 `parent`, `parenttype`, `parentfield` 等隐藏字段自动维护父子关系。
4. **UI 集成**：在 Frappe Desk 的表单界面上，子表以可编辑的表格形式呈现，支持行的增、删、改、排序。
5. **数据完整性**：这种设计确保了数据的强关联性和完整性，避免了"孤儿"数据的存在。

## 常见使用场景

- **销售订单/发票中的商品行**
- **博客文章中的评论**
- **学生成绩单中的各科成绩**
- **个人简历中的工作经历或教育背景**
- **物料清单（BOM）中的组件列表**

## 总结

子表DocType是Frappe框架中实现一对多关系的核心机制，通过依附父文档、自动关系管理、UI集成等特性，确保数据的完整性和一致性，适用于各种需要维护多个子记录的业务场景。