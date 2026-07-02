# 关键词选品研究

基于关键词的市场需求分析，从搜索趋势、竞争强度、转化能力等维度评估关键词价值，辅助选品和投放决策。

## 使用方式

用户输入: `/keyword-research [关键词]`

参数说明:
- 第1个参数: 目标关键词（必填）
- 可附加参数: 站点(默认US)、最低搜索量、最大商品数等筛选条件

## 执行步骤

### 第1步: 并行获取关键词全景数据

> 以下工具互相独立，**必须并行调用**以节省等待时间。

同时调用以下 6 个工具:

**1. `keyword_research`** - 关键词市场全景:
- marketplace: 用户指定站点
- keywords: 用户输入的关键词
- size: 15

**2. `keyword_miner`** - 关键词深度挖掘:
- marketplace: 用户指定站点
- keyword: 用户输入的关键词
- size: 15

**3. `aba_research_weekly`** - ABA 周度排名趋势:
- marketplace: 用户指定站点
- includeKeywords: 用户输入的关键词

**4. `aba_research_monthly`** - ABA 月度趋势:
- marketplace: 用户指定站点
- includeKeywords: 用户输入的关键词

**5. `keyword_research_trends`** - 关键词搜索趋势:
- marketplace: 用户指定站点
- keyword: 用户输入的关键词

**6. `aba_research_trend`** - ABA 趋势分析（可按周/月粒度）:
- marketplace: 用户指定站点
- keyword: 用户输入的关键词

### 第2步: Google 趋势验证

调用 `google_trend` 验证外部搜索趋势:

参数:
- marketplace: 用户指定站点
- keyword: 用户输入的关键词
- googleProp: "shoppingCart"（购物搜索）
- monthly: true

获取: Google Shopping 搜索热度趋势，判断是否为季节性、上升期或衰退期。

### 第3步: 生成关键词研究报告

综合所有数据，输出关键词研究报告:

#### 一、关键词概览

| 指标 | 数据 | 评价 |
|------|------|------|
| 关键词 | xxx | - |
| 月搜索量 | xxx | 高/中/低 |
| 月购买量 | xxx | - |
| 购买率 | xx% | 高/中/低 |
| 商品数 | xxx | 多/中/少 |
| 供需比 | xx | 供不应求/供需平衡/供过于求 |
| PPC竞价 | $x.xx | 高/中/低 |
| 点击集中度 | xx% | 垄断/分散 |
| 搜索增长率 | xx% | 上升/平稳/下降 |

#### 二、关键词价值评估

**市场需求评估 (1-10分)**:
- 搜索量水平
- 购买量与购买率
- 增长趋势（周/月/年）

**竞争强度评估 (1-10分)**:
- 商品数量
- 头部垄断程度（点击集中度）
- PPC竞价水平
- 标题密度

**综合可行性评分**:
- 是否值得进入
- 适合进入方式（新品/广告/SEO）

#### 三、关联关键词矩阵

| 关键词 | 月搜索量 | 购买率 | 商品数 | 供需比 | PPC竞价 | SPR | 机会评级 |
|--------|---------|--------|--------|--------|---------|-----|---------|
| word1  | xxx     | xx%    | xxx    | xx     | $x.xx   | xx  | ★★★★★  |
| word2  | xxx     | xx%    | xxx    | xx     | $x.xx   | xx  | ★★★★☆  |
| ...    | ...     | ...    | ...    | ...    | ...     | ... | ...     |

关键词评级标准:
- ★★★★★ 高搜索 + 低竞争 + 高转化 = 蓝海机会词
- ★★★★☆ 中高搜索 + 中等竞争 = 值得投入词
- ★★★☆☆ 有搜索但竞争较大 = 需要差异化
- ★★☆☆☆ 搜索量低或竞争过激 = 谨慎投入
- ★☆☆☆☆ 已被垄断或需求不足 = 不建议

#### 四、趋势分析
- ABA排名趋势（周度/月度）
- Google Shopping 搜索热度趋势
- 关键词生命周期判断（新兴/成长/成熟/衰退）
- 季节性特征（如有）

#### 五、关键词选品建议
- 基于搜索量推荐的产品方向
- 核心关键词与长尾关键词布局建议
- 广告投放优先级排序
- 自然SEO与广告投放的投入建议

#### 六、竞品关键词机会
- 该关键词下的头部竞品概况
- 新品切入的关键词策略
- 避免正面竞争的差异化关键词

## 输出格式

使用 Markdown 表格、星级评分和趋势文字描述呈现。机会评级使用星号直观表示。

## 字段映射与口径（已值级验证）

> 种子词扩展用 `keyword_miner`（网页「关键词挖掘」后端）。已核对：phone stand · US · 202605，`total=326` 与网页一致、22/23 字段逐位相等。详见 [`keyword_miner`](../reference/keyword_miner.md)。

- **近乎零改名**：`searches/purchases/purchaseRate/products/supplyDemandRatio/cvsShareRate/spr/titleDensity/searchRank` 等均同名。
- **唯一缺口**：网页 `avgReviews`（平均评分数）→ MCP `avgRatings` **返回 None**，展示标「—」。
- **刻度**：`purchaseRate/cvsShareRate/monopolyClickRate` 为 0~1，展示 ×100；`supplyDemandRatio` 真实比值不换算。
- **大部分区间筛选服务端直名支持**（minSearch/minPurchases/minProducts/minBid/minSPR/minTitleDensity…）；`minImpressions/minClicks/minCvsShareRate` MCP 无参数，需客户端兜底过滤。
- 工具选型：种子词扩展用 `keyword_miner`；类目维度选品（带同比/近3月增长率）用 `keyword_research`。

## 报告可视化（HTML Dashboard）

生成上述 Markdown 分析正文后，**在报告末尾追加一个可直接预览的完整 HTML Dashboard**，让交付更直观美观。完整样式规范（内联 CSS 骨架、组件库、数字格式、配色、模板骨架、检查清单）见 [`html-report-style.md`](../reference/html-report-style.md)。

要点：
- 用 ` ```html ` 代码块包裹整个页面，内联 `<style>` 且在 `:root` 定义颜色变量兜底，确保独立可预览。
- 按本报告内容选用组件：头部结论标签 → KPI 卡片行 → 两列（条形图 + 洞察）→ 图表 → 徽章表格 / 卡片网格 → 策略三列 → 页脚数据溯源。
- 所有数值必须格式化（销量千分位、占比 1 位小数、价格 `$X.XX`），注意 `0~1` 刻度换算，禁止裸 float。
- 图表用 `[[chart]]...[[/chart]]` 嵌入合法 ECharts option JSON（支持 `bar`/`line`/`pie` 与双轴），不引用外部 CDN。
- 数据缺失的维度如实标注「数据缺失」，页脚注明来源接口与数据月份。

---

## 参考文档

本 Skill 涉及的 API 详细参数说明：

- [`aba_research_monthly`](../../reference/aba_research_monthly.md)
- [`aba_research_trend`](../../reference/aba_research_trend.md)
- [`aba_research_weekly`](../../reference/aba_research_weekly.md)
- [`google_trend`](../../reference/google_trend.md)
- [`keyword_miner`](../reference/keyword_miner.md) ✅ 已验证
- [`keyword_research`](../../reference/keyword_research.md)
- [`keyword_research_trends`](../../reference/keyword_research_trends.md)
