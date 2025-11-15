# 表单导览 (Form Tours)

## 概述
表单导览是Frappe框架提供的一种功能，用于为复杂的DocType（文档类型）创建交互式教程，帮助用户了解表单中各字段的用途和操作步骤。通过简单的配置，即可生成分步骤的引导提示。

## 创建表单导览的步骤
1. **打开创建页面**：在Frappe的"Awesome Bar"（顶部搜索栏）中输入"new form tour"并回车。
2. **填写基本信息**：
   - **标题（Title）**：例如"创建自定义字段"。
   - **关联的DocType（Reference DocType）**：选择需要添加导览的文档类型。
   - **添加步骤（Steps）**：为每个字段设置说明。
3. **保存文档**：完成配置后保存。

## 配置选项

### 表单导览全局设置
1. **是否标准（Is Standard）**：
   - 若勾选，导览将作为标准配置以JSON格式存储。
   - 仅可在开发者模式下设置。
2. **完成后保存（Save on Completion）**：
   - 若启用，导览的最后一步会提示用户保存文档。
3. **显示首个文档导览（Show First Document Tour）**：
   - 启用后，将为该DocType的第一个现有文档（而非新建表单）展示导览。
4. **包含名称字段（Include Name Field）**：
   - 对于需要用户手动命名的DocType，启用此项会将名称字段设为导览的第一步。

### 导览步骤设置
1. **字段（Field）**：选择需要高亮说明的字段。
2. **标题（Title）与描述（Description）**：用于解释字段的作用及相关信息。
3. **位置（Position）**：设置提示框相对于字段的显示位置（如顶部、左侧等）。
4. **下一步条件（Next Condition）**：
   - 可输入JavaScript条件表达式，控制何时进入下一步。
   - 示例：`eval: doc.priority != ""`（表示任务优先级已设置时才继续）。
5. **是否为表格字段（Is Table Field）**：
   - 若字段位于子表格中，需勾选此项。
6. **父字段（Parent Field）**：
   - 仅当"Is Table Field"启用时显示，用于选择所属的子表格字段。

## 触发导览
在DocType的客户端脚本中，通过以下代码初始化和启动导览：
```javascript
frappe.ui.form.on('Your DocType', {
    onload: function(frm) {
        const tour_name = 'Your Form Tour Name'; // 填写导览名称
        frm.tour.init({ tour_name }).then(() => frm.tour.start());
    }
});
```

## 高级用法

### 条件性触发
可以根据特定条件决定是否显示导览：
```javascript
frappe.ui.form.on('Your DocType', {
    onload: function(frm) {
        // 只对新用户显示导览
        if (frappe.session.user === 'new-user@example.com') {
            const tour_name = 'Your Form Tour Name';
            frm.tour.init({ tour_name }).then(() => frm.tour.start());
        }
    }
});
```

### 跳过特定字段
可以使用特定字段作为跳过导览的条件：
```javascript
frappe.ui.form.on('Your DocType', {
    onload: function(frm) {
        // 检查用户是否已经看过导览
        if (!frm.doc.skip_tour) {
            const tour_name = 'Your Form Tour Name';
            frm.tour.init({ 
                tour_name,
                onComplete: function() {
                    // 导览完成后，更新标记字段
                    frm.set_value('skip_tour', 1);
                }
            }).then(() => frm.tour.start());
        }
    }
});
```

## 最佳实践
1. **简洁明了**：每个步骤的描述应该简洁明了，避免过长文本。
2. **逻辑连贯**：步骤顺序应与用户实际操作流程相符。
3. **条件控制**：合理使用"Next Condition"确保用户完成必要操作后再继续。
4. **字段覆盖**：为复杂字段和重要字段创建导览，特别是那些初次使用时不明显或有特殊要求的字段。
5. **测试优化**：反复测试导览流程，确保每个步骤都能正确触发和跳转。

## 注意事项
- 导览功能依赖于Frappe的Form API，确保代码插入正确的事件（如`onload`）。
- 步骤中的"Next Condition"需为有效的JS表达式，并以`eval:`开头。
- 表单导览可以通过用户首选项禁用，不应作为必须完成的强制步骤。
- 对于多语言环境，确保导览文本支持多语言翻译。