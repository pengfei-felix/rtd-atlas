---
title: ETA 与 Pre-position
---

# AMHS/MCS ETA 与 Pre-position

<div class="keyline" markdown>

**ETA 是 RTD 可执行性的核心输入。** 设备空闲时间、FOUP 到达时间、Reticle pod 到达时间三者取最大值，才是真正的可开工时间。

</div>

## ETA 建模

| 字段 | 说明 |
|---|---|
| from_location | 起点 |
| to_location | 终点 |
| estimated_arrival | 预计到达 |
| confidence | ETA 置信度 |
| congestion_state | 当前路径拥堵 |
| port_wait | 目的 port 等待 |
| stocker_wait | stocker 进出等待 |

## Pre-position 触发

| 触发 | 动作 |
|---|---|
| Q-time yellow | 预搬到目标 tool 附近 stocker |
| 瓶颈即将空闲 | 提前搬候选 Lot |
| Reticle pod ETA 长 | 提前搬辅助资源 |
| cross-bay move | 提前更长时间下发 move |

## 预搬送风险

| 风险 | 控制 |
|---|---|
| wasted move | valid_until + 释放策略 |
| port 占用 | 预搬到 stocker，不盲目占 port |
| 计划变更 | move 可取消 / 可重定向 |
| 搬送优先级挤占 | 瓶颈供料保护 |

## 指标

- ETA error；
- pre-position hit rate；
- wasted move；
- tool wait for material；
- Q-time transport loss。

---

相关：[Port 与 Stocker 约束](02-port与stocker.md)
