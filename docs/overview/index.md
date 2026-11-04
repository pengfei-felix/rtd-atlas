---
title: 全厂派工全景
---

# 全厂派工全景

<div class="keyline" markdown>

**全厂派工 ≠ 九个区各自最优的加总。** 一个 Lot 从投料到出厂要穿过九个区、几百道工序，各区的局部最优拼起来通常不是全厂最优——因为瓶颈只有一个，非瓶颈区的"优化"经常只是把在制品往瓶颈那里推。

</div>

## 区域角色

同一个 Fab 里，九个区在派工网络中的角色完全不同。角色决定该往哪里投优化资源。

<div class="grid cards" markdown>

-   :material-image-filter-center-focus:{ .lg .middle } __Litho 光刻__

    ---

    **派工角色：物流配送中心。** 单片要反复返回 18~40 次，队列来自全厂所有层、所有产品。同时要解 `Lot → 机台`、`Reticle/Pod → 机台`、`机台 → 温层状态` 三个耦合方程。

    **工艺：** 涂胶 → 曝光 → 显影，把 Reticle 上的图形转印到光刻胶上。这层胶就是下一道刻蚀或注入的掩膜。

    <span class="badge badge--done">1.0</span>

    [:octicons-arrow-right-24: 进入分幅](../litho/index.md)<br>
    [:octicons-arrow-right-24: 工艺基础](../zones/process-basics.md#litho)

-   :material-layers-triple:{ .lg .middle } __Etch 刻蚀__

    ---

    **派工角色：粒度最细的区。** 决策对象是 chamber 而不是 tool，一台 cluster tool 内的多个腔体各有资质、PM 记录与 seasoning 阶段。

    **工艺：** 用等离子体把没被光刻胶保护的薄膜刻掉，图形真正落到晶圆上。刻不足短路，刻过度伤底层。

    <span class="badge badge--done">1.0</span>

    [:octicons-arrow-right-24: 进入分幅](../etch/index.md)<br>
    [:octicons-arrow-right-24: 工艺基础](../zones/process-basics.md#etch)

-   :material-thermometer:{ .lg .middle } __Diffusion 扩散__

    ---

    **派工角色：批量代价最高的区。** 核心决策不是"哪个 Lot 上哪台机"，而是"哪些 Lot 拼成一炉"。组炉做错，代价是数小时的炉管时间被浪费。

    **工艺：** 高温炉管里做氧化、扩散、退火，以及 LPCVD 薄膜沉积；也有单片的快速热处理。

    <span class="badge badge--done">1.0</span>

    [:octicons-arrow-right-24: 进入分幅](../diffusion/index.md)<br>
    [:octicons-arrow-right-24: 工艺基础](../zones/process-basics.md#diffusion)

-   :material-atom:{ .lg .middle } __Implant 离子注入__

    ---

    **派工角色：换线成本主导的区。** 气体、能量、剂量三维切换，单次 setup 可能超过一批的加工时间。优化目标从"减少空转"变成"减少切换"。

    **工艺：** 把硼、磷、砷等离子加速打进硅片表面，改变导电类型和电阻率。阱、源漏、阈值调整都靠它。

    <span class="badge badge--done">1.0</span>

    [:octicons-arrow-right-24: 进入分幅](../implant/index.md)<br>
    [:octicons-arrow-right-24: 工艺基础](../zones/process-basics.md#implant)

-   :material-layers-plus:{ .lg .middle } __TF 薄膜__

    ---

    **派工角色：增材区，"长多少"比"谁来做"更重要。** 决策粒度是腔体，靶材是消耗型资源，末期直接影响膜厚和颗粒。关键层 dedication 很强。

    **工艺：** PVD、CVD、ALD 在晶圆表面长金属或介质膜。导电层、阻挡层、绝缘层、硬掩膜都从这里来。

    <span class="badge badge--wip">0.1</span>

    [:octicons-arrow-right-24: 进入分幅](../tf/index.md)<br>
    [:octicons-arrow-right-24: 工艺基础](../zones/process-basics.md#tf)

-   :material-circle-half-full:{ .lg .middle } __CMP 研磨__

    ---

    **派工角色：唯一强闭环的区。** 上一批的厚度量测决定下一批的加工参数，研磨垫又是连续退化资源。派工与工艺控制不能分开算。

    **工艺：** 旋转的研磨垫压着晶圆、配合浆料，把表面凸起磨掉，给下一层光刻留一个全局平坦的表面。

    <span class="badge badge--done">1.0</span>

    [:octicons-arrow-right-24: 进入分幅](../cmp/index.md)<br>
    [:octicons-arrow-right-24: 工艺基础](../zones/process-basics.md#cmp)

-   :material-water:{ .lg .middle } __Clean / Wet 清洗__

    ---

    **派工角色：走廊起点，也是最大的不确定性。** 药液槽和研磨垫一样会连续退化；清洗出槽那一刻，晶圆表面从"受控"变成"暴露"，全厂大部分 Q-time 从这儿开始计时。

    **工艺：** 用酸碱药液、超纯水、有机溶剂去掉颗粒、金属离子、有机残留和自然氧化层。几乎每道关键工序前后都要洗一次。

    <span class="badge badge--wip">0.1</span>

    [:octicons-arrow-right-24: 进入分幅](../clean/index.md)<br>
    [:octicons-arrow-right-24: 工艺基础](../zones/process-basics.md#clean)

-   :material-ruler-square:{ .lg .middle } __Metrology 量测__

    ---

    **派工角色：两个身份。** 既是占产能的消费者，又是给别人供数据的生产者——CMP 等着它的结果才能定下一批参数。同一台机不同 recipe 时长能差 30 倍。

    **工艺：** 测膜厚、线宽、套刻精度、缺陷密度。不改变晶圆，只读取状态。

    <span class="badge badge--wip">0.1</span>

    [:octicons-arrow-right-24: 进入分幅](../metrology/index.md)<br>
    [:octicons-arrow-right-24: 工艺基础](../zones/process-basics.md#metrology)

-   :material-swap-horizontal-bold:{ .lg .middle } __Sorter / Stocker__

    ---

    **派工角色：唯一按载具决策的区。** FOUP 数量有限且独占，载具不够机台再空也上不了料。这种假性 idle 在报表上会被误判成设备问题。

    **工艺：** 换载具、合批拆批、暂存。不加工晶圆，只改变"哪片在哪个载具里、载具在哪个位置"。

    <span class="badge badge--wip">0.1</span>

    [:octicons-arrow-right-24: 进入分幅](../sorter/index.md)<br>
    [:octicons-arrow-right-24: 工艺基础](../zones/process-basics.md#sorter)

-   :material-table-large:{ .lg .middle } __区域对比__

    ---

    九个区的约束、粒度、批量性、反馈强度横向排开，并按决策粒度分成三组——同一组内规则骨架可共用。

    <span class="badge badge--done">1.0</span>

    [:octicons-arrow-right-24: 对比表](../zones/index.md)

</div>

## 三条耦合通道

区域不是并列的，它们通过三条通道互相拉扯。**任何一条通道没建模，RTD 排出来的计划在现场就不可行。**

| 通道 | 传什么 | 在哪引爆 | 典型症状 |
|---|---|---|---|
| **Lot 流** | 在制品与优先级 | 瓶颈区的队列 | 上游"提效"后瓶颈区 WIP 堆积，周期时间反而变长 |
| **辅助资源** | 载具、光罩、耗材、药液 | 共享资源的争抢点 | Reticle 被锁在别的机台，Lot 到了机台空转 |
| **时间窗** | Q-time / Timelink | 跨区的窗口串联 | 单段都合规，串起来超时；前段一慢，后段全崩 |

三条通道的具体形态，见 [跨幅专题](../topics/index.md)。它们的规则落在哪一层，见 [通用规则层](../common/index.md)。

## 各区的决策空间

派工的自由度不是均匀分布的。**自由度越小，越依赖规则；自由度越大、反馈越强，越值得上优化模型。**

| 区域 | 可选机台数 | 可选时序 | 反馈强度 | 建议路线 |
|---|---|---|---|---|
| Litho | 少（受 Reticle / Dedication 锁死） | 中 | 弱 | 规则为主 + 前瞻窗口 |
| Etch | 多（chamber 级） | 中 | 中 | 规则 + 局部优化 |
| Diffusion | 少（受炉型与配方限制） | 高（组炉决定一切） | 弱 | 组炉优化优先 |
| Implant | 中 | 高（换线序列相关） | 弱 | 序列优化优先 |
| TF | 中（受 dedication 限制） | 中 | 中偏强 | 规则为主 + 分段资源约束 |
| CMP | 多 | 中 | **强** | 派工 + run-to-run 联合建模 |
| Clean | 少（槽体数量有限） | 高（批式合流） | 弱 | 出口限流优先，合流次之 |
| Metrology | 少 | 高（recipe 时长差异大） | 弱 | 按时长算产能 + 数据紧急度 |
| Sorter | 不适用 | 中 | 无 | 载具池建模优先，分选优化次之 |

!!! tip "怎么用这张表"
    不要一上来就选算法。先看这个区的**决策空间形状**：如果可选机台本来就少、时序也没得挑，上再复杂的优化器也只是把规则包装了一遍。反过来，如果自由度大但反馈强（比如 CMP），只做派工不做参数闭环，收益会打折一半。

## 九个区之上还有一层

九个区各自配好，仍会出现「每个区都对、全厂就是不对」。因为有一批规则的作用对象跨区：Hot Lot 的优先级、Q-time 走廊的预算、辅助资源的预留、派工的冻结期。

这些不属于任何单个区，统一放在 **通用规则层**（G1 优先级与排序 / G2 时间窗走廊 / G3 资源与污染 / G4 稳定性治理）。它和区域层是正交的两层，不是并列的第十个区。

## 下一步

- 想知道"瓶颈到底在哪个区" → [瓶颈判定](bottleneck.md)
- 想横向对照九个区的约束 → [区域对比](../zones/index.md)
- 想知道跨区规则放在哪 → [通用规则层](../common/index.md)
- 想先看跨区串联的问题 → [Q-time 走廊](../topics/qtime-corridor.md)
