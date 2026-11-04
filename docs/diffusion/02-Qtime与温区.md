---
title: Q-time 与温区 Campaign
---

# Diffusion Q-time 与温区 Campaign

<div class="keyline" markdown>

**Diffusion 最容易犯的错，是把“温区连续”做到漂亮，却把 Q-time 推进红区。** 温区 campaign 是产能工具，不是绝对目标；一旦窗口风险超过阈值，campaign 必须能被打断。

</div>

## 两类 Q-time

| 窗口 | 典型风险 | 控制策略 |
|---|---|---|
| 清洗前 → 进炉 | 清洗后等待过长，表面状态变差 | 清洗前组炉预测，清洗后限时开炉 |
| 出炉 → 下游 | 出炉后 clean / metrology / 下一工序堵塞 | 开炉前预约下游，错峰出炉 |

关键区别：**清洗前可以等，清洗后少等；出炉前可预测，出炉后难补救。**

## 温区 Campaign

温区 campaign 的收益来自减少升降温与气氛切换损失。

| Campaign 类型 | 适用 | 打断条件 |
|---|---|---|
| 同温区连续 | 同 family 到达稳定 | Q-time yellow 且等待会入红 |
| 同气氛连续 | 气体切换代价大 | Hot Lot / 瓶颈饥饿 |
| 同产品族连续 | 参数稳定，减少确认 | 下游不可接或 Boat 不足 |
| PM 前收尾 | PM 前再跑一炉 | 寿命不足或出炉下游堵塞 |

## 黄色预警比红区更重要

红区才救火，通常只剩半炉开或插队两种坏选择。黄色阶段可以：

1. 提前锁定炉管；
2. 提前锁定 Boat；
3. 调整清洗节奏；
4. 预约下游；
5. 打断 campaign 但减少整体损失。

## 规则骨架

```text
if lot.qtime_state == RED:
    hard_prioritize_if_feasible()
elif lot.qtime_state == YELLOW:
    reserve_tube_and_boat()
    check_campaign_break_condition()
else:
    optimize_load_factor_and_temperature_sequence()
```

## 典型冲突

| 冲突 | 判断 |
|---|---|
| 满炉 vs Q-time | 窗口内等待超过阈值时，牺牲装载率 |
| 温区连续 vs Hot Lot | Hot Lot 需要影响面评估，不是无条件插入 |
| 出炉波峰 vs 当前开炉 | 下游不可接时，当前开炉可能只是转移拥堵 |
| Boat 寿命 vs 炉管空闲 | Boat 不合格时，炉管空闲也不能开 |

## 验证口径

不要只看“Q-time violation 降低”。还要同时看：

- 半炉比例是否上升；
- 温区切换是否暴涨；
- 下游等待是否下降；
- 普通 Lot CT 是否被 Hot Lot 拖长；
- 炉管有效吞吐是否下降。

---

相关：[组炉与 SRB 规则](01-组炉与SRB.md) · [指标冲突链](../topics/metric-conflict.md)
