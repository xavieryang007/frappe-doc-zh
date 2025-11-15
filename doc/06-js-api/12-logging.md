# 日志记录 (Logging)

## 概述
日志记录是软件开发中用于跟踪软件运行时发生的事件的重要功能，在多租户架构下对于问题排查尤为关键。Frappe框架使用Python内置的`logging`模块，支持按站点（Site）和整个工作台（Bench）进行日志管理。

## 核心功能

### 日志轮转
- Frappe 13版本默认每个日志文件最大为100kB，并保留最近20个文件。

### 关键API与方法

#### `frappe.log_level`
- 用于设置Frappe进程的日志级别（如DEBUG、INFO、WARNING等）。

#### `frappe.utils.logger.set_log_level(level)`
- 动态设置日志级别并重新生成日志记录器。

#### `frappe.loggers`
- 存储当前进程中的所有活跃日志记录器（`dict`类型）。
- 键名格式：`{模块名}-{站点名}`，例如`frappe.web-frappeframework.com`。

#### `frappe.logger()`
- **功能**：创建或获取一个日志记录器（`logging.Logger`对象），支持站点级和工作台级日志记录。
- **参数**：
  - `module`：日志器名称，也决定了日志文件名。
  - `allow_site`：是否允许按站点记录日志（默认可自动识别当前站点）。
  - `with_more_info`：是否记录请求的附加信息（如表单数据）。
  - `max_size`：单个日志文件的最大大小（字节）。
  - `file_count`：保留的日志文件数量。

## 使用示例

以下代码展示了如何在API中使用日志记录：

```python
import frappe

# 设置日志级别为 DEBUG
frappe.utils.logger.set_log_level("DEBUG")

# 创建日志记录器
logger = frappe.logger("api", allow_site=True, file_count=50)

@frappe.whitelist()
def update(value):
    user = frappe.session.user
    logger.info(f"{user} 使用参数 value={value} 调用了 update 接口")

    current_value = frappe.get_single_value("Value", "counter")
    updated_value = current_value + value
    logger.debug(f"计算过程：{current_value} + {value} = {updated_value}")

    frappe.db.set_value("Value", "Value", "counter", updated_value)
    logger.info(f"{user} 将值更新为 {updated_value}")

    return updated_value
```

**日志输出示例**：
```
2020-07-31 16:06:55,067 INFO api user@example.com 使用参数 value=100 调用了 update 接口
2020-07-31 16:06:55,067 DEBUG api 计算过程：1000 + 100 = 1100
2020-07-31 16:06:55,068 INFO api user@example.com 将值更新为 1100
```

## 日志文件位置
- 工作台级日志：`./logs/{模块名}.log`
- 站点级日志：`./sites/{站点名}/logs/{模块名}.log`

## 日志级别
日志级别从低到高排序，每个级别包含所有更高级别的消息：
- `DEBUG`：详细的调试信息
- `INFO`：一般信息，确认程序正常运行
- `WARNING`：表示有意外情况发生，但不影响程序运行
- `ERROR`：由于更严重的问题，程序可能无法执行某些功能
- `CRITICAL`：严重错误，程序本身可能无法继续运行

## 最佳实践
1. **使用适当的日志级别**：
   - 调试时使用DEBUG
   - 常规信息使用INFO
   - 潜在问题使用WARNING
   - 错误情况使用ERROR

2. **结构化日志消息**：
   ```python
   logger.info(f"用户 {user} 在模块 {module} 中执行了操作 {action}")
   ```

3. **敏感信息**：
   - 避免在日志中记录密码、令牌等敏感信息

4. **性能考虑**：
   - 在生产环境中避免过度的DEBUG日志
   - 合理配置日志轮转以避免日志文件过大

## 总结
Frappe提供了灵活的日志记录机制，支持多级日志、动态配置和按站点隔离。通过`frappe.logger()`方法，开发者可以轻松集成日志功能，便于调试和监控应用运行状态。