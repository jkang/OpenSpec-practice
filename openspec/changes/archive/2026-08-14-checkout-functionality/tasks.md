## 1. 后端实现 (Node.js)

- [x] 1.1 在 `src/services/order.js` 中增加结算逻辑方法，处理从购物车到订单的转换 (Node.js)
- [x] 1.2 在 `src/http/server.js` 中新增 `POST /api/checkout` 路由并调用 Service (Node.js)
- [x] 1.3 在 `__tests__/unit.spec.js` 中为结算逻辑编写单元测试 (Node.js)

## 2. 后端实现 (Python)

- [x] 2.1 在 `src/services/order.py` 中增加结算逻辑函数 (Python)
- [x] 2.2 在 `src/api/server.py` 中新增 `POST /api/checkout` 接口 (Python)
- [x] 2.3 在 `tests/test_smoke.py` 中添加结算流程的冒烟测试 (Python)

## 3. 前端实现 (Vue)

- [x] 3.1 在 `src/App.vue` 的 `<script setup>` 中增加 `isProcessing` 状态位 (Frontend)
- [x] 3.2 实现 `checkout` 方法，使用 `fetch` 调用后端的 `/api/checkout` (Frontend)
- [x] 3.3 在模板中为 `checkout-btn` 绑定点击事件，并根据结算结果清空购物车或报错 (Frontend)

## 4. 全链路验证

- [x] 4.1 启动 Node.js 后端并运行前端进行手动端端测试 (全部)
- [x] 4.2 验证结算成功后，购物车 UI 和后端存储均已清空 (全部)
- [x] 4.3 模拟库存不足场景，验证前端错误提示的正确性 (全部)
