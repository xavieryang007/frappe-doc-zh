# Jinja API

Frappe框架提供了一系列可在Jinja模板中使用的白名单方法。

## frappe.format
- **描述**：将原始值（数据库中存储的格式）转换为用户可读的格式。例如，将`2019-09-08`转换为`08-09-2019`。
- **用法**：
  ```jinja
  {{ frappe.format('2019-09-08', {'fieldtype': 'Date'}) }}
  ```
  **输出**：`09-08-2019`

## frappe.format_date
- **描述**：将日期字符串格式化为可读的长格式。
- **用法**：
  ```jinja
  {{ frappe.format_date('2019-09-08') }}
  ```
  **输出**：`September 8, 2019`

## frappe.get_url
- **描述**：返回站点URL。
- **用法**：
  ```jinja
  {{ frappe.get_url() }}
  ```
  **输出**：`https://frappe.io`

## frappe.get_doc
- **描述**：根据文档类型和名称获取文档对象。
- **用法**：
  ```jinja
  {% set doc = frappe.get_doc('Task', 'TASK00002') %}
  {{ doc.title }} - {{ doc.status }}
  ```
  **输出**：`Buy Eggs - Open`

## frappe.get_all
- **描述**：返回指定文档类型的所有记录列表。如果未指定`fields`参数，则仅返回文档的`name`字段。
- **签名**：
  ```python
  frappe.get_all(doctype, filters, fields, order_by, start, page_length)
  ```
- **用法**：
  ```jinja
  {% set tasks = frappe.get_all('Task', filters={'status': 'Open'}, fields=['title', 'due_date'], order_by='due_date asc') %}
  {% for task in tasks %}
  ### {{ task.title }}
  Due Date: {{ frappe.format_date(task.due_date) }}
  {% endfor %}
  ```
  **输出**：
  ```
  ### Redesign Website
  Due Date: September 8, 2019
  
  ### Add meta tags on websites
  Due Date: September 22, 2019
  ```

## frappe.get_list
- **描述**：与`frappe.get_all`类似，但会根据当前会话用户的权限过滤记录。

## frappe.db.get_value
- **描述**：从文档中获取单个字段的值（或多个字段的值列表）。
- **用法**：
  ```jinja
  {% set company_abbreviation = frappe.db.get_value('Company', 'TennisMart', 'abbr') %}
  {{ company_abbreviation }}
  
  {% set title, description = frappe.db.get_value('Task', 'TASK00002', ['title', 'description']) %}
  ### {{ title }}
  {{ description }}
  ```
  **输出**：
  ```
  TM
  
  ### Buy Eggs
  Buy eggs from the market
  ```

## frappe.db.get_single_value
- **描述**：从单文档类型（Single DocType）中获取字段值。
- **用法**：
  ```jinja
  {% set timezone = frappe.db.get_single_value('System Settings', 'time_zone') %}
  {{ timezone }}
  ```
  **输出**：`Asia/Kolkata`

## frappe.get_system_settings
- **描述**：从系统设置（System Settings）中获取字段值。
- **用法**：
  ```jinja
  {% if frappe.get_system_settings('country') == 'India' %}
  Pay via Razorpay
  {% else %}
  Pay via PayPal
  {% endif %}
  ```
  **输出**：`Pay via Razorpay`

## frappe.get_meta
- **描述**：获取文档类型的元数据，包含字段、标题字段、图像字段等信息。
- **用法**：
  ```jinja
  {% set meta = frappe.get_meta('Task') %}
  Task has {{ meta.fields | len }} fields.
  {% if meta.get_field('status') %}
  It also has a Status field.
  {% endif %}
  ```
  **输出**：
  ```
  Task has 18 fields.
  It also has a Status field.
  ```

## frappe.get_fullname
- **描述**：返回指定用户邮箱的全名。如果未指定用户，则默认为当前登录用户。
- **用法**：
  ```jinja
  The fullname of faris@erpnext.com is {{ frappe.get_fullname('faris@erpnext.com') }}
  The current logged in user is {{ frappe.get_fullname() }}
  ```
  **输出**：
  ```
  The fullname of faris@erpnext.com is Faris Ansari
  The current logged in user is John Doe
  ```

## frappe.render_template
- **描述**：使用上下文渲染Jinja模板字符串或文件。
- **用法**：
  ```jinja
  {{ frappe.render_template('templates/includes/footer/footer.html', {}) }}
  
  {{ frappe.render_template('{{ foo }}', {'foo': 'bar'}) }}
  ```
  **输出**：`bar`

## frappe._ 或 _()
- **描述**：用于字符串翻译。
- **用法**：
  ```jinja
  {{ _('This string should get translated') }}
  ```
  **输出**（假设当前语言为印地语）：`इस तार का अनुवाद होना चाहिए`

## frappe.session.user
- **描述**：返回当前会话用户。

## frappe.session.csrf_token
- **描述**：返回当前会话的CSRF令牌。

## frappe.form_dict
- **描述**：如果模板在Web请求中评估，`frappe.form_dict`是查询参数字典，否则为`None`。

## frappe.lang
- **描述**：翻译函数使用的当前语言（两位小写代码）。