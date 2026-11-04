---
title: AMHS / MCS 联动
---

# AMHS / MCS 联动

<div class="keyline" markdown>

**Lot 可派不等于 FOUP 能到，资源可用不等于搬送能跟上。** 12 寸 Fab 里，RTD 如果不看 AMHS/MCS，很多“最优派工”只是把等待从设备队列转移到空中搬送和 stocker。

</div>

## RTD 与 AMHS 的边界

| 系统 | 负责什么 | 不负责什么 |
|---|---|---|
| RTD | 决定哪个 Lot / 资源应该去哪里、何时需要到 | 不控制 OHT 具体路径和交通调度 |
| MCS | 接收搬送请求、分配搬送任务、控制路径 | 不决定生产优先级本身 |
| AMHS | 实际搬送 FOUP / Reticle pod / carrier | 不理解工艺窗口和派工目标 |
| MES | 记录 Lot 状态和工艺执行 | 不优化搬送拥堵 |

工程重点是接口：RTD 要告诉 MCS “为什么急、何时到、迟到有什么后果”；MCS 要告诉 RTD “能否到、预计何时到、路径是否拥堵”。

## 需要进入派工的搬送状态

| 状态 | 派工含义 |
|---|---|
| FOUP current location | 候选 Lot 是否能及时到达设备 |
| FOUP ETA | 派工后是否会造成设备空等 |
| Port availability | 设备端是否有口可接 |
| Stocker capacity | 目的地附近是否能缓存 |
| OHT congestion | 搬送时间是否进入长尾 |
| Reticle pod location | Litho 双资源同步是否可行 |
| Move priority | 生产优先级如何转成搬送优先级 |

## 双资源同步

Litho 等区域经常不是单搬送，而是 Lot 和辅助资源需要同时到位。

```text
Lot FOUP ETA
Reticle Pod ETA
Tool ready time
Port / library availability
  → max(三者到位时间) 才是真正可开工时间
```

如果 RTD 只看 FOUP，不看 Reticle pod，会出现：Lot 先到设备口，Reticle 还在路上；或者 Reticle 先到库位，Lot 被别的高优先级搬送堵住。

## ETA 如何影响派工

| 场景 | 处理 |
|---|---|
| 设备即将空闲，候选 Lot ETA 差异大 | ETA 进入 score，避免设备空等 |
| Q-time 黄区，搬送 ETA 不确定 | 提前触发 pre-position，不等到红区 |
| 下游拥堵，派工会造成 port blockage | 延后或换目的设备 |
| stocker 满载 | 禁止把更多 WIP 推向该区域 |
| OHT 路径拥堵 | 降低远端候选 Lot 分数，优先近端可执行 Lot |

注意：ETA 不是固定搬送时间。应按区域、路径、时段、拥堵状态分布化处理。

## Move Request 时机

| 时机 | 适用 | 风险 |
|---|---|---|
| 派工后立即下 move | 简单、直接 | 可能过早占用 port / stocker |
| 设备预计空闲前 T 分钟 | 平衡等待和拥堵 | T 需要按搬送分布标定 |
| Q-time 黄区预搬送 | 防止红区救火 | 预搬后若计划变更需释放 |
| 双资源全部可达后搬送 | 减少一边等一边 | 可能错过提前准备机会 |

RTD 应支持 resource pre-position，但每个预搬送都要有有效期和释放策略。

## 搬送优先级与生产优先级冲突

| 冲突 | 例子 | 处理 |
|---|---|---|
| Hot Lot move vs 瓶颈供料 | Hot Lot 抢占 OHT，瓶颈设备饿料 | 设置瓶颈供料保护 |
| Q-time 红区 vs 普通搬送 | 红区 Lot 需要插队 | 红区优先，但记录影响面 |
| Reticle pod vs FOUP | 只搬 Lot 不搬罩也开不了工 | 双资源同步优先级绑定 |
| 出站搬送 vs 入站搬送 | port 满导致设备不能出片 | 出站释放能力优先级要提高 |

搬送优先级不能简单等于 Lot 优先级。要结合设备饥饿、port blockage、Q-time、资源同步一起算。

## Stocker / Buffer 约束

| 约束 | 派工影响 |
|---|---|
| stocker near tool 满 | 候选设备实际不可接 |
| port 数不足 | 需要控制提前搬送数量 |
| buffer dwell time | 某些 Lot 不宜长时间停在 buffer |
| cross-bay move | 搬送长尾明显，需提前预约 |
| reticle library capacity | Reticle 到了也未必能入库 |

## 指标体系

| 指标 | 解释 |
|---|---|
| dispatch-to-arrival time | 派工到 Lot 到机台的时间 |
| ETA error | 预计到达与实际到达差 |
| tool wait for material | 设备等 Lot / 资源的时间 |
| port blockage time | 端口堵塞导致设备不能出片或进片 |
| pre-position hit rate | 预搬送最终被使用的比例 |
| wasted move | 预搬后又改派、资源白搬 |
| Q-time transport loss | Q-time 中被搬送消耗的时间 |

## 典型问题

| 问题 | 根因 | 解法 |
|---|---|---|
| 设备空着等料 | 派工只看队列，不看 FOUP ETA | ETA 入 score，近端候选优先 |
| Q-time 到红区才开始搬 | 没有黄色预搬机制 | 黄区触发 pre-position |
| Reticle 和 Lot 到位不同步 | 双资源分开派 | 绑定双 ETA 和资源锁定 |
| 搬送系统被 Hot Lot 打穿 | 搬送优先级只看 Lot priority | 加瓶颈供料和 port blockage 权重 |
| 下游 port 满导致出片堵塞 | 只优化上游 track-in | 派工前检查下游 port / stocker |

## 落地路线

1. 接 FOUP location、ETA、port、stocker 状态。
2. 把 dispatch score 从“设备可用”升级为“设备可用 + Lot 可达”。
3. 在 Q-time 黄区引入 pre-position，不等红区救火。
4. 对 Litho 等区域绑定 Lot 与 Reticle / Pod 双 ETA。
5. 建 wasted move 和 pre-position hit rate，看预搬策略是否过度。

---

相关：[辅助资源流转](support-resource.md) · [Q-time 走廊](qtime-corridor.md) · [系统架构与接口](../method/system-architecture.md)
