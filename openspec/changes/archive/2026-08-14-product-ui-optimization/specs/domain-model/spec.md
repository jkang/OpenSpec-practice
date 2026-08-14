## MODIFIED Requirements

### Requirement: 商品实体定义
**Priority**: P0
**Rationale**: 商品图片是现代电商展示的核心要素，必须在领域模型层级进行支持。

系统 SHALL 定义商品实体，包含唯一标识、名称、价格、库存以及商品图片链接。

#### Scenario: 创建有效商品

- **GIVEN** 需要创建新商品
- **WHEN** 提供商品信息 { id, name, priceCents, stock, imageUrl }
- **THEN** 商品实体创建成功
- **AND** id 格式为 prod_xxxx
- **AND** priceCents >= 0
- **AND** stock >= 0
- **AND** imageUrl 必须是有效的 URL 字符串
