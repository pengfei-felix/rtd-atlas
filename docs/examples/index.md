---
title: 样例总览
---

# 样例总览

<div class="keyline" markdown>

**本组页面只放脱敏、虚构、可复用的工程样例。** 它的目的不是模拟真实产线，而是让数据模型、规则命中、派工解释和历史回放有可阅读的参照。

</div>

| 样例 | 用途 |
|---|---|
| [Lot 状态样例](sample-lot-state.md) | 说明 RTD 候选集需要哪些 Lot 字段 |
| [派工决策日志样例](sample-dispatch-decision.md) | 说明一次派工如何解释和复盘 |
| [规则命中日志样例](sample-rule-hit-log.md) | 说明规则库如何记录过滤、升权、降权 |
| [Q-time 窗口样例](sample-qtime-window.md) | 说明单窗口和走廊窗口如何建模 |
| [历史回放案例](sample-replay-case.md) | 说明新旧规则如何做 counterfactual 对比 |

## 样例边界

- 所有 ID、时间、设备、产品均为虚构。
- 字段名可作为系统设计参考，但不是固定接口规范。
- 样例强调可解释性，不追求完整数据库建模。
- 若要用于真实项目，需要按本地 MES/EAP/AMHS 接口重命名和扩展。
