---
title: Setup Family 与换线矩阵
---

# Implant Setup Family 与换线矩阵

<div class="keyline" markdown>

**Implant 的产能损失主要藏在换线里。** species、energy、dose、tilt、contamination 任一维度变化，都会改变 beam tune 和确认成本；所以 Implant 派工本质是序列相关 setup 问题。

</div>

## Setup Family 怎么分

| 维度 | 例子 | 调度含义 |
|---|---|---|
| Species / Dopant | B / P / As | 影响 source、污染和束流稳定 |
| Energy band | low / medium / high | 影响 beamline 调整 |
| Dose band | low / high dose | 影响确认和稳定时间 |
| Angle / Tilt | tilt group | 影响机械与 recipe 稳定 |
| Product sensitivity | critical / normal | 决定能否接受切换后首批 |

Family 不等于 recipe。Family 是为调度服务的聚类：**同 family 连跑代价低，跨 family 切换代价高。**

## From-To 换线矩阵

| from / to | B-low | B-high | P-low | As-high |
|---|---:|---:|---:|---:|
| B-low | 0 | 12 min | 35 min | clean + tune |
| B-high | 8 min | 0 | 40 min | clean + tune |
| P-low | 30 min | 35 min | 0 | 25 min |
| As-high | forbidden | clean + tune | 20 min | 0 |

矩阵要表达非对称性。`A → B` 和 `B → A` 不一定一样。

## Campaign 边界

| 边界 | 建议 |
|---|---|
| 最大连续时长 | 防止普通批饥饿 |
| 最大等待 | 防止为了少换线无限等 |
| Q-time 打断 | yellow 可评估，red 硬打断 |
| Hot Lot 打断 | 需计算影响面，不无条件插入 |
| 确认片需求 | 若打断后需重新确认，要计入代价 |

## 指标

- setup share；
- beam tune time；
- same-family run length；
- campaign interruption；
- confirmation wafer count；
- high-priority wait by family。

---

相关：[污染隔离与高能力机](02-污染隔离与高能力机.md) · [求解器与算法选型](../method/solver-playbook.md)
