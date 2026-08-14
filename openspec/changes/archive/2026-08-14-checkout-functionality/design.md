## Context

目前系统已具备基础的商品管理、购物车管理及订单查询能力（详见 `proposal.md`）。后端（Node.js/Python）采用分层架构，前端（Vue 3）通过 `App.vue` 单文件管理状态。本次设计需在现有架构基础上引入结算逻辑。

## Goals / Non-Goals

**Goals:**
- 实现跨技术栈（Node.js, Python, Vue）一致的结算逻辑。
- 确保结算过程的原子性：验证购物车 -> 创建订单 -> 扣减库存 -> 清空购物车。
- 前端界面保持极简扁平化风格。

**Non-Goals:**
- 不涉及真实的支付网关集成（目前仅模拟到订单创建成功）。
- 不处理复杂的促销或优惠券逻辑。

## Decisions

### 1. API 接口设计
- **选择**: 在订单控制器中新增 `POST /api/checkout` 接口，接收 `{ userId }`。
- **理由**: 虽然可以直接用 `POST /api/orders`，但 `checkout` 语义更明确，专门用于处理从“购物车”到“订单”的转换逻辑，方便未来扩展预结算校验。
- **替代方案**: 直接复用 `POST /api/orders`。考虑后认为 `checkout` 可以更好地封装“清空购物车”这一副作用。

### 2. 架构流程 (Mermaid-style ASCII)
```text
[Frontend: Vue] --(POST /api/checkout)--> [HTTP Layer]
                                             |
                                      [Service Layer: OrderService]
                                             |
        +--------------------+---------------+-------------------+
        |                    |               |                   |
[Repo: CartRepo]     [Repo: CatalogRepo] [Repo: OrderRepo] [Repo: ProductRepo]
(Read & Clear)       (Check Stock)       (Create Order)    (Deduct Stock)
```

### 3. 前端 UI 组件层级与状态管理
- **组件**: 继续在 `App.vue` 中实现。
- **状态管理**: 
  - 使用 Vue 3 `ref` 管理 `cart` 数组。
  - 新增 `isProcessing` 状态防止重复提交。
  - 结算成功后，直接重置 `cart.value = []`。

### 4. 异常处理
- 如果库存不足，后端返回 `409 Conflict`，前端弹出简单错误提示（不使用 UI 组件库，仅原生 alert 或内嵌文字）。

## Risks / Trade-offs

- **[Risk] 并发下单导致超卖** → **Mitigation**: 后端在 Service 层处理订单创建时，需先锁定/检查库存。由于目前是单机内存存储，采用同步校验即可。
- **[Trade-off] 结算接口的幂等性** → 暂时不引入复杂的幂等 Token 机制，但在前端通过按钮禁用 (`isProcessing`) 降低重复提交风险。
