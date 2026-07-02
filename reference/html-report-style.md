# HTML Dashboard 报告样式规范

> 所有综合分析 / 战术选品 Skill 在输出 Markdown 分析正文后，可据此追加一个**可直接预览的完整 HTML 报告**，让交付内容更直观美观。
> 本规范抽取并通用化自平台「Amazon 市场分析 Skill」的可视化交付标准。

---

## 一、输出总则

1. **位置**：HTML 页面追加在 Markdown 报告**末尾**，用 ` ```html ` 代码块包裹整个页面，便于前端提取为 HTML Artifact。
2. **自包含**：页面必须内联 `<style>`，并在 `:root` 中定义颜色/圆角变量兜底值（见 §三），确保脱离平台主题也能独立、美观地预览。
3. **数字格式化**：所有展示数值必须经过格式化，**禁止直接输出裸 float**（规则见 §五）。
4. **图表**：用 `[[chart]]...[[/chart]]` 标记嵌入**合法的 ECharts option JSON**（支持 `bar`/`line`/`pie`、双轴），**不引用外部 CDN、不写 ECharts 初始化 JS**（规则见 §六）。
5. **数据溯源**：页脚标注数据来源接口与数据月份；某维度数据缺失时如实标注「数据缺失」，禁止编造。
6. **颜色**：正文文字一律用 CSS 变量，禁止硬编码 `#333`/`black`；图表内部（ECharts）颜色用硬编码 hex。

---

## 二、整体布局结构

面板自上而下推荐区块顺序（按 Skill 实际内容取用，不必全含）：

```
[头部]        报告标题 + 站点 + 数据基准月 + 结论标签（右上角徽章）
[KPI 卡片行]  3~4 个核心指标卡
[两列区]      左：横向条形图（集中度/分布）  右：文字洞察
[图表区]      趋势 / 分布图（[[chart]] 全宽独占一行）
[主体]        徽章表格 或 卡片网格（关键词表 / 候选商品 / 产品形态）
[SWOT]        可选：竞品拆解场景用 2×2 SWOT 矩阵（见 §4.7）
[策略三列]    机会 / 风险 / 行动建议
[页脚]        数据来源 + 接口调用说明
```

---

## 三、内联 CSS 骨架

页面 `<style>` 直接使用以下骨架（`:root` 兜底值可按品牌微调）：

