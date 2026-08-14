## ADDED Requirements

### Requirement: 状态反馈模态框规范
**Priority**: P1
**Rationale**: 统一系统内的状态反馈视觉语言，提升专业感。

系统 SHALL 使用 Modern Flat 风格的模态框展示关键操作的结果（如结算成功）。模态框必须具备 1px 边框、纯白背景，并使用内容决定大小的紧凑布局。

#### Scenario: 展示成功模态框
- **GIVEN** 操作（如结算）已成功完成
- **WHEN** 触发反馈展示
- **THEN** 系统在页面中央展示一个 1px 边框 (`border-slate-200`) 的模态框
- **AND** 包含至少一个明确的动作按钮（如“继续购物”）用于关闭模态框
- **AND** 背景使用半透明白色 (`bg-white/80`) 遮罩

<details>
<summary>View UI Prototype Code</summary>

```html
<div v-if="showSuccess" class="fixed inset-0 z-50 flex items-center justify-center p-4 bg-white/80 backdrop-blur-sm">
    <div class="w-full max-w-xs bg-white border border-slate-200 p-8 space-y-6 text-center">
        <!-- 成功图标 -->
        <div class="flex justify-center">
            <i data-lucide="check-circle" class="w-12 h-12 text-slate-900"></i>
        </div>
        <!-- 内容和按钮省略... -->
    </div>
</div>
```
</details>
