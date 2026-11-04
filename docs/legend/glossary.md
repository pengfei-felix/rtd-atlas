---
title: 术语表
---

# 术语表

按类分组，不按字母排——中文检索按拼音没有意义，按语义分组更好找。

!!! note "关于多义项"
    行业里有些缩写有多个义项。本表只收**本图志采用的义项**，其他用法不展开。

## 系统与架构

| 术语 | 全称 | 说明 |
|---|---|---|
| **CIM** | Computer Integrated Manufacturing | 计算机集成制造，Fab 信息系统的总称 |
| **MES** | Manufacturing Execution System | 制造执行系统，管工艺流、在制、批次 |
| **EAP** | Equipment Automation Program | 设备自动化程序，机台与 MES 之间的桥 |
| **RTD** | Real-Time Dispatching | 实时派工。秒级到分钟级决定"哪个 Lot 上哪台机" |
| **RTS** | Real-Time Scheduling | 实时调度。分钟级到小时级的计划层 |
| **APS** | Advanced Planning & Scheduling | 高级计划排程，通常指天级到周级的产能规划 |
| **AMHS** | Automated Material Handling System | 自动物料搬运系统 |
| **OHT** | Overhead Hoist Transport | 天车，AMHS 的主流形态 |
| **Stocker** | — | 自动仓储，存放 FOUP / Pod |
| **BRS** | Bare Reticle Storage | 裸罩库，存放未装 Pod 的光罩 |

## 派工概念

| 术语 | 说明 |
|---|---|
| **Dispatching** | 派工。给"已到达、可加工"的 Lot 决定下一台机 |
| **Scheduling** | 调度。决定未来一段时间内所有 Lot 的加工顺序与设备占用 |
| **Rule-based** | 规则驱动。用 if-then 优先级规则决定派工，可解释但会膨胀 |
| **White Space** | 白空间。机台本可加工却没有可加工对象的空转时间 |
| **Hot Lot** | 加急批。有硬性交付时间要求的 Lot，通常有插队权 |
| **Dedication** | 机台专属性。某些层必须在固定机台加工，以控制一致性 |
| **Changeover / Setup** | 换线整定。从一种产品/配方切到另一种所需的时间 |
| **Sequence-dependent Setup** | 序列相关整定。换线时间取决于前后两种配方的组合，不是常数 |
| **Batching** | 组批。把多个 Lot 合并成一批同时加工（炉管、注入常用） |
| **Re-entrant Flow** | 重入流。同一片晶圆反复返回同一工序区（光刻 18~40 次） |

## 约束与时间

| 术语 | 说明 |
|---|---|
| **Q-time** | 两道工序之间允许的最大等待时间，超时可能需返工或报废 |
| **Timelink** | 与 Q-time 类似的工序间时间约束，部分厂用作同义词 |
| **Hold Time** | 允许的最长滞留时间，常用于在制品管理 |
| **Bottleneck** | 瓶颈。实际负荷最高、决定全厂产出的工序区 |
| **Bottleneck Rate** | 瓶颈率。负荷与有效产能之比，大于 1 即为瓶颈候选 |
| **Effective Capacity** | 有效产能。扣除 PM、宕机、降速后的实际可用产能 |

## 光刻专用

| 术语 | 说明 |
|---|---|
| **Reticle** | 光罩。因开发成本高通常唯一，不可复制 |
| **Pod** | 光罩载具。Reticle 成组装入，Pod 上机后其内光罩被独占 |
| **Layer Type** | 层类型。只有同类型 Reticle 才能装入同一 Pod |
| **Tool Library** | 机台内置光罩库，典型容量约 10 张 |
| **Overlay** | 套刻精度。多层图形对准误差，是机台专属性的主要动因 |
| **温层值** | 表征光罩温度需求的自然数，用于换罩后的升降温排序 |
| **Reticle Move** | 光罩搬运次数，是独立的成本指标，与吞吐可能互相冲突 |

## 指标

| 术语 | 说明 |
|---|---|
| **WIP** | 在制品。产线上尚未完成的数量 |
| **CT / Cycle Time** | 周期时间。一批从投料到完工的全部时间 |
| **X-factor** | CT 与理论加工时间的比值，衡量排队损耗 |
| **Utilization** | 设备利用率。注意区分"运行率"与"有效利用率" |
| **Throughput** | 吞吐量。单位时间产出，通常以片/天计 |
| **On-time Delivery** | 准时交付率，加急批管理的主指标 |

---

术语有歧义或想补充的，直接改这一页。**改之前先想清楚：这个词在九个区里是不是同一个意思。** 如果不是，应该拆成两条。
