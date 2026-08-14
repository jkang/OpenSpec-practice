## MODIFIED Requirements

### Requirement: 订单创建
系统 SHALL 支持从用户购物车结算生成订单，过程中处理库存扣减与购物车清空。为了提高结算的可靠性，系统 SHALL 验证结算请求中的价格一致性。

**Priority**: P0 (Critical)

**Rationale**: 订单创建是交易的核心流程，涉及多模块协调（Cart -> Catalog -> Order）。

#### Scenario: 成功创建订单
- **GIVEN** 用户购物车中有商品
- **AND** 商品库存充足
- **WHEN** 发送 POST /api/orders 携带 { userId } 或通过结算接口触发
- **THEN** 返回状态码 201
- **AND** 返回新创建的订单 Order
- **AND** 订单状态为 PENDING_PAYMENT
- **AND** 购物车被清空
- **AND** 库存被扣减

#### Scenario: 创建订单时购物车为空
- **GIVEN** 用户购物车为空
- **WHEN** 发送 POST /api/orders 携带 { userId }
- **THEN** 返回状态码 400
- **AND** 返回错误码 CART_EMPTY

#### Scenario: 创建订单时库存不足
- **GIVEN** 用户购物车中有商品
- **AND** 商品库存不足
- **WHEN** 发送 POST /api/orders 携带 { userId }
- **THEN** 返回状态码 409
- **AND** 返回错误码 OUT_OF_STOCK

#### Scenario: 幂等性创建订单
- **GIVEN** 用户携带 Idempotency-Key 请求头
- **WHEN** 重复发送相同的 POST /api/orders 请求
- **THEN** 返回相同的订单信息
- **AND** 不重复创建订单
