# `competitor_lookup` —— 查竞品 / 商品列表查询

> 网页对应：大数据选品 → 查竞品（`POST /v3/api/competing-lookup`）。
> ✅ 已值级验证：B0DZBZG4TD · US · 202604，锚定 ASIN × 9 字段一致（`amzUnit=3000` 同名同值）。

## 用途
按 **ASIN / 关键词 / 品牌 / 卖家 / 类目** 查询 Amazon 商品**列表**，返回销量、销售额、BSR、价格、评分、卖家、变体等。
> 注意：这是「**列表查询**」，与 `/competitor-analysis`（单 ASIN 深度拆解，用 asin_detail 等）定位不同，二者互补。

## 请求参数（网页 → MCP）

| 网页字段 | MCP 字段 | 说明 |
| --- | --- | --- |
| `market` | `marketplace` | 站点 |
| `monthName` | `month` | 取末段 yyyyMM |
| `asins` | `asins` | 数组，最多 40 |
| `keywords` | `keyword` | 复数→单数 |
| `includeBrands` | `brand` | |
| `includeSellers` | `sellerName` | |
| `nodeIdPaths` | `nodeIdPath` / `nodeIdPaths` | 单串或数组 |
| `page` / `size` / `order` / `matchType` / `variation` | 同名 | variation 传 Y 去重 |

## 响应字段（⚠️ 改名规律同 product_research）

| 页面列 | 网页 API | MCP | 备注 |
| --- | --- | --- | --- |
| 月销量 | `totalUnits` | **`units`** | |
| 月销售额 | `totalAmount` | **`revenue`** | |
| 评分数 | `reviews` | **`ratings`** | |
| BSR | `bsrRank` | **`bsr`** | |
| 配送方式 | `sellerType` | **`fulfillment`** | FBA/FBM/AMZ |
| 亚马逊自营销量 | `amzUnit` | `amzUnit` | 同名锚定 |
| 均价/当前价/评分值/变体 | `averagePrice`/`price`/`rating`/`variations` | 同名 | |

## 陷阱
1. **ASIN 须真实存在**，否则 `total=0`。
2. **月份口径**：网页「最近30天」(`bsr_sales_nearly`) ≠ MCP 自然月，数值不完全相等；**字段名映射仍成立**。要做相等比对请两边都用自然月。
3. 响应读 MCP 名（units/revenue/ratings/bsr/fulfillment）。

> 可复现核对证据见附录：[`_verification/选市场-选产品-查竞品-核对报告.md`](_verification/选市场-选产品-查竞品-核对报告.md)（查竞品段：锚定 ASIN 字段一致）。
