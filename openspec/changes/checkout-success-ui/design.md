## Context

目前的结算逻辑在 `App.vue` 的 `checkout` 函数中直接使用 `alert()`。我们需要将其重构为响应式的 UI 状态。详见 [proposal.md](proposal.md)。

## Goals / Non-Goals

**Goals:**
- 在 `App.vue` 中集成结算成功模态框。
- 使用 Vue 3 响应式状态控制模态框的显示。
- 确保 UI 视觉与 [prototype-management.html](prototypes/checkout-management.html) 完全一致。

**Non-Goals:**
- 修改后端结算 API。
- 引入重量级的第三方 UI 库（继续使用 Tailwind CSS）。

## Decisions

### 1. 模态框实现方式
- **方案**: 直接在 `App.vue` 的模板末尾添加条件渲染的模态框 HTML 结构。
- **理由**: 目前项目为单页面单组件架构，直接集成比拆分独立组件更符合现有的极简风格。
- **替代方案**: 创建 `SuccessModal.vue`。由于项目目前主要在单个 `App.vue` 中维护，暂不引入额外的组件文件。

### 2. 状态管理
- **方案**: 新增 `isCheckoutSuccess` (boolean) 和 `lastOrderId` (string) 响应式变量。
- **理由**: 简单直接，易于在 `checkout` 函数中更新。

### 3. 视觉规范
- **方案**: 严格遵循 Modern Flat 风格。
- **细节**: 使用 `border-slate-200` (1px)，`bg-white/80` 背景遮罩，`backdrop-blur-sm` 增加层次感。

## 架构图 (ASCII)

```text
[App.vue]
   |
   +-- [Header]
   +-- [Main]
   |     +-- [ProductGrid]
   |     +-- [CartAside] --> triggers checkout()
   |
   +-- [SuccessModal] (v-if="isCheckoutSuccess")
         +-- Display orderId
         +-- Reset cart
         +-- Close -> isCheckoutSuccess = false
```

## 全链路流程

1. 用户点击“结算”。
2. `checkout()` 函数发起异步请求。
3. 请求成功：
   - 记录 `order.id` 到 `lastOrderId`。
   - 设置 `isCheckoutSuccess = true`。
   - 清空 `cart.value`。
4. 模态框显示。
5. 用户点击“继续购物”：
   - 设置 `isCheckoutSuccess = false`。

## Risks / Trade-offs

- [Risk] 模态框遮罩层层级冲突 → [Mitigation] 使用 `z-50` 确保模态框在最顶层。
- [Risk] 图标未渲染 → [Mitigation] 确保 `lucide-vue` 图标在 `App.vue` 中已正确导入和使用。
