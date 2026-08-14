## MODIFIED Requirements

### Requirement: 按 ID 查询单个商品
**Priority**: P0
**Rationale**: 确保在查看商品详情时能够展示其对应的图片。

系统 SHALL 提供按商品 ID 查询单个商品详情的接口。返回的信息 SHALL 包含 `imageUrl`。

#### Scenario: 查询存在的商品

- **WHEN** 客户端发送 `GET /api/products/{id}`，且该 ID 对应的商品存在
- **THEN** 系统返回 200 状态码及该商品的完整 JSON 对象（id、name、priceCents, stock, imageUrl）