```html
<style>
:root{
  --color-background-primary:#ffffff;
  --color-background-secondary:#f5f6f8;
  --color-text-primary:#1a1d21;
  --color-text-secondary:#6b7280;
  --color-text-tertiary:#9ca3af;
  --color-border-tertiary:#e6e8eb;
  --border-radius-md:6px;
  --border-radius-lg:10px;
  --color-background-info:#e8f1fc;    --color-text-info:#1e6fd0;
  --color-background-success:#e7f6ef; --color-text-success:#1f9d63;
  --color-background-warning:#fdf1e0; --color-text-warning:#c77a15;
  --color-background-danger:#fdeaea;  --color-text-danger:#d64545;
}
*{box-sizing:border-box;margin:0;padding:0}
.wrap{padding:1rem;font-size:14px;color:var(--color-text-primary);
  font-family:-apple-system,BlinkMacSystemFont,"Segoe UI","PingFang SC",sans-serif;
  background:var(--color-background-primary);max-width:960px;margin:0 auto}
.sec-title{font-size:13px;font-weight:500;color:var(--color-text-secondary);
  letter-spacing:.04em;margin:1.5rem 0 .75rem;padding-bottom:6px;
  border-bottom:.5px solid var(--color-border-tertiary)}
.kpi-grid{display:grid;grid-template-columns:repeat(4,1fr);gap:10px;margin-bottom:1rem}
.kpi{background:var(--color-background-secondary);border-radius:var(--border-radius-md);padding:12px 14px}
.kpi-val{font-size:22px;font-weight:500;color:var(--color-text-primary);line-height:1.2}
.kpi-label{font-size:12px;color:var(--color-text-secondary);margin-top:3px}
.kpi-sub{font-size:11px;color:var(--color-text-secondary);margin-top:2px}
.two-col{display:grid;grid-template-columns:1fr 1fr;gap:12px;margin-bottom:1rem}
.card{background:var(--color-background-primary);border:.5px solid var(--color-border-tertiary);
  border-radius:var(--border-radius-lg);padding:1rem 1.25rem;margin-bottom:1rem}
.card-title{font-size:13px;font-weight:500;color:var(--color-text-secondary);margin-bottom:.75rem}
.bar-row{display:flex;align-items:center;gap:8px;margin-bottom:6px}
.bar-label{font-size:12px;color:var(--color-text-secondary);width:140px;flex-shrink:0;
  white-space:nowrap;overflow:hidden;text-overflow:ellipsis}
.bar-track{flex:1;height:8px;background:var(--color-background-secondary);border-radius:4px;overflow:hidden}
.bar-fill{height:100%;border-radius:4px}
.bar-val{font-size:12px;font-weight:500;color:var(--color-text-primary);width:44px;text-align:right;flex-shrink:0}
.tag{display:inline-block;padding:2px 8px;border-radius:var(--border-radius-md);font-size:11px;font-weight:500}
.tag-info{background:var(--color-background-info);color:var(--color-text-info)}
.tag-success{background:var(--color-background-success);color:var(--color-text-success)}
.tag-warn{background:var(--color-background-warning);color:var(--color-text-warning)}
.tag-danger{background:var(--color-background-danger);color:var(--color-text-danger)}
.insight-row{display:flex;gap:8px;margin-bottom:8px;align-items:flex-start}
.insight-icon{font-size:16px;flex-shrink:0;margin-top:1px}
.insight-text{font-size:13px;color:var(--color-text-secondary);line-height:1.5}
.insight-text strong{color:var(--color-text-primary);font-weight:500}
.tbl{width:100%;font-size:12px;border-collapse:collapse}
.tbl th{font-weight:500;color:var(--color-text-secondary);text-align:left;
  padding:6px 8px 6px 0;border-bottom:.5px solid var(--color-border-tertiary)}
.tbl td{padding:6px 8px 6px 0;border-bottom:.5px solid var(--color-border-tertiary);
  color:var(--color-text-primary);vertical-align:middle}
.tbl tr:last-child td{border-bottom:none}
.form-grid{display:grid;grid-template-columns:repeat(3,1fr);gap:10px}
.form-card{background:var(--color-background-secondary);border-radius:var(--border-radius-md);padding:12px}
.strat-grid{display:grid;grid-template-columns:1fr 1fr 1fr;gap:10px;margin-bottom:1rem}
.strat-card{background:var(--color-background-primary);border:.5px solid var(--color-border-tertiary);
  border-radius:var(--border-radius-lg);padding:12px 14px}
.strat-card-title{font-size:13px;font-weight:500;margin-bottom:8px}
.strat-item{font-size:12px;color:var(--color-text-secondary);margin-bottom:4px;display:flex;gap:6px;line-height:1.4}
.strat-item::before{content:"·";color:var(--color-text-tertiary);flex-shrink:0}
.swot-grid{display:grid;grid-template-columns:1fr 1fr;gap:10px}
.swot-card{border-radius:var(--border-radius-md);padding:12px 14px}
.swot-title{font-size:12px;font-weight:600;margin-bottom:6px}
.swot-item{font-size:12px;color:var(--color-text-secondary);line-height:1.6}
.foot{font-size:12px;color:var(--color-text-secondary);padding-top:8px;
  border-top:.5px solid var(--color-border-tertiary)}
@media(max-width:640px){
  .kpi-grid{grid-template-columns:repeat(2,1fr)}
  .two-col,.strat-grid,.form-grid,.swot-grid{grid-template-columns:1fr}
}
</style>
```

> 移动端适配：`@media(max-width:640px)` 已将多列网格降为单列/双列，务必保留。

---

## 四、组件库

### 4.1 头部 + 结论标签
```html
<div style="display:flex;align-items:center;justify-content:space-between;margin-bottom:1rem">
  <div>
    <div style="font-size:15px;font-weight:500">{报告标题}</div>
    <div style="font-size:12px;color:var(--color-text-secondary);margin-top:3px">Amazon {站点} · 数据基准：{YYYY-MM}</div>
  </div>
  <span class="tag tag-warn">{结论标签}</span>
</div>
```
结论标签配色建议：机会/达标 → `tag-success`，中性 → `tag-info`，需注意 → `tag-warn`，高风险/垄断 → `tag-danger`。

