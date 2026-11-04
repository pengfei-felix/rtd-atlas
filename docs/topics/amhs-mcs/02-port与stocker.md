---
title: Port 与 Stocker 约束
---

# Port 与 Stocker 约束

## Port 是设备可执行性的门槛

设备 ready 但 port 满，本质上不可接。RTD 应把 port capacity 作为派工前校验。

| 状态 | 影响 |
|---|---|
| input port full | 新 Lot 到不了 |
| output port full | 出片堵塞，设备不能释放 |
| port reservation | 预搬送是否可落地 |
| port down | 设备能力需要折减 |

## Stocker 不是无限 buffer

| 约束 | 调度影响 |
|---|---|
| near-tool stocker full | 预搬送无法落地 |
| stocker in/out queue | ETA 拉长 |
| bay 间搬送 | 长尾明显 |
| reticle library capacity | Pod 到了也未必能入库 |

## 派工策略

```text
if output_port_blocked:
    prioritize_unload_or_downstream_move()
if input_port_unavailable:
    do_not_dispatch_far_away_lot()
if near_stocker_full:
    avoid_preposition_to_that_area()
```

## 典型问题

| 问题 | 根因 | 解法 |
|---|---|---|
| 设备等料 | input port / stocker 未校验 | port-aware dispatch |
| 出片堵塞 | output move 优先级低 | unload priority boost |
| 预搬送变拥堵 | near stocker 满 | pre-position capacity cap |
| Q-time 运输损失大 | cross-bay move 未提前 | yellow stage pre-move |

---

相关：[搬送优先级冲突](03-搬送优先级冲突.md)
