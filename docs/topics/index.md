---
title: 专题总览
---

# 跨幅专题

<div class="keyline" markdown>

**跨幅专题是不属于任何单个区域的图。** 就像地图集里的水系图和交通图——它们穿越行政边界，画的是"东西怎么流动"，而不是"某个地方长什么样"。

</div>

区域分幅回答"这个区有什么约束"，跨幅专题回答"东西如何在区与区之间流动、卡在哪里"。**后者通常是 RTD 项目真正能拿到收益的地方**，因为它对应的是全厂级的失衡，而不是单区的效率。

## 五张图

<div class="grid cards" markdown>

-   :material-timer-sand-complete:{ .lg .middle } __Q-time 走廊__

    ---

    一个 Lot 从投料到出厂要穿过几十个时间窗。**单段都合规，串起来超时**——这是最难查的一类问题。

    <span class="badge badge--done">1.0</span>

    [:octicons-arrow-right-24: 展开](qtime-corridor.md)

-   :material-swap-horizontal:{ .lg .middle } __辅助资源流转__

    ---

    Reticle、Pod、Pad、Boat 各有各的共享方式。**独占 / 成组 / 退化 / 消耗**，四种模式对应四种派工策略。

    <span class="badge badge--done">1.0</span>

    [:octicons-arrow-right-24: 展开](support-resource.md)

-   :material-sync:{ .lg .middle } __重入流路径__

    ---

    同一片晶圆要返回光刻区 18~40 次。**队列构成每十几分钟换一副面孔**，只盯当前队列的派工规则必然失真。

    <span class="badge badge--done">1.0</span>

    [:octicons-arrow-right-24: 展开](reentrant-flow.md)

-   :material-chart-timeline-variant-shimmer:{ .lg .middle } __指标冲突链__

    ---

    提吞吐会压垮什么？压 CT 会牺牲什么？**指标不是独立变量**，它们之间有确定的传导链。

    <span class="badge badge--done">1.0</span>

    [:octicons-arrow-right-24: 展开](metric-conflict.md)

-   :material-transit-connection-horizontal:{ .lg .middle } __AMHS/MCS 联动__

    ---

    Lot 可派不等于 FOUP 能到。RTD 必须把 ETA、port、stocker、Reticle pod 与设备空闲一起看。

    <span class="badge badge--done">1.0</span>

    [:octicons-arrow-right-24: 展开](amhs-mcs-coupling.md)

</div>

## 为什么先做专题，后填区域

区域分幅是**平行复制**：同一套模板再走一遍，信息增量有限。

跨幅专题是**新增信息**：现在整个图志里一条都没有，而它们恰恰是决策时最需要的。

| | 区域分幅 | 跨幅专题 |
|---|---|---|
| 回答的问题 | 这个区有哪些约束 | 约束之间怎么互相引爆 |
| 复用方式 | 换区重填 | 一次画完，全库受益 |
| 对决策的价值 | 中（局部） | 高（全局） |
| 当前版本 | 九区（5 个 1.0 + 4 个 0.1） | 五个专题 1.0 |

## 专题的写法

跨幅专题和区域分幅的体例不同，不要套五段模板：

1. **先画流动**。什么东西在流动？从哪到哪？经过哪些节点？
2. **再标卡点**。哪几个节点最容易堵？堵了会传导到哪里？
3. **最后给策略**。派工层能做什么，不能做什么，需要谁配合。

**不要写成"某某区的问题汇总"。** 那又变回区域分幅了，失去跨幅的意义。