### 4.2 KPI 卡片行
```html
<div class="kpi-grid">
  <div class="kpi"><div class="kpi-val">{值}</div><div class="kpi-label">{指标名}</div><div class="kpi-sub">{补充}</div></div>
  <!-- 共 3~4 个 -->
</div>
```

### 4.3 横向条形图（集中度 / 排名占比）
每行：`品牌/项目名(140px) + 轨道(flex) + 数值(44px右对齐)`。宽度按「该项占比 ÷ 第一名占比 × 100%」归一化（第一名 100%）。填充色按排名取：`#378ADD` `#5DCAA5` `#EF9F27` `#E24B4A` `#7F77DD`。
```html
<div class="bar-row">
  <span class="bar-label">{名称}</span>
  <span class="bar-track"><span class="bar-fill" style="width:{归一化%};background:#378ADD"></span></span>
  <span class="bar-val">{占比%}</span>
</div>
```

### 4.4 徽章表格（关键词 / 候选商品）
原生 `<table class="tbl">`，末列用徽章表达「进入建议 / 评级」：

| 场景 | 样式类 |
|------|--------|
| 重点布局 / 主推 | `tag-info` |
| 竞争适中 / 可测试 | `tag-success` |
| 需差异化 / 谨慎 | `tag-warn` |
| 竞争激烈 / 不建议 | `tag-danger` |

### 4.5 卡片网格（产品形态 / 变体）
`.form-grid`（3 列）内放 `.form-card`，每卡含名称 + 2~3 行核心特征 + 1 个徽章。

### 4.6 策略三列
`.strat-grid` 三列：进入机会（标题 `color:var(--color-text-success)`）/ 核心风险（`--color-text-danger`）/ 行动建议（`--color-text-info`），每列 ≤4 条 `.strat-item`。

### 4.7 SWOT 2×2 矩阵（竞品拆解等场景）
`.swot-grid` 2×2 四格，各用四色底 + 同色标题：优势 S（`success`）/ 劣势 W（`warning`）/ 机会 O（`info`）/ 威胁 T（`danger`）。每格 `.swot-item` 内要点用逗号分隔或短句罗列。
```html
<div class="swot-grid">
  <div class="swot-card" style="background:var(--color-background-success)">
    <div class="swot-title" style="color:var(--color-text-success)">优势 S</div>
    <div class="swot-item">{要点，逗号分隔}</div>
  </div>
  <div class="swot-card" style="background:var(--color-background-warning)">
    <div class="swot-title" style="color:var(--color-text-warning)">劣势 W</div>
    <div class="swot-item">{…}</div>
  </div>
  <div class="swot-card" style="background:var(--color-background-info)">
    <div class="swot-title" style="color:var(--color-text-info)">机会 O</div>
    <div class="swot-item">{…}</div>
  </div>
  <div class="swot-card" style="background:var(--color-background-danger)">
    <div class="swot-title" style="color:var(--color-text-danger)">威胁 T</div>
    <div class="swot-item">{…}</div>
  </div>
</div>
```

### 4.8 页脚
```html
<div class="foot">数据来源：卖家精灵 MCP · {接口列表} · 数据月份 {YYYY-MM}</div>
```

---

## 五、数字格式规则

| 类型 | 格式 | 示例 |
|------|------|------|
| 销售额（美元） | `$XX.X万` 或 `$XXX` | `$37.5万` · `$285` |
| 销量（件） | 整数 + 千分位 | `13,990` |
| 占比 | 1 位小数 + % | `21.1%` |
| 均价 | `$XX.XX` | `$27.63` |
| CPC / 竞价 | `$X.XX` | `$1.21` |
| 搜索量 | 万为单位 1 位小数，或千分位整数 | `40.7万` · `406,728` |
| 购买率 | 2 位小数 + % | `2.85%` |

> 注意刻度：接口中 `purchaseRate/naturalRatio/adRatio/*Crn/monopolyClickRate` 等多为 `0~1`，展示需 ×100；`supplyDemandRatio` 为真实比值不换算。

---

## 六、图表规范（[[chart]] ECharts）

- 用 `[[chart]]` 与 `[[/chart]]` 包裹，中间是**合法 ECharts option JSON**（可被 `JSON.parse`）。
- 支持 `bar`（对比/排名）、`line`（趋势）、`pie`（结构占比），及双 `yAxis`（如销量+均价）。
- 图表**独占一行全宽**，放在「两列区」与主体表格之间。
- 颜色用硬编码 hex：`#378ADD` `#5DCAA5` `#EF9F27` `#E24B4A` `#7F77DD`。
- 数值语义清晰：趋势图 x 轴按月（`05月`…），占比图用 pie。

