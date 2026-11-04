---
title: Pad 状态与 R2R 反馈
---

# CMP Pad 状态与 R2R 反馈

<div class="keyline" markdown>

**CMP 派工不能脱离 Pad 与 R2R。** 同一个 Lot 派到不同 pad age、不同 feedback freshness 的机台，产出风险完全不同。

</div>

## Pad 状态分段

| 状态 | 派工策略 |
|---|---|
| Break-in | 限制 critical layer，优先低风险 Lot |
| Stable | 正常派工，适合关键层 |
| Warning | 降权，限制高风险 Lot |
| EOL | 禁止新派工，触发更换 |

Pad age 不是寿命计数这么简单，应结合 removal rate、thickness deviation 和 conditioner 状态。

## R2R Feedback Freshness

| 字段 | 用途 |
|---|---|
| last_feedback_time | 反馈新鲜度 |
| same_product_count | 同类连续性 |
| pad_state_at_feedback | 反馈是否适配当前 pad |
| metrology_delay | 量测结果是否滞后 |
| parameter_confidence | 当前参数置信度 |

## 派工策略

```text
if feedback_fresh and pad_stable:
    boost_recipe_continuity()
elif feedback_stale or pad_warning:
    penalize_critical_lot()
if metrology_queue_blocked:
    avoid_creating_feedback_gap()
```

## 指标

- pad age distribution；
- warning-zone critical lot count；
- feedback age；
- recipe continuity；
- thickness deviation；
- re-polish rate。

---

相关：[Post-CMP 与污染隔离](02-postCMP与污染隔离.md)
