---
title: 组炉与 SRB 规则
---

# Diffusion 组炉与 SRB 规则

<div class="keyline" markdown>

**炉管调度的第一问不是“哪批先跑”，而是“现在开炉还是等下一批”。** SRB（sequential rule-based batching）的价值，就是把“等满炉”和“救窗口”放在同一张判断表里。

</div>

## 组炉对象

Diffusion 的决策对象是：

```text
Batch = Lot set + Recipe family + Furnace / Tube + Boat + Time window
```

| 维度 | 必须建模 | 常见错误 |
|---|---|---|
| Lot set | wafer 数、Lot 数、priority、Q-time | 只按 Lot 数凑批 |
| Recipe family | 可同炉、可连续跑、禁止同炉 | 只看 recipe code |
| Tube | qualification、温区、PM / clean 状态 | 炉管可用就认为能开 |
| Boat / Quartzware | 容量、寿命、清洁、污染族 | 辅助资源开炉前才找 |
| Time window | 清洗前、清洗后、出炉后 | 只看当前队列，不看窗口 |

## SRB 判断链

```text
当炉管即将可用：
  1. 当前队列是否存在红区 / 黄区 Lot？
  2. 同 recipe family 在未来 Δt 内是否将到？
  3. 等待 Δt 是否会让任何 Lot 进入红区？
  4. 当前 batch 装载率是否低于最小经济装载？
  5. 下游在预计出炉时间是否可接？
  6. Boat / Quartzware 是否可锁定？
  7. 开炉、等待、换炉管、拆批四者选损失最小者。
```

SRB 的关键不是规则多，而是每条规则都有**触发条件、等待上限、例外条件和验证指标**。

## 等待时间 Δt 标定

| 输入 | 用法 |
|---|---|
| 历史到达间隔 | 估算未来同族 Lot 到达概率 |
| 炉管加工时间 | 长炉程更能容忍开炉前多等一小段 |
| Q-time 剩余 | 决定等待上限 |
| 最小经济装载 | 判断半炉开是否可接受 |
| 下游能力 | 避免同时出炉造成波峰 |

Δt 不建议固定全区一值。更稳的做法是按 recipe family 和 Q-time 风险分段。

## 批次优先级

| 因子 | 说明 |
|---|---|
| 紧急件占比 | 红区 / 黄区 Lot 比例越高，越倾向立即开炉 |
| 装载率 | wafer 数接近容量，开炉损失小 |
| 等待收益 | 等待后装载率提升是否明显 |
| 温区连续性 | 与上一炉 / 下一炉温区差异 |
| 下游可接续 | 出炉后是否有 clean / metrology 能力 |

一个可解释的批次 score 应写成：

```text
batch_score = qtime_risk + load_gain + downstream_readiness - switch_loss - wait_risk
```

## 工程验收

| 指标 | 用途 |
|---|---|
| average load factor | 看装载率，但不能单独做主指标 |
| half-batch ratio | 看救火开炉是否过多 |
| post-clean wait | 看是否在危险窗口内硬等 |
| temperature switch count | 看温区切换损失 |
| downstream burst | 看出炉波峰是否打穿下游 |

---

相关：[Q-time 与温区](02-Qtime与温区.md) · [Boat 与出炉波峰](03-Boat与出炉波峰.md)
