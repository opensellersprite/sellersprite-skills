# `market_research` —— 选市场 / 市场全景

> 网页对应：大数据选品 → 选市场（`POST /v2/market-research`）。
> ✅ 已值级验证（真实抓包 + MCP 回跑）：Mat Bags · US · 202604，`topProducts/brands/sellers = 100/67/68`、`totalUnits=18254`、`totalProducts=292` 全部逐字段一致。

## 用途
给父类目（节点/关键词）+ 多维筛选，**单次调用**返回匹配的细分市场列表，每行含 ~100 个市场级指标（规模、集中度梯度、新品、卖家结构）。

## 请求参数（关键）

| 参数 | 类型 | 说明 |
| --- | --- | --- |
| `marketplace` | string | 站点，默认 US |
| `nodeIdPath` | string | 类目节点 `parentId:childId`（优先） |
| `departmentKeyword` / `keyword` | string | 类目路径关键词 / 类目关键词 |
| `month` | string | yyyyMM，**当前月常未就绪取最近月** |
| `newProduct` | int | 新品定义月数 1/3/6/12，默认 6 |
| `topNum` | int | 头部商品数，默认 10 |
| `sellerLocation` | string | 卖家所属地，**单值才生效**；多值需拆成 N 次单值调用再并集 |
| `min*/max*` 筛选 | number | 见下方刻度说明 |

### ⚠️ 筛选刻度
- **集中度/占比类筛选**（`*Crn` 商品/品牌/卖家集中度、`*Proportion` 占比）是 **0~1 小数**：表单按百分比填则需 ÷100 再传。

## 响应字段（关键）

| 字段 | 含义 | 备注 |
| --- | --- | --- |
| `nodeLabelName` / `nodeLabelPath` | 细分市场名 / 路径 | |
| `topProducts` | 样本商品数（默认近30天销量前100） | |
| `brands` / `sellers` | 品牌数 / 卖家数 | **不是** topBrands/topSellers |
| `totalProducts` | 类目在售商品总数 | 页面「商品总数」列 |
| `totalUnits` / `totalRevenue` | 样本月总销量 / 销售额 | |
| `avgUnits` / `avgRevenue` / `avgPrice` / `avgBsr` / `avgRating` / `avgRatings` | 各项均值 | |
| `top{3,5,10,20}{Product,Brand,Seller}Crn` | 分层集中度 | **0~1，展示 ×100** |
| `fbaProportion` / `fbmProportion` / `amazonSelfProportion` | 卖家结构占比 | **已是 0~100 百分数，不再 ×100** |
| `returnRatio` | 退货率 | **已是百分数，不 ×100** |
| `l{1,3,6,12}NewRatio` / `NewCount` | 分层新品占比 / 数量 | Ratio **0~1，展示 ×100** |

### 网页→MCP 改名规律
`HeadListing*→Top*`、`*Ratio→*Proportion`、`*Sales→*Units`、`*Reviews→*Ratings`、`*TotalProducts→*GoodsCount`、`marketId→marketplace`、`monthName→month`、`topn→topNum`、`newReleaseNum→newProduct`。

## 陷阱
1. **根节点统计全 0**：`product_node` 首个候选常是根类目（`totalProducts=0`），必须选叶子节点。
2. **集中度 ×100、`returnRatio`/`*Proportion` 不换算**（前者 0~1，后者已是百分数）。
3. **`sellerLocation` 多选无效**：MCP 只接受单值，多选拆分并集。
4. **月份未就绪**：当前月常返回 0，取最近自然月。

> 可复现核对证据见附录：[`_verification/选市场-选产品-查竞品-核对报告.md`](_verification/选市场-选产品-查竞品-核对报告.md)。
