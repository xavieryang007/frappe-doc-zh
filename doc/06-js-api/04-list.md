# 列表 (List)

## 概述
列表视图适用于除子表（Child Tables）和单文档类型（Single DocTypes）外的所有文档类型，提供强大的数据查看和操作功能。

## 列表视图功能
列表视图内置以下功能：
- 筛选（Filters）
- 排序（Sorting）
- 分页（Paging）
- 按标签筛选
- 视图切换（如报表、日历、甘特图、看板等）

## 自定义列表视图方法

### 标准列表JS文件配置
- 需在对应文档类型目录下创建 `{doctype}_list.js` 文件（例如 `note_list.js`）
- **示例配置代码**展示了多种自定义选项：
  ```javascript
  frappe.listview_settings['Note'] = {
      add_fields: ['title', 'public'],           // 添加要获取的字段
      filters: [['public', '=', 1]],            // 设置默认筛选条件
      hide_name_column: true,                    // 隐藏名称列
      get_indicator(doc) {                       // 自定义状态指示器
          if (doc.public) {
              return [__("Public"), "green", "public,=,Yes"];
          } else {
              return [__("Private"), "darkgrey", "public,=,No"];
          }
      },
      button: {                                  // 添加自定义按钮
          show(doc) { return doc.reference_name; },
          get_label() { return 'View'; },
          action(doc) { 
              frappe.set_route('Form', doc.reference_type, doc.reference_name); 
          }
      },
      formatters: {                              // 字段值格式化
          title(val) { return val.bold(); },
          public(val) { return val ? 'Yes' : 'No'; }
      }
  };
  ```

### 客户端脚本自定义
- 通过**客户端脚本（Client Script）**进行站点特定的列表视图定制
- 路径：**首页 > 自定义 > 客户端脚本 > 新建**
- 适用于特定站点的逻辑定制，跨站点共享需通过应用（Apps）实现

## 多按钮支持（开发版功能）
可在列表视图行中添加多个按钮，通过下拉菜单提供更多操作选项：
```javascript
frappe.listview_settings["ToDo"] = {
    button: { /* 主按钮配置 */ },
    dropdown_button: {
        get_label: __("Dropdown"),
        buttons: [
            { get_label: __("按钮1"), action: function(doc) { /* 操作1 */ } },
            { get_label: __("按钮2"), action: function(doc) { /* 操作2 */ } }
        ]
    }
};
```

## 关键配置选项
| 选项 | 描述 |
|------|------|
| `add_fields` | 添加要在列表中获取的额外字段 |
| `filters` | 设置默认的筛选条件 |
| `hide_name_column` | 是否隐藏名称列 |
| `get_indicator` | 自定义状态指示器函数 |
| `button` | 添加自定义行按钮 |
| `formatters` | 字段值的格式化显示函数 |
| `dropdown_button` | 添加下拉菜单多按钮支持 |

通过以上配置，开发者可以灵活地定制列表视图的外观和功能，满足不同业务场景的需求。