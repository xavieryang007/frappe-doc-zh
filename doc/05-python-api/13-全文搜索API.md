# 全文搜索API (FullTextSearch API)

Frappe 框架对 [Whoosh](https://pypi.org/project/Whoosh/) 的封装。

## `update_index_by_name(self, doc_name)`

封装了 `update_index` 方法，通过文档名称获取文档并更新索引。**此函数会更改当前用户，因此仅应作为管理员或在后台任务中运行。**

**参数：**
- `self`（object）：FullTextSearch 实例
- `doc_name`（str）：待更新文档的名称

## `remove_document_from_index(self, doc_name)`

从搜索索引中移除文档。

**参数：**
- `self`（object）：FullTextSearch 实例
- `doc_name`（str）：待移除文档的名称

## `update_index(self, document)`

更新文档的搜索索引。

**参数：**
- `self`（object）：FullTextSearch 实例
- `document`（_dict）：包含 `title`、`path` 和 `content` 的字典

## `build_index(self)`

为所有已解析的文档构建索引。

## `search(self, text, scope=None, limit=20)`

从当前索引中搜索内容。

**参数：**
- `text`（str）：要搜索的字符串
- `scope`（str，可选）：限制搜索范围的参数，默认为 `None`
- `limit`（int，可选）：限制搜索结果数量，默认为 `20`

**返回：**
- `[list(_dict)]`：搜索结果列表

---

如果您需要进一步了解具体用法或示例，可以查阅 Frappe 官方文档或相关社区资源。