---
name: a-share-event-driven
description: A股事件驱动短线研究（催化寻龙 + 首板挖掘）。用于连板概率顺位、次日首板发现与挖掘、有连板预期的重组并购、模型修正。触发词包括 催化寻龙、连板、首板、涨停、D/E研究、重组并购、续板概率、寻龙、首板挖掘、超短首板、明天买哪只首板。
type: workflow
lifecycle: active
---

# A股事件驱动短线研究（催化寻龙 + 首板挖掘）

合并技能：包含原 **催化寻龙** 完整方法论与 **首板挖掘** 专用流程。

Apply the 催化寻龙五步法、D/E涨停研究，以及首板挖掘打分与筛选流程。

Load detailed rules from `references/core-methodology.md` when executing any step. Never invent probabilities or skip verification gates.

## Core Capabilities

1. **Consecutive board (连板) ranking** — Rank D-pool by seal structure, theme strength + decay, dragon-tiger seat quality, auction strength, and weak-seal demotion. Output rank order only and label "顺位≠概率".
2. **Next-day first-board (首板) discovery & mining** — Build E-pool and apply the dedicated 首板挖掘 scoring + workflow (see below and references). Rank by overnight/day catalyst cluster strength + theme age. Anchor each candidate to observable next-day conditions.
3. **Restructuring / M&A with board expectation** — Inside C-layer. Surface candidates that pass dual threshold and show high theme recognizability.
4. **Model correction** — Structured post-mortem + walk-forward residual analysis after results.

## Iron Rules (always enforce)

- Probability not audited → do not emit numeric p; rank ≠ probability.
- Dual threshold default strict — 流通市值 <100亿元 AND 股价 <20元. Relaxed mode only when user explicitly requests.
- Data accuracy first — missing evidence write "待核"; never fill 0 or invent.
- Feature freeze — only T-day close and earlier; label = next trading day native limit-up bool.
- Regime first — every D/E run must open with explicit 3-level market emotion state before any ranking.

## Mandatory Pre-Ranking Layer: Regime Detection

Before building any D or E rank, output a one-line regime declaration:

- **冰点** — 当日涨停家数 ≤ 45 或 近5日日均涨停 ≤ 55 或 1进2晋级率 < 12% 或 炸板率 > 40%
- **正常** — 中间状态（涨停约 55–90，1进2 约 15–25%）
- **过热** — 当日涨停 ≥ 110 或 1进2晋级率 > 30% 且 最高板 ≥ 7

Adjustments:
- 冰点 → 高连板与弱封单整体大幅降权
- 过热 → 首板质量需额外审慎
- 正常 → 按标准排序

## 首板挖掘专用流程（集成）

当用户要求筛选首板、挖掘首板、或询问明天早盘买哪只时，优先使用以下专用流程（与催化寻龙E池互补）：

### 核心原则
- 只做当前最强主线（不碰杂毛题材）
- 优先低位/中低位
- 必须有硬催化或板块强共振
- 竞价和开盘前30分钟决定一切
- 弱转强优先于直接高开秒板

### 打分表（满分100分）

| 项目 | 标准 | 得分 | 备注 |
|------|------|------|------|
| 题材地位 | 属于当日最强主线，且前排有表现 | 0-30 | 主线确认给满分，边缘题材减分 |
| 位置形态 | 低位/中低位，有蓄势或昨日烂板 | 0-25 | 高位直接0分 |
| 催化强度 | 硬催化（政策/订单/公告） | 0-20 | 纯概念最多10分 |
| 竞价强度 | 高开/平开、竞价额放大、弱转强 | 0-15 | 低开超2%直接0分 |
| 资金弹性 | 流通市值20-150亿，量能放大 | 0-10 | 过小或过大减分 |
| **总分** | — | 100 | ≥75分优先，≥85分重点跟踪 |

**准确性约束**：打分时必须区分“已核实”与“预判”。未核实的催化或竞价预期不得给满分。

### 筛选流程
1. **盘前（9:00前）**：确定主线 → 筛选低位候选5-8只 → 打分保留≥75分 → 标注已核实/待核。
2. **竞价阶段（9:15-9:25）**：观察竞价强度 → 剔除弱势 → 决策门只保留通过确认的。
3. **开盘后（9:30-9:40）**：优先快速上攻、分时强承接的；必须同时满足竞价通过 + 开盘走稳走强。
4. **封板确认**：只做能快速封死或弱转强回封的；反复炸板直接放弃。

### 弱转强优先标准 & 避雷清单 & 操作纪律
详见原首板挖掘规则（已整合进上述流程）。强制验证昨日烂板证据，证据不足直接降权。

## Ranking Enhancements（相对强度评分 · 非概率）

用于D/E顺位（冰点日校准）：

| 维度 | 权重 | 高分条件 | 低分/降权条件 |
|------|------|----------|---------------|
| 催化质量 | 33% | 硬事件信息新鲞 | 纯情绪、老题材 |
| 竞价强度 | 28% | 大幅高开+量能确认 | 平开/低开/被砸 |
| 位置与高度 | 20% | 低位或3板以下 | 5板+明显降权 |
| 主题年龄 | 12% | 年龄短、独立新方向 | 连续3日+高位板密集 |
| 资金与结构 | 12% | 龙虎榜净买、硬封 | 榜净卖、弱封 |

- 竞价弱势可一票否决
- 最终顺位必须标注「顺位≠概率」
- Accuracy Verification Gate 强制对前5候选列出已核实/待核。

## Dual Threshold Modes
- 默认严格：流通市值 <100亿 AND 股价 <20元。
- 放宽模式仅当用户明确请求。

## Delivery Format
Full run 固定五部分消息，标题 "催化寻龙|日期|N/5"。D/E section must open with regime line。

## Data Source Discipline & Model Correction
Prefer measured sources (Tencent, Eastmoney, CLS, Wencai). Mark "待核". After results, run structured post-mortem and walk-forward loop. Probability remains blocked until independent audit.

详细完整方法论见 `references/core-methodology.md`。
