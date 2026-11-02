---
title: 全厂派工全景
---

# 全厂派工全景

<div class="keyline" markdown>

**全厂派工 ≠ 五个区各自最优的加总。** 一个 Lot 从投料到出厂要穿过五个区、几百道工序，五个区的局部最优拼起来通常不是全厂最优——因为瓶颈只有一个，非瓶颈区的"优化"经常只是把在制品往瓶颈那里推。

</div>

## 五个区的角色

同一个 Fab 里，五个区在派工网络中的角色完全不同。角色决定该往哪里投优化资源。

<div class="grid cards" markdown>

-   :material-image-filter-center-focus:{ .lg .middle } __Litho 光刻__

    ---

    **物流配送中心。** 单片要反复返回 18~40 次，队列来自全厂所有层、所有产品。同时要解 `Lot → 机台`、`Reticle/Pod → 机台`、`机台 → 温层状态` 三个耦合方程。

    <span class="badge badge--done">已完成</span>

    [:octicons-arrow-right-24: 进入分幅](../litho/index.md)

-   :material-layers-triple:{ .lg .middle } __Etch 刻蚀__

    ---

    **粒度最细的区。** 决策对象是 chamber 而不是 tool，一台 cluster tool 内的多个腔体各有资质、PM 记录与 seasoning 阶段。

    <span class="badge badge--wip">骨架</span>

    [:octicons-arrow-right-24: 进入分幅](../etch/index.md)

-   :material-thermometer:{ .lg .middle } __Diffusion 扩散__

    ---

    **唯一强批量的区。** 核心决策不是"哪个 Lot 上哪台机"，而是"哪些 Lot 拼成一炉"。组炉做错，代价是数小时的炉管时间被浪费。

    <span class="badge badge--wip">骨架</span>

    [:octicons-arrow-right-24: 进入分幅](../diffusion/index.md)

-   :material-atom:{ .lg .middle } __Implant 离子注入__

    ---

    **换线成本主导的区。** 气体、能量、剂量三维切换，单次 setup 可能超过一批的加工时间。优化目标从"减少空转"变成"减少切换"。

    <span class="badge badge--wip">骨架</span>

    [:octicons-arrow-right-24: 进入分幅](../implant/index.md)

-   :material-circle-half-full:{ .lg .middle } __CMP 研磨__

    ---

    **唯一强闭环的区。** 上一批的厚度量测决定下一批的加工参数，研磨垫又是连续退化资源。派工与工艺控制不能分开算。

    <span class="badge badge--wip">骨架</span>

    [:octicons-arrow-right-24: 进入分幅](../cmp/index.md)

</div>

## 三条耦合通道

区域不是并列的，它们通过三条通道互相拉扯。**任何一条通道没建模，RTD 排出来的计划在现场就不可行。**

| 通道 | 传什么 | 在哪引爆 | 典型症状 |
|---|---|---|---|
| **Lot 流** | 在制品与优先级 | 瓶颈区的队列 | 上游"提效"后瓶颈区 WIP 堆积，周期时间反而变长 |
| **辅助资源** | 载具、光罩、耗材 | 共享资源的争抢点 | Reticle 被锁在别的机台，Lot 到了机台空转 |
| **时间窗** | Q-time / Timelink | 跨区的窗口串联 | 单段都合规，串起来超时；前段一慢，后段全崩 |

三条通道的具体形态，见 [跨幅专题](../topics/index.md)。

## 各区的决策空间

派工的自由度不是均匀分布的。**自由度越小，越依赖规则；自由度越大、反馈越强，越值得上优化模型。**

| 区域 | 可选机台数 | 可选时序 | 反馈强度 | 建议路线 |
|---|---|---|---|---|
| Litho | 少（受 Reticle / Dedication 锁死） | 中 | 弱 | 规则为主 + 前瞻窗口 |
| Etch | 多（chamber 级） | 中 | 中 | 规则 + 局部优化 |
| Diffusion | 少（受炉型与配方限制） | 高（组炉决定一切） | 弱 | 组炉优化优先 |
| Implant | 中 | 高（换线序列相关） | 弱 | 序列优化优先 |
| CMP | 多 | 中 | **强** | 派工 + run-to-run 联合建模 |

!!! tip "怎么用这张表"
    不要一上来就选算法。先看这个区的**决策空间形状**：如果可选机台本来就少、时序也没得挑，上再复杂的优化器也只是把规则包装了一遍。反过来，如果自由度大但反馈强（比如 CMP），只做派工不做参数闭环，收益会打折一半。

## 下一步

- 想知道"瓶颈到底在哪个区" → [瓶颈判定](bottleneck.md)
- 想横向对照五个区的约束 → [区域对比](../zones/index.md)
- 想先看跨区串联的问题 → [Q-time 走廊](../topics/qtime-corridor.md)
