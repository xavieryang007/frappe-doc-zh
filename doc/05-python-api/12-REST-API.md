# REST API

Frappe框架为所有DocType自动生成了开箱即用的REST API。此外，您还可以通过点分模块路径调用任意的Python方法。

---

## 1. 认证 (Authentication)

有三种方式进行认证：

### 1.1 基于令牌的认证 (Token Based Authentication)
令牌是一对API密钥和API秘钥。
*   **生成步骤**：在用户列表中选择用户 -> 点击"设置"选项卡 -> 展开"API访问"部分 -> 点击"生成密钥"。保存弹出的API秘钥。
*   **使用方式**：将 `api_key` 和 `api_secret` 用冒号 `:` 连接，在请求头 `Authorization` 中传入。
    ```bash
    curl http://<base-url>/api/method/frappe.auth.get_logged_user -H "Authorization: token api_key:api_secret"
    ```

### 1.2 基于密码的认证 (Password Based Authentication)
依赖Cookie和会话数据。
*   **使用方式**：先调用登录接口获取会话，后续请求携带Cookie。
    ```bash
    # 登录
    curl --cookie-jar cookie.txt -X POST "http://<base-url>/api/method/login" -H 'Content-Type: application/json' -d '{"usr":"<username>", "pwd":"<password>"}'
    # 使用会话访问API
    curl --cookie cookie.txt "http://<base-url>/api/method/frappe.auth.get_logged_user"
    ```

### 1.3 访问令牌 (Access Token)
参考OAuth设置文档获取 `access_token`。
*   **使用方式**：在请求头 `Authorization` 中传入。
    ```bash
    curl http://<base-url>/api/method/frappe.auth.get_logged_user -H "Authorization: Bearer access_token"
    ```

---

## 2. 列出文档 (Listing Documents)

通过GET请求获取某个DocType的记录列表。

**端点**：`GET /api/resource/<DocType>`

**常用参数**：
*   `fields`：指定返回字段，例如 `fields=["name", "description"]`。
*   `filters`：过滤条件，格式为JSON数组，例如 `filters=[["status", "=", "Open"]]`。多个条件默认为AND关系。
*   `or_filters`：提供OR关系的过滤条件。
*   `order_by`：排序，例如 `order_by=creation%20desc` (URL编码的空格)。
*   `limit_start` 和 `limit_page_length`：分页参数。`limit` 是 `limit_page_length` 的别名。
*   `as_dict`：默认为`True`，返回字典列表；设为`False`返回列表的列表。
*   `debug`：设为`True`可在返回结果中查看执行的SQL语句。
*   `expand`: 展开链接字段，例如 `expand=["priority"]`，返回结果会包含链接文档的详细信息。

**示例响应**：
```json
{
  "data": [
    {"name": "doc1", "description": "Test 1"},
    {"name": "doc2", "description": "Test 2"}
  ]
}
```

---

## 3. CRUD 操作

确保设置请求头：
```http
Accept: application/json
Content-Type: application/json
```

### 3.1 创建 (Create)
*   **端点**：`POST /api/resource/<DocType>`
*   **请求体**：要创建的文档数据（JSON格式）。
*   **示例**：
    ```bash
    curl -X POST http://<base-url>/api/resource/ToDo -d '{"description": "New Todo"}'
    ```

### 3.2 读取 (Read)
*   **端点**：`GET /api/resource/<DocType>/<Document_Name>`
*   **参数**：`expand_links=True` 可以展开所有链接字段。
*   **示例**：
    ```bash
    curl http://<base-url>/api/resource/ToDo/doc1
    ```

### 3.3 更新 (Update)
*   **端点**：`PUT /api/resource/<DocType>/<Document_Name>`
*   **请求体**：只需包含要更新的字段（JSON格式）。
*   **示例**：
    ```bash
    curl -X PUT http://<base-url>/api/resource/ToDo/doc1 -d '{"description": "Updated Description"}'
    ```

### 3.4 删除 (Delete)
*   **端点**：`DELETE /api/resource/<DocType>/<Document_Name>`
*   **示例**：
    ```bash
    curl -X DELETE http://<base-url>/api/resource/ToDo/doc1
    ```

---

## 4. 远程方法调用 (Remote Method Calls)

可以调用任何被标记为 *whitelisted* 的Python方法。

*   **端点**：`/api/method/<dotted.method.path>`
*   **HTTP方法**：对于只读操作使用`GET`，对于会修改数据的操作使用`POST`。
*   **成功响应**：包含 `message` 键，其值为方法的返回值。
*   **错误响应**：包含 `exc` 和 `exc_type` 键，显示错误信息和异常类型。
*   **示例**：
    ```bash
    curl http://<base-url>/api/method/frappe.auth.get_logged_user
    # 返回 {"message": "administrator"}
    ```

---

## 5. 文件上传 (File Uploads)

使用专用的上传方法。

*   **端点**：`POST /api/method/upload_file`
*   **Headers**：需要包含认证信息（如Token）。
*   **Body**：使用 `multipart/form-data` 格式，文件字段名为 `file`。
*   **cURL示例**：
    ```bash
    curl -X POST http://<base-url>/api/method/upload_file \
      -H 'Authorization: token api_key:api_secret' \
      -F 'file=@/path/to/your/file.png'
    ```

---

**总结**
Frappe的REST API提供了对数据模型（DocType）完整的CRUD操作、灵活的查询、安全的认证机制以及执行自定义服务器端逻辑的能力，是进行系统集成和构建第三方应用的强大工具。