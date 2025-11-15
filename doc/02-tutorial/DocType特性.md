# DocType特性 (DocType Features)

## 1. **命名规则（Naming）**

- **默认行为**：创建文档时，系统会自动生成一个随机哈希值作为文档名称。
- **自定义命名**：可通过在DocType的"Naming"部分设置"Auto Name"字段（例如输入`field:article_name`），使文档名称直接使用指定字段的值（如文章标题）。
- **唯一性约束**：自定义命名后，文档名称必须在同一DocType中保持唯一，无法重复创建同名文档。
- **示例**：将"Auto Name"设置为`field:article_name`后，新建文章时"Article Name"字段的值会成为文档名称，并可在数据库中直接查询到。

## 2. **表单布局（Form Layout）**

- **字段排列优化**：通过添加**分栏（Column Break）** 和**分节（Section Break）** 字段，可以灵活调整表单布局，提升空间利用率。
- **隐藏字段**：若某些字段（如"Image"）无需在表单中显示，可将其隐藏。
- **操作演示**：通过动态GIF展示了如何在DocType的字段设置中添加布局元素并调整显示逻辑。

## 3. **表单设置（Form Settings）**

- **图片显示**：在"Image Field"中指定字段（如`image`），图片将显示在表单左上角。
- **允许重命名**：启用"Allow Rename"后，用户可修改文档名称。

## 4. **权限控制（Permissions）**

- **角色分配**：可为不同角色（如"Librarian""Library Member"）设置针对DocType的操作权限。
- **权限类型**：
  - **Librarian**：拥有所有操作权限（创建、读取、写入、删除等）。
  - **Library Member**：仅限读取权限。
- **测试建议**：创建不同角色的用户并登录，验证其可执行的操作是否符合权限设置。

## 下一步建议

- 完成本部分后，可继续学习**Controller Methods**，进一步了解如何通过控制器方法扩展DocType逻辑。

如需查看具体操作截图或深入某个特性，请参考原文中的GIF图示及数据库查询示例。