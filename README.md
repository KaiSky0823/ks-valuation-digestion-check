# ks-valuation-digestion-check · 估值消化体检 📈

> *Read-only "can EPS growth digest this valuation" screen for a basket of tickers.*

「这股票 60 倍 PE，贵不贵？」

这个问题本身就问错了。正确的问法是：**未来两三年的真实 EPS 增长，能不能把这个估值消化到合理区间？** 增长是真的还是并购堆的？现金流跟得上吗？周期顶的低 PE 是不是假象？

这个 skill 把这个问题拆成一张可核查的表。

## 🧮 它怎么算

对每个 ticker 在线取五样东西，**每样都带日期和来源**：

1. 💵 最新价（币种、交易所、时点）
2. 🔭 前瞻 12 个月 P/E（口径注明；负 EPS 就写「无 PE」，不硬算）
3. 📊 未来 2～3 年 EPS 共识，自算 CAGR（只有单年增速时**不冒充** CAGR）
4. ➗ PEG = forward P/E ÷ EPS CAGR
5. 🧾 EPS 质量：有机还是并购、一次性收益、股本变化、GAAP/非 GAAP、FCF 正负

然后给四档裁决：**成立 / 边界 / 不成立（透支或借钱买增长）/ 框架不适用**。

## 🪤 它主动拦三个陷阱

- 🏦 **并购买增长** —— EPS 涨但 FCF 弱，PEG 失真
- ⛰️ **周期顶低 PE** —— 峰值盈利压出来的便宜，不是便宜
- ❌ **没有正 EPS** —— PEG 无数学意义，直接判「框架不适用」，不强算

## 💬 你说什么，它给什么

你说：「体检一下 NVDA MDB MA OKTA FTNT」

它还你一张固定字段的表（price / forward_pe / cagr / peg / eps_quality / growth_driver / verdict / one_line），下面跟三段：**data_gaps**（哪个字段没找到或口径冲突）、**sources**（按 ticker 分组的链接和日期）、**method_notes**（CAGR 年限、PEG 算法）。

多组 ticker 时会派 agent 并行，但 PEG 一律由主线程复算，异常值自己再核一遍。

## ⚠️ 它不是什么

不是买卖建议。它是研究筛查，把「事实 / 计算 / 判断」分开写清楚，扣扳机的永远是你。

## ⚙️ 安装

```bash
# Claude Code
git clone https://github.com/KaiSky0823/ks-valuation-digestion-check.git ~/.claude/skills/ks-valuation-digestion-check
# Codex
git clone https://github.com/KaiSky0823/ks-valuation-digestion-check.git ~/.agents/skills/ks-valuation-digestion-check
```

## License

MIT © 2026 KaiSky0823
