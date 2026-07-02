# `traffic_keyword` —— 关键词反查 / ASIN 流量词

> 网页对应：关键词优化 → 关键词反查（`POST /v3/api/relation/reversing`）。
> ✅ 已值级验证：B0DZBZG4TD · US · 202604，交集词 × 23 字段**逐位相等（零改名）**，连 `supplyDemandRatio=78.88`、嵌套 `rankPosition` 都一致。

## 用途
输入 ASIN，返回其实际获得曝光/流量的关键词列表：搜索量、自然/广告排名、流量占比、自然/广告占比、点击/曝光、PPC 竞价、供需比、转化指标等。

## 请求参数（网页 → MCP）

| 网页字段 | MCP 字段 | 说明 |
| --- | --- | --- |
| URL `?market=COM` | `marketplace` | COM→US, CO.JP→JP, CO.UK→UK, … |
| `asin` | `asin` | **单值字符串** |
| `month` | `month` | yyyyMM |
| `limit` / `skip` | `size` / `page` | page = skip/limit + 1 |
| `keyword` | `keyword` | 关键词过滤 |
| `badges` / `trafficKeywordTypes` / `conversionKeywordTypes` | 同名 | 多选；大小写不敏感 |
| `order`(整数UI码) / `desc` | `order.field`(字符串) / `order.desc` | 默认 trafficPercentage |

### ⚠️ 关键：MCP 单工具不覆盖全部网页筛选
- `traffic_keyword`：返回与网页 **1:1 全字段**，但**无任何区间筛选参数**。
- `traffic_extend`：支持区间筛选，但**丢弃** rankPosition/adPosition/naturalRatio/adRatio/impressions/clicks（属"关键词拓展"，列不全）。
- 推荐做法：用 `traffic_keyword` 保真取全字段，区间筛选（searches/purchases/bid/rank/impressions/clicks/...）在**客户端**对响应字段过滤。

## 响应字段（零改名，仅 keywords→keyword 复数差异）
`keyword`、`keywordCn/keywordJp`、`searches`、`products`、`purchases`、`purchaseRate`(0~1)、`bid/bidMin/bidMax`、`rankPosition`(对象,取 `.position`=自然排名)、`adPosition`(对象,广告排名)、`searchesRank`、`supplyDemandRatio`(**真实比值,不×100**)、`trafficPercentage`、`naturalRatio`、`adRatio`、`monopolyClickRate`、`top3ClickingRate`、`top3ConversionRate`、`clicks`、`impressions`、`calculatedWeeklySearches`、`latest{1,7,30}daysAds`。

## 陷阱
1. **刻度**：`purchaseRate/trafficPercentage/naturalRatio/adRatio/monopolyClickRate/top3*` 为 **0~1，展示 ×100**；`supplyDemandRatio` 已是真实比值（如 78.88）**不换算**。
2. `rankPosition`/`adPosition` 是对象 `{page,index,position}`，展示取 `.position`。
3. **别用 `keyword_order`** 当反查全集：它只返回转化质量子集（searchRank/monopolyClickRate/cvsShareRate/top3*），字段远少于网页。
4. 网页区间筛选 `minTrafficPercentage/max` 按 0~100 填、响应 0~1，客户端比较需 ×100。

> 可复现核对证据见附录：[`_verification/关键词反查-核对报告.md`](_verification/关键词反查-核对报告.md)（23 字段 × 3 词逐位相等）。
