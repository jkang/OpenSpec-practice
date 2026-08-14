## MODIFIED Requirements

### Requirement: 商品列表查询
**Priority**: P0
**Rationale**: 确保前端能够获取并展示商品的视觉信息，这是提升专业感的基础。

系统 SHALL 提供商品列表查询接口。返回的商品对象 SHALL 包含 `imageUrl` 字段以供前端展示。

#### Scenario: 获取所有商品（无过滤）

- **GIVEN** 系统中存在包含图片信息的商品数据
- **WHEN** 用户请求 GET /api/products（不带 name 参数）
- **THEN** 返回状态码 200
- **AND** 返回商品数组 Product[]，其中每个商品均包含 `imageUrl` 字段

### Requirement: 商品上架
**Priority**: P1
**Rationale**: 完善商品管理闭环，支持从源头录入图片信息。

系统 SHALL 支持商品上架功能，接收包含图片链接的商品信息并创建新商品记录。

#### Scenario: 上架新商品

- **GIVEN** 管理员需要添加带图片的商品
- **WHEN** 发送 POST /api/products 携带商品信息 { name, priceCents, stock, imageUrl }
- **THEN** 返回状态码 201
- **AND** 返回包含 `imageUrl` 的商品对象 Product
