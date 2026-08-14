## 1. 后端模型与数据扩展 (Node.js & Python)

- [x] 1.1 在 Node.js 的 `src/domain/Product.js` (JSDoc) 中增加 `imageUrl` 字段定义 (Node.js)
- [x] 1.2 在 Node.js 的 `data/products.json` 中为每个商品添加 Unsplash 图片链接 (Node.js)
- [x] 1.3 在 Python 的 `src/domain/models.py` 中的 `Product` Pydantic 模型里增加 `image_url: Optional[str]` 字段 (Python)
- [x] 1.4 更新 Python 后端的初始商品数据，包含对应的图片链接 (Python)

## 2. 前端 UI 重构 (Vue 3)

- [x] 2.1 修改 `App.vue`，实现 Modern Flat 风格的响应式网格布局 (`grid-cols-1 md:grid-cols-3`) (Frontend)
- [x] 2.2 实现 `ProductCard` 组件逻辑，支持 4:3 比例的图片展示，并添加 1px 细线边框 (Frontend)
- [x] 2.3 在前端添加 `searchQuery` 状态和计算属性，实现基于名称和分类的实时搜索过滤 (Frontend)
- [x] 2.4 为 `<img>` 标签添加图片加载失败的处理函数，显示占位图 (Frontend)

## 3. 全链路验证与同步

- [x] 3.1 启动 Node.js 后端并验证 `GET /api/catalog` 接口返回的数据包含有效的 `imageUrl` (全部)
- [x] 3.2 启动 Python 后端并验证 `GET /products` 接口返回的数据包含有效的 `image_url` (全部)
- [x] 3.3 在前端验证搜索功能的实时性及其在“未找到匹配项”时的空状态展示 (全部)
- [x] 3.4 验证全链路交互：从主页图片展示到加入购物车，确保购物车中的缩略图也正确显示 (全部)
