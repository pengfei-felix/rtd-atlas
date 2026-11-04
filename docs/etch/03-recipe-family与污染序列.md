---
title: Recipe Family 与污染序列
---

# Etch Recipe Family 与污染序列

<div class="keyline" markdown>

**Etch 的 setup 是序列相关的。** A 后跑 B 和 B 后跑 A，代价可能完全不同；某些切换还必须 clean。只写“同 recipe 优先”远远不够。

</div>

## From-To 切换矩阵

| from / to | Family A | Family B | Family C |
|---|---:|---:|---:|
| Family A | 0 | 8 min | clean required |
| Family B | 15 min | 0 | 20 min |
| Family C | forbidden | 12 min | 0 |

这个矩阵应由工艺、设备和历史数据共同维护。没有 from-to 矩阵，setup loss 会被系统性低估。

## 污染规则类型

| 类型 | 处理 |
|---|---|
| 禁止切换 | 硬过滤 |
| 单向切换 | sequence rule |
| clean 后允许 | 加 clean 工时和状态变化 |
| 工程审批 | 需要 override reason 和审批记录 |
| 限制关键层 | 降权或过滤 critical lot |

## Campaign 边界

同 family 连跑有收益，但必须有边界。

| 边界 | 说明 |
|---|---|
| max_run_time | 最长连续运行时间 |
| max_lot_count | 最大连续 Lot 数 |
| qtime_break | 黄区 / 红区 Lot 打断 |
| hot_lot_break | Hot Lot 插入门槛 |
| downstream_break | 下游不可接时停止继续放大波峰 |

## 派工 score

```text
score = qtime_priority
      + matching_score
      + downstream_score
      - sequence_setup_cost
      - contamination_risk
      - AMHS_eta_penalty
```

硬污染规则不应进入 score，而应提前过滤。

## 验证指标

| 指标 | 含义 |
|---|---|
| setup loss by family | 各 family 切换损失 |
| clean-triggered switch | 因切换触发 clean 次数 |
| campaign interruption | campaign 被 Q-time / Hot Lot 打断次数 |
| contamination block count | 污染规则过滤次数 |
| manual contamination override | 人工污染规则改派次数 |

---

相关：[Chamber 级建模](01-chamber级建模.md) · [求解器与算法选型](../method/solver-playbook.md)