**趋势 + 均价双轴（bar + line）示例：**
```
[[chart]]
{"title":{"text":"月销量与均价趋势（近12个月）"},"tooltip":{"trigger":"axis"},"legend":{"data":["月销量","均价"]},"xAxis":{"type":"category","data":["05月","06月","07月","08月","09月","10月","11月","12月","01月","02月","03月","04月"]},"yAxis":[{"type":"value","name":"月销量"},{"type":"value","name":"均价($)"}],"series":[{"name":"月销量","type":"bar","itemStyle":{"color":"#378ADD"},"data":[12000,12800,13500,14200,15800,17200,19000,16500,14000,13200,13800,13990]},{"name":"均价","type":"line","yAxisIndex":1,"itemStyle":{"color":"#EF9F27"},"smooth":true,"data":[26.5,26.8,27.1,27.3,27.0,26.6,26.2,27.0,27.4,27.6,27.5,27.63]}]}
[[/chart]]
```

**结构占比（pie）示例：**
```
[[chart]]
{"title":{"text":"流量结构占比"},"tooltip":{"trigger":"item"},"series":[{"type":"pie","radius":"60%","data":[{"value":48,"name":"自然搜索"},{"value":32,"name":"SP广告"},{"value":12,"name":"品牌广告"},{"value":8,"name":"推荐流量"}]}]}
[[/chart]]
```

---

## 七、完整模板骨架

```html
<style>/* ← §三 CSS 骨架 */</style>
<div class="wrap">
  <!-- 头部 -->
  <div style="display:flex;align-items:center;justify-content:space-between;margin-bottom:1rem">
    <div>
      <div style="font-size:15px;font-weight:500">{标题}</div>
      <div style="font-size:12px;color:var(--color-text-secondary);margin-top:3px">Amazon {站点} · 数据基准：{YYYY-MM}</div>
    </div>
    <span class="tag tag-warn">{结论标签}</span>
  </div>

  <!-- KPI -->
  <div class="kpi-grid">…3~4 个 .kpi…</div>

  <!-- 两列：条形图 + 洞察 -->
  <div class="two-col">
    <div class="card"><div class="card-title">{集中度/分布}</div>…bar-row…</div>
    <div class="card"><div class="card-title">关键洞察</div>…insight-row…</div>
  </div>

  <!-- 图表（全宽） -->
  <div class="card"><div class="card-title">{趋势/结构}</div>
    <!-- 此处放 [[chart]]…[[/chart]] -->
  </div>

  <!-- 主体表格 -->
  <div class="card"><div class="card-title">{关键词/候选商品}</div>
    <table class="tbl"><thead>…</thead><tbody>…含 .tag 徽章…</tbody></table>
  </div>

  <!-- 策略三列 -->
  <div class="strat-grid">
    <div class="strat-card"><div class="strat-card-title" style="color:var(--color-text-success)">进入机会</div>…</div>
    <div class="strat-card"><div class="strat-card-title" style="color:var(--color-text-danger)">核心风险</div>…</div>
    <div class="strat-card"><div class="strat-card-title" style="color:var(--color-text-info)">行动建议</div>…</div>
  </div>

  <!-- 页脚 -->
  <div class="foot">数据来源：卖家精灵 MCP · {接口列表} · {YYYY-MM}</div>
</div>
```

---

## 八、输出前检查清单

- [ ] 整个 HTML 用 ` ```html ` 代码块包裹，含内联 `<style>` 且 `:root` 变量已定义
- [ ] KPI / 表格 / 图表内所有数值已格式化（无裸 float），刻度换算正确
- [ ] 条形图宽度已归一化（第一名 100%）
- [ ] 表格末列徽章颜色符合 §4.4 语义
- [ ] 图表用 `[[chart]]` + 合法 ECharts JSON，无外部 CDN、无初始化 JS
- [ ] 结论标签颜色与市场判断一致
- [ ] 页脚标注数据来源接口与月份；缺失维度已标「数据缺失」
- [ ] 多列网格保留了 `@media(max-width:640px)` 移动端降级
