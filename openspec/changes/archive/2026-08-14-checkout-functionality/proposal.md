## Why

目前用户可以在购物车中添加商品，但无法完成购买流程。实现结算功能是为了打通从购物车到订单的闭环，让用户能够正式提交订单并完成交易。

## What Changes

- **前端 (Vue)**: 
  - 为购物车面板增加“去结算”按钮的点击事件处理。
  - 调用后端结算接口，并在成功后清除本地购物车状态。
  - 增加简单的结算成功/失败提示。
- **后端 (Node.js & Python)**:
  - 新增结算接口（POST `/api/checkout` 或集成到 `/api/orders`），接收购物车 ID 或明细并生成订单。
  - 实现从购物车提取数据、验证库存、计算总价并保存订单的业务逻辑。
  - **BREAKING**: 订单接口可能需要支持从购物车对象直接初始化的参数结构。

## Capabilities

### New Capabilities
- `checkout-management`: 处理从购物车状态转换到正式订单状态的业务逻辑，包括数据转换、库存锁定预校验等。

### Modified Capabilities
- `cart-management`: 需要支持结算后的购物车清空逻辑。
- `order-management`: 需要支持从现有购物车数据快速生成订单的能力。

## Impact

- **受影响的实现版本**: 全部 (Node.js / Python / Frontend)。
- **后端**: 修改 `src/services/order.js` (Node.js) 和 `src/services/order.py` (Python) 以支持结算逻辑。
- **前端**: 修改 `src/App.vue` 中的 `checkout-btn` 处理函数。
- **API**: 新增或更新订单相关的 REST 接口。
- **SLO 目标**: 结算接口延迟应在 200ms 以内，支持 50 TPS 的并发处理。
