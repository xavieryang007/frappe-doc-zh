# 单一DocType (Single DocType)

## 单一DocType概述

- **定义**：单一DocType是一种在数据库中只存在**唯一实例**的DocType。它适用于存储不需要多个记录的配置或设置，例如系统设置（System Settings）。
- **用途**：常用于保存全局配置，如通知频率、网站设置等，确保整个系统只有一个有效配置。

## 核心特性

### 1. **唯一性**

- 每个单一DocType在数据库中仅有一个记录，无法创建多个实例。
- 例如，`System Settings` 作为一个单一DocType，只能通过唯一标识访问其配置。

### 2. **数据存储方式**

- 单一DocType的数据存储在数据库的 `tabSingles` 表中，**每个字段单独存储为一条记录**。
- 表结构包含以下列：
  - `doctype`：DocType名称。
  - `field`：字段名。
  - `value`：字段值。

### 3. **访问方法**

- 通过 `frappe.get_doc()` 直接获取唯一实例，无需指定名称或其他标识。  
**示例代码：**
  ```python
  settings = frappe.get_doc('System Settings')
  frequency = settings.notification_frequency  # 获取通知频率字段值
  ```

## 适用场景

- 系统全局配置（如邮件服务器设置、安全策略）。
- 应用级别的单例数据（如公司信息、版本号）。
- 避免重复记录的场景，确保数据一致性。

## 与普通DocType的区别

| 特性 | 普通DocType | 单一DocType |
|------|-------------|-------------|
| 实例数量 | 可创建多个记录 | 仅有一个实例 |
| 存储表 | `tab[DocType]`（如 `tabUser`） | `tabSingles` |
| 访问方式 | 需指定文档名（如 `frappe.get_doc('User', 'user1')`） | 直接通过DocType名获取（如 `frappe.get_doc('System Settings')`） |

## 注意事项

- 不支持版本控制（Versioning）或审计跟踪（Audit Trail），因为数据存储在 `tabSingles` 表中。
- 字段变更时，需直接更新对应记录，而非创建新文档。

## 总结

单一DocType是Frappe框架中用于管理**全局单例数据**的高效工具，通过简化的存储和访问机制，确保关键配置的唯一性和一致性。适用于系统设置、应用配置等场景。

如需进一步了解如何创建或自定义单一DocType，可参考Frappe文档中的 **"创建DocType"** 和 **"字段类型"** 章节。