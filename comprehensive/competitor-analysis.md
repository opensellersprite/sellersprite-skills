# 竞品深度拆解

对指定的 Amazon 竞品 ASIN 进行全面深度拆解，分析其销售表现、流量结构、关键词策略、定价策略和竞争优劣势。

## 使用方式

用户输入: `/competitor-analysis [ASIN]`

参数说明:
- 第1个参数: 竞品的 ASIN（必填）
- 可附加参数: 站点(默认US)

## 执行步骤

> **调用优化说明**: 以下工具均作用于同一 ASIN，无数据依赖关系。分为两批并行调用，将原本 9 次串行调用优化为 2 次并行批次。

### 第1步: 并行获取基础画像与历史数据

同时调用以下 4 个工具:

**1. `asin_detail`** - 商品完整基础信息:
- marketplace: 用户指定站点
- asin: 目标ASIN

**2. `asin_prediction`** - 销量预测数据:
- marketplace: 用户指定站点
- asin: 目标ASIN

**3. `keepa_info`** - 商品历史趋势:
- marketplace: 用户指定站点
- asin: 目标ASIN

**4. `asin_coupon_trend`** - 促销策略:
- marketplace: 用户指定站点
- asin: 目标ASIN

### 第2步: 并行获取流量与竞品数据

同时调用以下 5 个工具:

**1. `traffic_keyword_stat`** - 流量概览:
- marketplace: 用户指定站点
- asin: 目标ASIN
- month: 历史月份 yyyyMM（可选，不传默认近30天）

**2. `traffic_keyword`** - 流量关键词明细:
- marketplace: 用户指定站点
- asin: 目标ASIN
- order: 按 trafficPercentage 降序
- size: 100

**3. `traffic_source`** - 流量来源结构:
- marketplace: 用户指定站点
- q: 目标ASIN

**4. `traffic_listing`** - 关联竞品:
- marketplace: 用户指定站点
- asinList: [目标ASIN]
- relations: ["similar"]（array 必填，可选关联类型，如 "similar"）
- size: 10

**5. `review`** - 买家评论:
- marketplace: 用户指定站点
- asin: 目标ASIN
- categoryId: 类目ID（可选，提升类目相关性；从 asin_prediction.asinDetail.categoryId 获取。注意：asin_detail 响应中是 nodeId/nodeIdPath，并无 categoryId 字段）
- size: 10

### 第3步: 生成竞品分析报告

综合所有数据，输出完整的竞品拆解报告:

#### 一、商品概览

| 指标 | 数据 |
|------|------|
| ASIN | xxx |
| 标题 | xxx |
| 品牌 | xxx |
| 价格 | $xx.xx (优惠后 $xx.xx) |
| 评分/评分数 | x.x / xxx |
| BSR排名 | 大类 #xxx / 小类 #x |
| 卖家类型 | FBA/FBM/自营 |
| 变体数 | x个 |
| 上架时间 | xxxx-xx-xx |
| Listing质量分 | xx/100 |
| 标识 | BS/AC/NR |

#### 二、销售表现分析
- 近14个月月销量趋势图（文字描述）
- 月销售额趋势
- 价格调整历史与策略分析
- BSR波动与排名稳定性
- 当前所处生命周期阶段（新品期/成长期/成熟期/衰退期）

#### 三、流量结构分析

| 流量来源 | 词数 | 占比 |
|----------|------|------|
| 自然搜索词 | xx | xx% |
| SP广告词 | xx | xx% |
| 品牌广告词 | xx | xx% |
| 视频广告词 | xx | xx% |
| Amazon推荐(AC/ER/4星) | xx | xx% |
| HR推荐 | xx | xx% |

流量健康度评估:
- 自然流量占比是否健康
- 是否过度依赖广告
- 推荐流量获取能力

#### 四、核心关键词分析
- TOP 10 流量关键词列表（关键词、搜索量、排名、流量占比、PPC竞价）
- 关键词布局策略分析
- 主要流量词的竞争难度

#### 五、促销策略分析
- 优惠类型与力度
- 促销频率与周期
- 价格策略（高价常促 vs 低价稳定）

#### 六、竞争格局
- 直接竞品 TOP 10（ASIN、品牌、价格、销量、评分）
- 市场份额与定位分析
- 竞品差异化对比

#### 七、买家评价洞察
- 评分分布（5星/4星/3星/2星/1星占比）
- 买家好评关键词/卖点
- 买家差评痛点/抱怨
- 产品改进机会

#### 八、SWOT 分析

| 优势 (S) | 劣势 (W) |
|----------|----------|
| xxx | xxx |

| 机会 (O) | 威胁 (T) |
|----------|----------|
| xxx | xxx |

#### 九、竞品应对策略建议
- 产品差异化方向
- 定价策略建议
- 关键词切入建议
- Listing优化方向
- 广告投放建议

## 输出格式

使用 Markdown 表格、评分卡、SWOT矩阵呈现。关键发现用加粗标注。

## 字段映射与口径（已值级验证）

> 本 Skill 是**单 ASIN 深度拆解**（asin_detail + traffic_keyword 等）。若需**按 ASIN/关键词/品牌/卖家批量查商品列表**，用 `competitor_lookup`（网页「查竞品」后端），二者互补。

- **流量关键词** `traffic_keyword`：响应与网页**零改名**；`rankPosition/adPosition` 是对象取 `.position`；`purchaseRate/trafficPercentage/naturalRatio/adRatio` 为 0~1 展示 ×100，`supplyDemandRatio` 真实比值不换算。详见 [`traffic_keyword`](../reference/traffic_keyword.md)。
- **列表查询** `competitor_lookup`：响应改名同 product_research（`totalUnits→units`、`bsrRank→bsr`、`sellerType→fulfillment` 等）；网页「最近30天」与 MCP 自然月口径不同。详见 [`competitor_lookup`](../reference/competitor_lookup.md)。

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

- [`asin_coupon_trend`](../../reference/asin_coupon_trend.md)
- [`asin_detail`](../../reference/asin_detail.md)
- [`asin_prediction`](../../reference/asin_prediction.md)
- [`competitor_lookup`](../reference/competitor_lookup.md) ✅ 已验证（列表查询，补充）
- [`keepa_info`](../../reference/keepa_info.md)
- [`review`](../../reference/review.md)
- [`traffic_keyword`](../reference/traffic_keyword.md) ✅ 已验证
- [`traffic_keyword_stat`](../../reference/traffic_keyword_stat.md)
- [`traffic_listing`](../../reference/traffic_listing.md)
- [`traffic_source`](../../reference/traffic_source.md)
