# 扫描器API (Scanner API)

## 概述
Frappe框架使用开源库[Html5-QRCode](https://github.com/mebjas/html5-qrcode)来提供基于设备摄像头扫描条形码、二维码等输入的功能。通过`frappe.ui.Scanner`类，开发者可以轻松实现单次或连续扫描。

## 主要类与方法

### `frappe.ui.Scanner`
通过`new frappe.ui.Scanner({ ... })`创建一个扫描器实例，用于启动摄像头扫描。

#### 配置选项

| 选项 | 类型 | 说明 |
|------|------|------|
| `container` | DOM 元素 | 用于显示摄像头视频画面的容器元素 |
| `dialog` | 布尔值 | 若设为 `true`，将在对话框中显示摄像头画面 |
| `multiple` | 布尔值 | 若设为 `false`，扫描到第一个结果后自动停止 |
| `on_scan` | 函数 | 扫描成功后的回调函数，接收扫描结果对象（如 `data.decodedText`） |

## 使用示例

### 示例 1：单次扫描（扫描后自动停止）
```javascript
new frappe.ui.Scanner({
    dialog: true,      // 在对话框中打开摄像头
    multiple: false,   // 只扫描一次
    on_scan(data) {
        console.log("扫描结果：", data.decodedText);
    }
});
```

### 示例 2：连续扫描（需手动停止）
```javascript
const scanner = new frappe.ui.Scanner({
    dialog: true,
    multiple: true,    // 允许连续扫描
    on_scan(data) {
        handleScannedBarcode(data.decodedText); // 自定义处理函数
    }
});

// 如需停止扫描，可调用：
// scanner.stop_scan();
```

## 注意事项
- 扫描功能依赖于用户授权使用摄像头。
- 使用 `dialog: true` 时，扫描界面将以模态对话框形式出现，用户可随时关闭。
- 如需在自定义容器中显示摄像头画面，请指定 `container` 选项，并确保其为有效的 DOM 元素。

## 高级用法

### 在自定义容器中显示扫描器
```javascript
// 假设页面中有一个id为scanner-container的元素
const scanner = new frappe.ui.Scanner({
    container: $('#scanner-container').get(0),
    multiple: true,
    on_scan(data) {
        // 处理扫描结果
        processScannedData(data.decodedText);
    }
});
```

### 扫描不同类型的码
Html5-QRCode库支持扫描多种类型的码，包括：
- 二维码（QR Code）
- 条形码（Bar Code）
- 其他常见一维码

## 相关链接
- [Html5-QRCode 项目地址](https://github.com/mebjas/html5-qrcode)
- 文档页面所属路径：`JS API` → `Scanner API`

如需进一步了解如何集成到具体表单或页面中，可以查阅Frappe官方文档中的"表单脚本"或"页面API"部分。