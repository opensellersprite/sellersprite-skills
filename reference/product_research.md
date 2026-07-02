# `product_research` —— 选产品 / 高级商品筛选

> 网页对应：大数据选品 → 选产品（`POST /v3/api/product-research`）。
> ✅ 已值级验证：yoga mat · US · 202604，5 个 ASIN × 9 字段 = 45/45 逐字段一致。

## 用途
按关键词 + 多维条件（销量/销售额/价格/BSR/评分/利润/配送/卖家/变体/重量等）筛选 Amazon 商品。

## 请求参数（网页 → MCP 改名）

| 网页字段 | MCP 字段 | 说明 |
| --- | --- | --- |
| `marketId` | `marketplace` | 1→US |
| `monthName` | `month` | 取末段 yyyyMM |
| `keyword` / `matchType` | 同名 | matchType 1词组/2模糊/3精确，默认 2 |
| `minSales/maxSales` | `minUnits/maxUnits` | 月销量 |
| `minAmount/maxAmount` | `minRevenue/maxRevenue` | 月销售额 |
| `minRanking/maxRanking` | `minSubBsrRank/maxSubBsrRank` | 子类 BSR 排名 |
| `minReviewsGrouth` | `minRatingsCv` | 月新增评分数 |
| `minTotalUnitsGrowth` | `minUnitsCr` | 月销量增长率 |
| `minDeliveryPrice` | `minFba` | 运费 |
| `minQuestions` | `minLqs` | Listing 质量分 |
| `minPrice/minBsr/minRatings/minRating/minProfit/minSellers/minVariations/minWeights/minFba` | 同名 | 直传 |

### 多值字段（MCP 期望逗号字符串，非数组）
`includeBrands` / `excludeBrands` / `includeSellers` / `excludeSellers` / `fulfillment`(FBA,FBM,AMZ) / `sellerNation` / `dimensionType`。
`badgeAC` / `badgeBS` / `badgeNR` 取值 `"Y"`。`weightUnit` 取值 `g/kg/ounces/pounds`（**不是** oz/lb）。

## 响应字段（⚠️ 网页 API ≠ MCP 字段名）

| 页面列 | 网页 API 字段 | MCP 字段 | 验证 |
| --- | --- | --- | --- |
| 月销量 | `totalUnits` | **`units`** | 57028=57028 ✅ |
| 月销售额 | `totalAmount` | **`revenue`** | 1596213.8 ✅ |
| 评分数 | `reviews` | **`ratings`** | 15948 ✅ |
| BSR | `bsrRank` | **`bsr`** | 82 ✅ |
| 配送方式 | `sellerType` | **`fulfillment`** | FBA ✅ |
| 亚马逊自营销量 | `amzUnit` | `amzUnit` | 30000 ✅（同名锚定） |
| 均价/当前价/评分值 | `averagePrice`/`price`/`rating` | 同名 | ✅ |

**改名规律（v3 JSON 响应通用）**：`totalUnits→units`、`totalAmount→revenue`、`reviews→ratings`、`bsrRank→bsr`、`sellerType→fulfillment`。

## 陷阱
1. **响应读 MCP 名**（units/revenue/ratings/bsr/fulfillment），别用网页名。
2. **月份未就绪**：202605 等月 `units/revenue` 可能为 None，取最近自然月（如 202604）。
3. 黑白名单/配送/卖家国家传**逗号字符串**；badges 传 `"Y"`。

> 可复现核对证据见附录：[`_verification/选市场-选产品-查竞品-核对报告.md`](_verification/选市场-选产品-查竞品-核对报告.md)（选产品段：45/45 字段一致）。
