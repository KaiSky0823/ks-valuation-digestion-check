---
name: ks-valuation-digestion-check
description: 'Run a read-only “EPS growth absorbs valuation” health check for a basket of public-market tickers. Use when the user asks for 组合估值消化体检, PEG screening, whether growth can digest a high valuation, or a batch verdict of 成立/边界/不成立/框架不适用. Gather current dated price, forward P/E, 2–3 year consensus EPS growth, PEG, EPS/FCF quality, and growth drivers; flag acquisition-funded growth, negative FCF, pre-profit names, cyclicals at peak earnings, baskets, and non-EPS assets. Ask for the ticker basket when none is supplied. Read-only research—not personalized investment advice or an order to trade.'
metadata:
  version: "1.0.0"
  source: "由组合估值消化体检工作流整理的可移植技能"
---

# 组合估值消化体检

把“高估值能否被未来真实 EPS 增长消化”拆成可核查的批量研究表。本技能包含完整研究流程，不依赖外部私有 workflow 文件。

## 输入与边界

- 接受 ticker 数组、逗号/空格分隔的标的，或用户给出的持仓/观察名单。
- 用户未提供标的时，先询问研究名单；不携带作者的个人观察名单。
- 规范化为大写并去重；歧义 ticker 先确认交易所或公司。
- 这是研究与风险筛查，不替用户作个性化买卖决定。不得伪造实时数据、共识或来源。

## 数据闸门

以系统当前日期为准。当前价格、共识估值和预测会变，必须在线核验：

1. 最新价：记录价格、币种、交易所、价格时点或最近交易日。
2. 前瞻 12 个月 P/E：注明口径与数据日期；负 EPS 或缺失写明“负 EPS/无 PE”。
3. 未来 2–3 年 EPS 共识：优先取得逐年预测，自算 CAGR；只有单年增速时不得冒充 CAGR。
4. PEG：forward_pe ÷ fwd_eps_cagr_percent。例如 P/E 30、增速 25% 时 PEG=1.20。注明自算口径。
5. EPS 质量：有机增长、并购堆量、一次性收益、股本变化、GAAP/非 GAAP 差异、FCF 正负及现金转化。
6. 增长驱动：区分真实订单、客户、产能与可验证经营指标，和纯主题、故事或周期顶。

优先用公司 IR/财报/监管披露核验历史与现金流，用可靠市场数据源核验价格和共识；关键数至少给出可追溯链接与日期。来源冲突时并列口径，不自行挑选更“好看”的数字。

## 协作方式

每组最多 5 个 ticker。只有多组时才并行派发；使用可用的协作 agent，每个 agent 负责完整的一组，主 agent 负责统一口径、去重来源、复算 PEG 和最终裁决。不要让多个 agent 重复研究同一 ticker，也不要因并行而突破当前可用并发槽。

给每组相同的字段和判定规则。子任务必须要求返回结构化结果与 data_gaps、sources；子 agent 的结论只是证据输入，主 agent 必须复核算术和异常值。

## 四档裁决

- 成立：真实、有机且现金流支持的 EPS 增长，2–3 年内有望把估值消化到大致合理区间；PEG 约不高于 1 只是线索，不是单独充分条件。
- 边界：PEG、预测可信度、现金流质量或驱动证据存在一项明显不确定，但尚不足以否定。
- 不成立（透支或借钱买增长）：估值主要依赖故事，或 EPS 增长来自并购、杠杆、一次性项目且 FCF 不支持。
- 框架不适用：无正 EPS/pre-profit、周期股处于峰值盈利导致低 P/E 假象、ETF/一篮子、无 EPS 资产，或数据不足到无法形成可比判断。

主动拦截三类陷阱：

- 并购/借钱买增长：EPS 涨但 FCF 弱或为负，PEG 可能失真。
- 周期顶低 P/E：峰值盈利压低 P/E，不能解释为便宜。
- 无正 EPS：PEG 无数学意义，不得强算。

## 输出

先给一张统一表，字段固定为：

| ticker | price_now（日期/币种） | forward_pe（口径） | fwd_eps_cagr | peg（自算口径） | eps_quality | growth_driver | frank_verdict | one_line |
|---|---|---|---|---|---|---|---|---|

随后列：

- data_gaps：逐标的写缺失字段、冲突口径或无法验证之处。
- sources：按 ticker 分组，给出直接链接和数据/发布日期。
- method_notes：说明 CAGR 年限、PEG 算法、共识口径和哪些标的是框架不适用。

结论必须区分事实、计算与判断。找不到就留空并解释，绝不编数。
