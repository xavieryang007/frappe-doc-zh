# SQLite搜索

## 概述
SQLite Search是Frappe应用程序的全文本搜索框架，基于SQLite的FTS5引擎构建。它提供以下高级功能：
- 拼写纠正
- 基于时间的近期性评分
- 自定义排名
- 权限感知过滤
- 可扩展的评分管道

## 快速开始

### 1. 创建搜索类
创建一个继承自 `SQLiteSearch` 的类，并定义索引名称、架构和可索引的文档类型。

```python
# my_app/search.py
from frappe.search.sqlite_search import SQLiteSearch

class MyAppSearch(SQLiteSearch):
    INDEX_NAME = "my_app_search.db"
    INDEX_SCHEMA = {
        "metadata_fields": ["project", "owner", "status"],
        "tokenizer": "unicode61 remove_diacritics 2 tokenchars '-_'",
    }
    INDEXABLE_DOCTYPES = {
        "Task": {
            "fields": ["name", {"title": "subject"}, {"content": "description"}, "modified", "project", "owner", "status"],
        },
        "Issue": {
            "fields": ["name", "title", "description", {"modified": "last_updated"}, "project", "owner"],
            "filters": {"status": ("!=", "Closed")},
        },
    }

    def get_search_filters(self):
        accessible_projects = frappe.get_all("Project", filters={"owner": frappe.session.user}, pluck="name")
        return {"project": accessible_projects} if accessible_projects else {"project": []}
```

### 2. 注册搜索类
在 `hooks.py` 中注册搜索类：

```python
# hooks.py
sqlite_search = ['my_app.search.MyAppSearch']
```

### 3. 创建API端点
创建一个白名单方法以暴露搜索功能：

```python
# my_app/api.py
import frappe
from my_app.search import MyAppSearch

@frappe.whitelist()
def search(query, filters=None):
    search_obj = MyAppSearch()
    return search_obj.search(query, filters=filters)
```

### 4. 构建索引
通过控制台或程序构建索引：

```python
from my_app.search import MyAppSearch
search_obj = MyAppSearch()
search_obj.build_index()
```

## 工作原理

### 索引过程
- **全量索引构建**：创建临时SQLite数据库，批量插入文档，构建拼写纠正词典，原子替换现有索引。
- **单文档索引**：实时更新单个文档，增量更新索引和词典。

### 搜索过程
1. 权限过滤
2. 查询预处理
3. 拼写纠正
4. FTS5查询执行
5. 结果处理（自定义评分、排名、内容格式化）

## 配置

### INDEX_SCHEMA
定义索引结构：

```python
INDEX_SCHEMA = {
    "text_fields": ["title", "content"],
    "metadata_fields": ["project", "owner", "status", "priority"],
    "tokenizer": "unicode61 remove_diacritics 2 tokenchars '-_@.'"
}
```

### INDEXABLE_DOCTYPES
指定可索引的文档类型及其字段映射：

```python
INDEXABLE_DOCTYPES = {
    "Task": {
        "fields": [
            "name",
            {"title": "subject"},
            {"content": "description"},
            {"modified": "creation"},
            "project",
            "owner"
        ],
        "filters": {
            "status": ("!=", "Cancelled"),
            "docstatus": ("!=", 2)
        }
    }
}
```

## 功能与自定义

### 权限过滤
通过 `get_search_filters()` 方法实现基于用户权限的过滤。

### 自定义评分
使用 `@SQLiteSearch.scoring_function` 装饰器创建自定义评分函数：

```python
@SQLiteSearch.scoring_function
def _get_priority_boost(self, row, query, query_words):
    priority = row.get("priority", "Medium")
    if priority == "High":
        return 1.5
    elif priority == "Medium":
        return 1.1
    return 1.0
```

### 近期性提升
系统自动使用 `modified` 字段进行时间加权评分。如需使用其他时间字段，可映射至 `modified`。

### 文档准备
重写 `prepare_document()` 方法实现自定义文档处理逻辑。

### 拼写纠正
基于trigram相似性自动纠正查询中的拼写错误。

### 内容处理
自动清理HTML内容，提取纯文本供搜索使用。

### 仅标题搜索
通过 `title_only=True` 参数实现仅搜索标题字段。

### 高级过滤
支持多值过滤和单值过滤：

```python
filters = {
    "project": ["PROJ001", "PROJ002"],
    "owner": current_user
}
```

## 索引管理

### 自动索引处理
注册搜索类后，框架自动处理：
- 迁移后索引构建
- 定期索引验证（每15分钟）
- 实时文档更新（插入、更新、删除事件）

### 手动索引处理
若不注册搜索类，可手动控制索引生命周期，例如通过后台任务构建索引。

## API参考

### `search(query, title_only=False, filters=None)`
执行搜索，返回格式化结果。

**返回示例：**
```json
{
    "results": [
        {
            "doctype": "Task",
            "name": "TASK-001",
            "title": "Fix login bug",
            "content": "User cannot login after password reset...",
            "score": 0.85,
            "original_rank": 3,
            "rank": 1
        }
    ],
    "summary": {
        "duration": 0.023,
        "total_matches": 15,
        "corrected_words": {"loggin": "login"},
        "applied_filters": {"status": ["Open"]}
    }
}
```

### 其他方法
- `build_index()`：全量构建索引
- `index_doc(doctype, docname)`：索引单个文档
- `remove_doc(doctype, docname)`：移除单个文档
- `is_search_enabled()`：检查搜索是否启用
- `index_exists()`：检查索引是否存在
- `get_search_filters()`：获取当前用户的搜索过滤器（需子类实现）