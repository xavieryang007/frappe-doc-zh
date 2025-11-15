# 对话框API (Dialog API)

Frappe 框架提供了一组标准、交互式且灵活的对话框，易于配置和使用。Python 环境下的 Dialog API 主要用于在服务器端请求/响应周期内向用户显示消息。

## 核心方法

### 1. `frappe.msgprint`

此方法在请求/响应周期内工作，向发起请求的 Desk 登录用户显示消息。

**语法:**
```python
frappe.msgprint(msg, title, raise_exception, as_table, as_list, indicator, primary_action, is_minimizable, wide, realtime)
```

**参数说明:**

*   **`msg`** (必需): 要显示的消息内容。
*   **`title`**: 对话框模态窗口的标题。
*   **`as_table`**: 如果 `msg` 是一个列表的列表（例如 `[['行1列1', '行1列2'], ['行2列1', '行2列2']]`），则将其渲染为 HTML 表格。
*   **`as_list`**: 如果 `msg` 是一个列表（例如 `['项目1', '项目2', '项目3']`），则将其渲染为 HTML 无序列表。
*   **`primary_action`**: 绑定一个主要的服务器端或客户端操作。接受一个字典参数（详见下文）。
*   **`raise_exception`**: 指定触发的异常类型（例如 `FileNotFoundError`）。如果提供，将在显示消息后抛出此异常。
*   **`is_minimizable`**: 允许用户最小化对话框模态窗口。值为 `True` 或 `False`。
*   **`wide`**: 显示一个更宽的模态窗口。值为 `True` 或 `False`。
*   **`realtime`**: 如果为 `True`，则通过 WebSocket 立即发布消息，而不是添加到响应消息日志中。值为 `True` 或 `False`。

**基础示例:**
```python
frappe.msgprint(
    msg='该文件不存在。',
    title='错误',
    raise_exception=FileNotFoundError
)
```

**使用 `primary_action` 的示例:**
`primary_action` 参数是一个字典，用于定义用户点击主要按钮时执行的操作。

```python
frappe.msgprint(
    msg='此操作需要确认。',
    title='确认',
    primary_action={
        'label': _('执行操作'), # 按钮上显示的文字
        'server_action': 'dotted.path.to.server.method', # 服务器端方法（点分路径）
        'client_action': 'dotted.path.to.client.method', # 客户端JavaScript函数（点分路径，必须是全局可用函数）
        'hide_on_success': True, # 操作成功完成后关闭消息对话框
        'args': args # 传递给 server_action 或 client_action 的参数
    }
)
```
**注意:** `primary_action` 中可以包含 `server_action` **或** `client_action`，用于指定在服务器端或客户端执行的方法。JavaScript 函数必须是全局可用的。

---

### 2. `frappe.throw`

此方法是 `frappe.msgprint` 的包装器，它会**同时**显示一个消息对话框**并**抛出一个异常。通常用于验证失败或错误处理。

**语法:**
```python
frappe.throw(msg, exc, title, is_minimizable, wide, as_list, primary_action)
```

**参数说明:**
大部分参数与 `frappe.msgprint` 相同。
*   **`msg`** (必需): 要显示的错误消息。
*   **`exc`**: 指定要抛出的异常类型。**默认为 `ValidationError`**。如果不需要特定异常，通常可以省略。
*   **`title`**: 对话框的标题。
*   **`is_minimizable`**, **`wide`**, **`as_list`**, **`primary_action`**: 功能同 `frappe.msgprint`。

**示例:**
```python
frappe.throw(
    title='错误',
    msg='该文件不存在，无法继续操作。',
    exc=FileNotFoundError # 可选，不指定则默认抛出 ValidationError
)
```

## 重要提示

*   **执行环境**: 这些 Python API 主要在 Frappe 的服务器端控制器方法、API 接口或其他请求处理函数中使用。
*   **客户端 API**: 文档提到 Frappe 还提供了功能更丰富的 [Javascript Dialog API](https://docs.frappe.io/framework/user/en/api/dialog)，用于在浏览器客户端直接创建和操作对话框。如果您需要在客户端（例如在 Form Script 中）触发对话框，应使用那个 API。
*   **用户反馈**: 此 API 的主要目的是向当前登录的 Desk 用户提供即时反馈，例如操作结果、错误信息或需要确认的交互。

## 总结表格

| 方法 | 主要用途 | 关键特性 |
| :--- | :--- | :--- |
| **`frappe.msgprint`** | 向用户显示信息性消息、警告或确认对话框。 | 支持格式化列表/表格，可绑定自定义操作按钮，可选择是否抛出异常。 |
| **`frappe.throw`** | 显示错误消息并中断当前操作流程（抛出异常）。 | 是 `msgprint` 的便捷包装，默认抛出 `ValidationError`，常用于数据验证。 |