# 设置LDAP集成

## 概述

轻量级目录访问协议（LDAP）是一种对象目录，通常用于集中认证，即常说的单点登录（SSO）。许多从中小型到大型的组织都部署了LDAP。

通过在Frappe中设置LDAP集成，您将能够使用您的LDAP凭据登录到您的Frappe应用程序。

## 1. 配置

LDAP设置页面上的许多字段是必填的。这些字段将在下文中以**粗体**字体清晰标示。

当配置LDAP设置并勾选"启用"复选框时，Frappe将验证您输入到字段中的数据。如果任何数据无效，设置将无法保存，并且您会看到错误消息。一旦纠正所有错误，您将能成功启用LDAP认证。

LDAP设置配置页面已分解为多个小部分。请参阅以下标题，了解如何配置每个部分。

要进行LDAP设置，请导航至：

> 首页 > 集成 > LDAP设置

**:memo: 提醒**
*不要忘记在将设置输入配置页面后，勾选页面顶部的"启用"框。*

### 1.1 LDAP服务器

Frappe已与OpenLDAP和Active Directory均经过测试。但任何符合标准的LDAP目录都可用作LDAP认证源。

- **LDAP目录服务器**：（下拉菜单）选择与您的LDAP目录匹配的选项。可用选项有：
  - `Active Directory`：用于Microsoft Active Directory。
  - `OpenLDAP`：用于OpenLDAP。
  - `Custom`：如果您需要调整LDAP组属性（例如，您的LDAP组设置为`groupofnames`或`uniquegroupofnames`等），请选择此选项。
- **LDAP服务器URL**：这是您的LDAP服务器的URL。必须为`ldap://您的服务器:端口`或`ldaps://您的服务器:端口`格式。

### 1.2 LDAP认证

此部分包含将绑定到目录服务器的用户的详细信息。

- **基本专有名称（DN）**：这是将绑定到LDAP的用户。此用户需要有权读取您下面输入的搜索路径。
- **基本DN的密码**：这是上述用户的密码，用于在您的LDAP服务器上查找用户详细信息。

### 1.3 LDAP搜索路径

此部分是每个LDAP目录对象的搜索路径。任何LDAP容器对象路径只要符合LDAP FDN（完整专有名称）格式即可使用，例如`ou=我的frappe对象,dc=example,dc=com`。此处提供的任何路径都要求绑定用户具有读写权限。

> 绑定用户需要写入权限，以便最终用户可以重置其密码。

- **用户的LDAP搜索路径**：这是您LDAP服务器中所有要能登录到Frappe应用程序的用户必须属于的搜索路径。
- **组的LDAP搜索路径**：这是您LDAP服务器中所有要能登录到Frappe应用程序的组必须属于的搜索路径。
- **LDAP搜索字符串**：此字段允许Frappe将Frappe登录屏幕中输入的用户名/电子邮件与LDAP服务器进行匹配。例如，根据您的偏好，您可以使用电子邮件地址或用户名。

