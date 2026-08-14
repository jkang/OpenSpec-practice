## Context

本项目是一个极简电商系统，包含 Node.js、Python 和 Vue 三种技术栈实现。目前商品展示页面仅包含文字描述，需要扩展数据模型并重构前端 UI 以支持图片展示和 Modern Flat 风格的卡片布局。

## Goals / Non-Goals

**Goals:**
- 在 Node.js 和 Python 后端商品模型中增加 `imageUrl` 字段。
- 更新初始数据，为所有商品提供有效的 Unsplash 图片链接。
- 在 Vue 前端实现基于 1px 边框的 Modern Flat 商品卡片，包含 4:3 比例的图片展示。
- 实现基于商品名称的实时搜索过滤功能。

**Non-Goals:**
- 不涉及用户上传图片的功能（图片链接仅由初始数据或管理员通过 API 提供）。
- 不涉及图片缓存或 CDN 优化。

## Decisions

### 1. 后端模型扩展
- **决策**: 在 Node.js 的 `Product` 类和 Python 的 Pydantic 模型中新增 `imageUrl` 字段（可选字符串）。
- **理由**: 保持前后端数据结构一致，确保 API 返回的数据包含 UI 渲染所需的视觉信息。
- **替代方案**: 使用单独的图片映射表。由于系统较小，直接扩展实体模型更为简洁。

### 2. 前端 UI 架构 (对齐 Prototype)
- **决策**: 使用 Tailwind CSS 的 `aspect-4-3` 类来约束图片容器，并使用 `border-slate-200` 实现 1px 细线边框。
- **理由**: 严格遵循 Modern Flat 设计规范，确保在不同分辨率下图片的比例始终统一。
- **UI 组件层级**:
  - `App.vue` (根容器，处理单屏布局 `h-screen overflow-hidden`)
    - `Header` (包含搜索框)
    - `Main` (网格布局 `grid-cols-1 md:grid-cols-3`)
      - `ProductCard` (图片容器 4:3 + 商品信息 + 购买按钮)
    - `CartSidebar` (侧边栏)

### 3. 全链路交互流程
- **流程**:
  1. 用户在前端搜索框输入内容。
  2. Vue 状态 `searchQuery` 变化，触发 `filteredProducts` 计算属性更新。
  3. UI 实时重绘，仅展示匹配项。
  4. 点击“加入购物车”时，前端状态 `cart` 更新，侧边栏同步显示包含图片的缩略图。

## Risks / Trade-offs

- **[Risk]** Unsplash 图片链接失效 → **Mitigation**: 在前端 `<img>` 标签中添加 `@error` 处理，加载失败时回退到占位图。
- **[Trade-off]** 单屏布局在商品极多时可能导致局部滚动复杂 → **Mitigation**: 严格限制网格的最大列数，并使用 `no-scrollbar` 保持界面整洁。
