## Why

当前商品列表页面仅包含文字描述，缺乏视觉吸引力和专业感。为了提升用户体验并向现代电商视觉风格靠齐，需要引入商品图片展示，并采用 Modern Flat 风格的卡片布局。

## What Changes

- **[后端]** 在商品数据模型中增加 `imageUrl` 字段。
- **[后端]** 更新初始商品数据，为每个商品生成极简风格的 AI 图片链接。
- **[前端]** 重构 `App.vue` 中的商品列表，将纯文字卡片升级为带图片的 Modern Flat 图文卡片。
- **[前端]** 优化响应式网格布局，确保在单屏显示下视觉效果紧凑专业。

## Capabilities

### New Capabilities
- `frontend-ui`: 重构商品卡片 UI，引入图片展示并调整布局样式。

### Modified Capabilities
- `domain-model`: 扩展商品实体定义以包含 `imageUrl` 字段。
- `catalog-management`: 扩展商品模型以支持图片展示，并优化列表呈现逻辑。
- `product-query`: 更新商品查询返回字段以包含 `imageUrl`。

## Impact

- **API**: `/api/products` 返回的 JSON 将包含 `imageUrl` 字段。
- **UI**: 商品列表展示从纯文字变为图文卡片流。
- **数据**: `initialProducts` 将包含图片链接。