您可以使用复杂的LDAP搜索过滤器，只要它们包含登录名占位符`{0}`并用括号`()`括起来即可。此占位符放在搜索过滤器中您希望匹配登录名的位置。任何您的LDAP目录支持且[ldap3 python模块](https://ldap3.readthedocs.io/en/latest/searches.html#the-ldap-filter)支持的过滤器都应有效。示例包括：

- `(uid={0})`
- `(SamAccountName={0})` - （用于Active Directory）
- `(&(description=*ACCESS:Frappe*)(uid={0}))` - 在OpenLDAP中使用描述过滤器，可以实现类似于Active Directory中`memberOf`的过滤效果，因为它只会选择在描述字段中包含`ACCESS:Frappe`的用户。
- `(&(SamAccountName={0})(memberof=cn=Domain Users,ou=Groups,DC=example,dc=com))` - （用于Active Directory）

### 1.4 LDAP用户创建与映射

此部分包含将从目录服务器读取并映射到Frappe用户的LDAP属性。

- **LDAP电子邮件字段**：指定包含用户电子邮件地址的LDAP属性。常用的LDAP属性是`mail`。
- **LDAP用户名字段**：指定包含用户用户名的LDAP属性。对于Active Directory是`sAMAccountName`，对于OpenLDAP是`uid`。
- **LDAP名字字段**：指定包含用户名字的LDAP字段。常用的是`givenName`或`sn`。

还有许多其他非强制性的LDAP属性可用于映射到Frappe用户。它们是：

- 中间名
- 电话
- 手机

> **安全警告**：请理解，如果您使用LDAP属性`mail`作为用户名字段，可能会导致安全隐患。`mail`在LDAP中不是唯一值。启用`mail`属性用于登录目的超出了本文档的范围。请查阅您的目录服务器文档以了解如何操作。

### 1.5 LDAP安全

此部分是可选的。但是，如果您想配置到目录服务器的安全连接，这是您输入TLS连接详细信息的地方。

- 在LDAP设置区域，有两个下拉菜单：
  1. **SSL/TLS模式** - 将其设置为**StartTLS**以使用StartTLS连接到您的LDAP服务器。如果您的LDAP服务器不支持StartTLS，设置此选项为StartTLS将导致错误`StartTLS is not supported`。如果收到此错误，请检查您的LDAP服务器配置。
  2. **需要可信证书** - 如果将其更改为：
     - `是`，则LDAP服务器提供的证书必须受Frappe服务器信任。
     - `否`，不建议使用此设置，因为它绕过了确保证书由可信证书颁发机构签名的检查。

如果您希望使用带有自签名（不受信任）证书的StartTLS，请将其设置为**否**。如果您不使用StartTLS，则忽略此设置。

- **SSL/TLS模式**：指定是否要在初始连接到LDAP服务器时启动TLS会话。
- **需要可信证书**：指定是否需要可信证书才能连接到LDAP服务器。

如果您指定可信证书，则需要指定证书文件的路径。这些文件需放置在您的Frappe服务器上，并且以下字段应为服务器上文件的绝对路径。证书字段包括：
- 私钥文件路径
- 服务器证书路径
- CA证书文件路径

> 要将您的Frappe实例配置为接受由您自己的"证书颁发机构"或"自签名证书"签名的证书，请查阅您的操作系统文档，了解如何将"证书颁发机构"/"自签名"证书添加到您的"受信任证书存储"中。

### 1.6 LDAP自定义目录

如果您选择**自定义**目录服务器，则此部分是**强制性的**。这里只有两个可用选项，它们与您的目录服务器使用的组有关。它们是：

- **_组对象类_**：这是您目录服务器内组的对象类。
- **_组成员属性_**：这是组内包含用户公共名称（CN）的LDAP属性。
- **_自定义组搜索_**：这可用于指定自定义LDAP组搜索，这对于某些LDAP提供商是必需的。

### 1.7 LDAP组映射

此部分包含用于映射LDAP组的详细信息。用户所属的任何组都将被映射到指定的Frappe角色。

> Frappe特性：如果您配置了组映射，每次用户登录时，他们的角色都会被检查并根据需要添加/删除。

- **创建时的默认角色**：当用户在您的Frappe应用程序中创建时，将被分配此默认角色。
- **LDAP组映射表**：您可以将多个LDAP组映射到Frappe角色。输入的LDAP组必须与LDAP的`cn`属性匹配，例如应输入`'Domain Users'`，而不是LDAP FDN条目`'cn=Domain Users,ou=Groups,dc=example,dc=com'`。

*示例*

| 序号 | LDAP组 | 角色 |
| :--- | :--- | :--- |
| 1 | Domain Users | Guest |
| 2 | Domain Administrators | Administrator |

- **LDAP组字段**（**弃用警告**）：这是用户LDAP对象中包含用户所属组列表的LDAP属性。如果您已按照上述所有设置进行配置，则此设置将不起作用。此字段仅存在于为当前布局之前配置LDAP的用户。此字段将在未来版本的Frappe中移除。

## 2. 使用LDAP登录Frappe

设置并启用LDAP后，登录屏幕上会启用**通过LDAP登录**选项。

*图1. 配置并启用LDAP后的Frappe应用程序登录屏幕。*
（文档此处通常应有一张图片，显示登录界面上的"Login Via LDAP"按钮或链接）

---

**总结**

本文档详细说明了在Frappe框架中配置LDAP集成以实现单点登录的全部步骤，涵盖了服务器连接、认证凭据、搜索路径、属性映射、安全设置、自定义目录配置和用户角色映射等关键方面。请务必根据您实际使用的LDAP服务器类型（如Active Directory或OpenLDAP）进行相应的配置。