# 流量结构分析

对指定 Amazon ASIN 的流量来源进行全面拆解，分析自然流量、广告流量、关联流量的占比和关键词表现，帮助卖家优化流量获取策略。

## 使用方式

用户输入: `/traffic-analysis [ASIN]`

参数说明:
- 第1个参数: 目标 ASIN（必填）
- 可附加参数: 站点(默认US)、对比ASIN（可选）

## 执行步骤

### 第1步: 流量结构概览 + 来源分布（并行调用）

同时调用以下两个工具:

**1a. `traffic_keyword_stat`** 获取流量关键词统计:

参数:
- marketplace: 用户指定站点
- asin: 目标ASIN
- month: 历史月份 yyyyMM（可选，不传默认近30天）

获取: 自然搜索词数、各类广告词数、推荐词数，判断整体流量健康度。

**1b. `traffic_listing_stat`** 获取免费/付费流量结构:

参数:
- marketplace: 用户指定站点
- asin: 目标ASIN

获取: 免费流量 vs 付费流量占比、各关联类型的数量分布。

### 第2步: 流量关键词明细 + 来源详情（并行调用）

同时调用以下两个工具:

**2a. `traffic_keyword`** 获取详细流量词:

参数:
- marketplace: 用户指定站点
- asin: 目标ASIN
- order: 按 trafficPercentage 降序
- size: 100

获取: TOP 50 流量关键词，包含搜索量、自然排名、广告排名、流量占比、PPC竞价等。

**2b. `traffic_source`** 获取 ASIN 流量来源:

参数:
- marketplace: 用户指定站点
- q: 目标ASIN

获取: 不同来源的流量词分布、Amazon推荐体系占比。

### 第3步: 关联流量 + 关键词拓展 + 转化词（并行调用）

同时调用以下三个工具:

**3a. `traffic_listing`** 获取关联商品:

参数:
- marketplace: 用户指定站点
- asinList: [目标ASIN]
- relations: ["similar"]（array 必填，可选关联类型，如 "similar"）
- size: 20

获取: 与目标ASIN存在关联关系的竞品列表及其流量数据。

**3b. `traffic_extend`** 发现更多流量词:

参数:
- marketplace: 用户指定站点
- asinList: [目标ASIN]
- queryType: 2
- minSearches: 500
- size: 30

> 注意: `order` 参数为 object 类型（如 `{"field": "searches", "sort": "desc"}`），如需排序请传入 object，不要传字符串。

获取: 尚未覆盖的高搜索量关键词机会。

**3c. `keyword_order`** 获取关键词转化表现:

参数:
- marketplace: 用户指定站点
- asins: [目标ASIN]
- reverseType: "M"
- date: 当月，格式 yyyyMM（必填，如 "202605"）。reverseType="M" 时用 yyyyMM 格式；reverseType="W" 时用 yyyyMMdd 格式（当周周六）

获取: 转化优质词、平稳词、流失词和无效曝光词。

### 第4步: 生成流量分析报告

#### 一、流量结构概览

| 流量类型 | 关键词数量 | 占比 | 健康度 |
|----------|-----------|------|--------|
| 自然搜索 | xx | xx% | 健康/一般/不足 |
| SP广告 | xx | xx% | - |
| 品牌广告 | xx | xx% | - |
| 视频广告 | xx | xx% | - |
| AC推荐 | xx | xx% | - |
| ER推荐 | xx | xx% | - |
| 4星推荐 | xx | xx% | - |
| HR推荐 | xx | xx% | - |
| **合计** | **xx** | **100%** | - |

**免费 vs 付费占比**: 免费 xx% / 付费 xx%
**流量健康度评估**: 健康/一般/风险（广告依赖度）

#### 二、核心流量关键词 (TOP 20)

| 排名 | 关键词 | 月搜索量 | 自然排名 | 广告排名 | 流量占比 | PPC竞价 |
|------|--------|---------|---------|---------|---------|---------|
| 1    | word1  | xxx     | #x      | #x      | xx%     | $x.xx   |
| 2    | word2  | xxx     | #x      | -       | xx%     | $x.xx   |
| ...  | ...    | ...     | ...     | ...     | ...     | ...     |

#### 三、关键词转化分析

| 转化类型 | 词数 | 占比 | 说明 |
|----------|------|------|------|
| 转化优质词 | xx | xx% | 高转化，应重点维护 |
| 转化平稳词 | xx | xx% | 稳定贡献，持续优化 |
| 转化流失词 | xx | xx% | 有曝光无转化，需优化 |
| 无效曝光词 | xx | xx% | 低质流量，考虑否词 |

**关键流失词** (有流量但转化差的关键词):
| 关键词 | 搜索量 | 转化状态 | 建议 |
|--------|--------|---------|------|

#### 四、未覆盖流量机会

**高搜索量未覆盖词** (竞品有流量但本ASIN未覆盖):
| 关键词 | 月搜索量 | 购买率 | PPC竞价 | 切入难度 |
|--------|---------|--------|---------|---------|
| word1  | xxx     | xx%    | $x.xx   | 低/中/高 |

#### 五、关联流量分析
- 关联竞品 TOP 10 列表
- 关联类型分布
- 关联流量获取建议

#### 六、流量优化策略

**自然流量提升**:
- SEO优化关键词列表
- 自然排名提升优先级

**广告投放建议**:
- 新增投放关键词（从未覆盖词中筛选）
- 否词建议（无效曝光词）
- 竞价调整建议

**推荐流量获取**:
- AC/ER/4星推荐获取条件
- 提升推荐流量的优化方向

#### 七、与竞品对比（如提供）
- 流量词数量对比
- 自然/广告流量结构对比
- 重叠关键词与独有关键词

## 输出格式

使用 Markdown 表格和结构化列表呈现。流量健康度用颜色文字标注（健康/警告/危险）。关键优化建议用加粗标注。

## 字段映射与口径（已值级验证）

> 流量关键词明细用 `traffic_keyword`（网页「关键词反查」后端，`POST /v3/api/relation/reversing`）。已核对：交集词 × 23 字段逐位相等（零改名）。详见 [`traffic_keyword`](../reference/traffic_keyword.md)。

- **零改名**：响应字段与网页同名（仅 `keywords→keyword`）。
- `rankPosition`（自然排名）/ `adPosition`（广告排名）是对象 `{page,index,position}`，展示取 `.position`。
- **刻度**：`purchaseRate/trafficPercentage/naturalRatio/adRatio/monopolyClickRate/top3*` 为 0~1，展示 ×100；`supplyDemandRatio` 真实比值不换算。
- **单工具不覆盖全部筛选**：`traffic_keyword` 全字段但无区间筛选；`traffic_extend` 有区间筛选但丢弃 rankPosition/adPosition/naturalRatio/adRatio/impressions/clicks。保真取数用前者 + 客户端区间过滤。
- 转化分析用 `keyword_order`（转化优质/平稳/流失词），它是转化质量子集，**不等于**反查全集。

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

- [`keyword_order`](../../reference/keyword_order.md)
- [`traffic_extend`](../../reference/traffic_extend.md)
- [`traffic_keyword`](../reference/traffic_keyword.md) ✅ 已验证
- [`traffic_keyword_stat`](../../reference/traffic_keyword_stat.md)
- [`traffic_listing`](../../reference/traffic_listing.md)
- [`traffic_listing_stat`](../../reference/traffic_listing_stat.md)
- [`traffic_source`](../../reference/traffic_source.md)
