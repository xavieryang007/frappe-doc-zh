# 图表API (Chart API)

## 概述
Frappe框架提供了易于使用且可配置的SVG图表功能，基于Frappe Charts库实现。您可以通过[Frappe Charts官方文档](https://frappe.io/charts)了解更多图表类型和配置选项。

## 主要类：frappe.ui.RealtimeChart
`frappe.ui.RealtimeChart` 是一个用于实现实时数据更新的图表类，扩展了基础Frappe Chart的功能。

### 构造函数
```javascript
new frappe.ui.RealtimeChart(dom_element, event_name, max_label_count, data)
```
- **dom_element**: 用于渲染图表的HTML元素（如`#chart`）。
- **event_name**: 用于接收实时数据的Socket事件名称。
- **max_label_count**: X轴允许的最大标签数量。
- **data**: 图表的初始数据（格式与Frappe Chart一致）。

### 示例代码
**前端初始化图表：**
```javascript
// 空数据初始化
const data = {
  datasets: [
    { name: "Some Data", values: [] }
  ]
};

// 创建实时图表
let chart = new frappe.ui.RealtimeChart("#chart", "test_event", 8, {
  title: "My Realtime Chart",
  data: data,
  type: "line",
  height: 250,
  colors: ["#7cd6fd", "#743ee2"]
});
```

**后端推送数据（Python示例，可通过定时任务执行）：**
```python
data = {
  'label': 1,          # X轴标签
  'points': [10]       # 数据点（对应数据集数量）
}
frappe.publish_realtime('test_event', data)
```

## 类方法说明

### 1. `start_updating()`
开始监听指定的Socket事件，并自动更新图表。
```javascript
chart.start_updating();
```

### 2. `stop_updating()`
停止监听实时数据更新。
```javascript
chart.stop_updating();
```

### 3. `update_chart(label, data)`
手动更新图表数据。
- **label**: X轴标签。
- **data**: 数据点数组（长度需与数据集数量一致）。
```javascript
chart.update_chart(2, [30]);
```

## 注意事项
- 确保前端已正确引入Frappe Charts库。
- 实时功能依赖于Frappe的Socket.IO机制，需确保后端正确配置事件推送。
- 图表类型（如折线图、柱状图）可通过`type`参数配置。

## 图表类型
通过构造函数中的`type`参数，可以指定不同类型的图表：
- `line` - 折线图
- `bar` - 柱状图
- `pie` - 饼图
- `percentage` - 百分比图
- `axis-mixed` - 混合图表

如需更详细的图表配置参数或高级用法，建议结合[Frappe Charts官方文档](https://frappe.io/charts)进行查阅。