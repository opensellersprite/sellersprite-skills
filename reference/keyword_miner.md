# `keyword_miner` —— 关键词挖掘 / 种子词扩展

> 网页对应：关键词优化 → 关键词挖掘（`POST /v3/api/keyword-miner`）。
> ✅ 已值级验证：phone stand · US · 202605，`total=326` 与网页一致，前 3 词 × 23 字段中 22 一致（唯一缺口 `avgReviews`）。

## 用途
输入种子词（或 ASIN），挖掘相关长尾词及需求/竞争指标：搜索量、购买量、购买率、商品数、供需比、PPC 竞价、点击集中度、SPR、标题密度等。

## 请求参数（网页 → MCP）

| 网页字段 | MCP 字段 | 说明 |
| --- | --- | --- |
| `keyword` | `keyword` | 种子词或 ASIN |
| `market`（整数） | `marketplace` | 1=US,2=CA,3=UK,4=DE,5=FR,6=IT,7=ES,8=JP,9=IN,10=MX,11=BR,12=AU,13=AE |
| `pageNum` / `pageSize` | `page` / `size` | |
| `orderBy`(整数UI码) / `desc` | `order.field` / `order.desc` | 默认 searches |
| `historyDate` | `historyDate` | yyyyMM 自然月 |
| `filterRootWord`(0全部/1只含词根) / `amazonChoice` | 同名 | |
| `includeKeywords` / `excludeKeywords` | 同名 | |

### 服务端直名区间筛选（min/max 成对）
`minRelevancy`、`minSearchRank`、`minSearch`、`minPurchases`、`minPurchasesRate`、`minSPR`、`minTitleDensity`、`minProducts`、`minSupplyDemandRatio`、`minAdProducts`、`minMonopolyClickRate`、`minBid`、`minWordCount`（MCP 另支持 `minPrice/minRating/minRatings`、`keywordList` 批量精确）。

### ⚠️ 客户端兜底筛选（MCP 无该区间参数，但响应有该字段）
`minImpressions/max`（impressions）、`minClicks/max`（clicks）、`minCvsShareRate/max`（cvsShareRate）。
网页 `departmentList` 类目过滤：keyword_miner 无类目参数 → 客户端按 `item.departments` 过滤；类目维度选品建议改用 `keyword_research`。

## 响应字段（近乎零改名）
`keyword`、`keywordCn/keywordJp`、`searches`、`purchases`、`purchaseRate`(0~1)、`products`、`adProducts`、`supplyDemandRatio`(真实比值)、`avgPrice`、`avgRating`、`bid/bidMin/bidMax`、`cvsShareRate`(0~1)、`wordCount`、`titleDensity`、`spr`、`searchRank`、`clicks`、`impressions`、`monopolyClickRate`(0~1)、`amazonChoice`、`departments`。

### 唯一缺口
网页 `avgReviews`（平均评分数）→ MCP `avgRatings` **返回 None**，无法替代，展示标「—」。

## 陷阱
1. **刻度**：`purchaseRate/cvsShareRate/monopolyClickRate` 为 0~1，展示 ×100；`supplyDemandRatio` 真实比值不换算。
2. **工具选型**：种子词扩展用 `keyword_miner`；类目维度关键词选品（带增长率）用 `keyword_research`。
3. `matchType`、`keywordBidMatchType` 为 UI/展示选项，忽略。

> 可复现核对证据见附录：[`_verification/关键词挖掘-核对报告.md`](_verification/关键词挖掘-核对报告.md)（total=326 一致，22/23 字段相等）。
