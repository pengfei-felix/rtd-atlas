---
title: 区域总览
---

# 区域派工档案 · 总览

<div class="keyline" markdown>

**九个区，三套决策粒度，两类资源结构。** 先用九区速查对齐口径，再看每个区在派工网络里扛的是什么角色——角色决定该往哪儿投优化资源。

</div>

## 九区速查

| 区域 | 决策粒度 | 辅助资源 | 批量性 | Q-time | 闭环 |
|---|---|---|---|---|---|
| [Litho 光刻](../litho/index.md) | Lot → Tool | **Reticle + Pod** | 单片 | 中（多段嵌套） | 弱 |
| [Etch 刻蚀](../etch/index.md) | Lot → **Chamber** | 腔体资质 / matching | 单片 | **高**（后段腐蚀） | 中 |
| [Diffusion 扩散](../diffusion/index.md) | **组炉** → Tool | Boat / 石英件 | **强批量** | 高（前后双窗） | 弱 |
| [Implant 注入](../implant/index.md) | **Batch** → Tool | 气体 / 靶材 | **强批量** | 高（退火窗口） | 弱 |
| [TF 薄膜](../tf/index.md) | Lot → **Chamber** | **靶材 / 前驱体** | 单片 | 中（native oxide） | 中偏强 |
| [CMP 研磨](../cmp/index.md) | Lot → Tool | **Pad**（连续退化） | 单片 | 中 | **强**（R2R） |
| [Clean / Wet 清洗](../clean/index.md) | **Batch** → Bath | **Bath**（化学消耗） | 批 + 单片 | **高**（走廊起点） | 弱 |
| [Metrology 量测](../metrology/index.md) | Lot → Tool | 校准片 / 标准片 | 单片 | 中 | 弱（但产数据） |
| [Sorter / Stocker](../sorter/index.md) | **Carrier** → Slot | **载具** | 不适用 | 低 | 无 |

## 区域角色

同一个 Fab 里，九个区在派工网络中的角色完全不同。同样是「优化这个区」，该投的东西并不一样。

<div class="grid cards" markdown>

-   :material-image-filter-center-focus:{ .lg .middle } __Litho 光刻__

    ---

    **派工角色：物流配送中心。** 单片要反复返回 18~40 次，队列来自全厂所有层、所有产品。同时要解 `Lot → 机台`、`Reticle/Pod → 机台`、`机台 → 温层状态` 三个耦合方程。

    **工艺：** 涂胶 → 曝光 → 显影，把 Reticle 上的图形转印到光刻胶上。这层胶就是下一道刻蚀或注入的掩膜。

    <span class="badge badge--done">1.0</span>

    [:octicons-arrow-right-24: 进入分幅](../litho/index.md)<br>
    [:octicons-arrow-right-24: 工艺基础](../basics/index.md#litho)

-   :material-layers-triple:{ .lg .middle } __Etch 刻蚀__

    ---

    **派工角色：粒度最细的区。** 决策对象是 chamber 而不是 tool，一台 cluster tool 内的多个腔体各有资质、PM 记录与 seasoning 阶段。

    **工艺：** 用等离子体把没被光刻胶保护的薄膜刻掉，图形真正落到晶圆上。刻不足短路，刻过度伤底层。

    <span class="badge badge--done">1.0</span>

    [:octicons-arrow-right-24: 进入分幅](../etch/index.md)<br>
    [:octicons-arrow-right-24: 工艺基础](../basics/index.md#etch)

-   :material-thermometer:{ .lg .middle } __Diffusion 扩散__

    ---

    **派工角色：批量代价最高的区。** 核心决策不是"哪个 Lot 上哪台机"，而是"哪些 Lot 拼成一炉"。组炉做错，代价是数小时的炉管时间被浪费。

    **工艺：** 高温炉管里做氧化、扩散、退火，以及 LPCVD 薄膜沉积；也有单片的快速热处理。

    <span class="badge badge--done">1.0</span>

    [:octicons-arrow-right-24: 进入分幅](../diffusion/index.md)<br>
    [:octicons-arrow-right-24: 工艺基础](../basics/index.md#diffusion)

-   :material-atom:{ .lg .middle } __Implant 离子注入__

    ---

    **派工角色：换线成本主导的区。** 气体、能量、剂量三维切换，单次 setup 可能超过一批的加工时间。优化目标从"减少空转"变成"减少切换"。

    **工艺：** 把硼、磷、砷等离子加速打进硅片表面，改变导电类型和电阻率。阱、源漏、阈值调整都靠它。

    <span class="badge badge--done">1.0</span>

    [:octicons-arrow-right-24: 进入分幅](../implant/index.md)<br>
    [:octicons-arrow-right-24: 工艺基础](../basics/index.md#implant)

-   :material-layers-plus:{ .lg .middle } __TF 薄膜__

    ---

    **派工角色：增材区，"长多少"比"谁来做"更重要。** 决策粒度是腔体，靶材是消耗型资源，末期直接影响膜厚和颗粒。关键层 dedication 很强。

    **工艺：** PVD、CVD、ALD 在晶圆表面长金属或介质膜。导电层、阻挡层、绝缘层、硬掩膜都从这里来。

    <span class="badge badge--wip">0.1</span>

    [:octicons-arrow-right-24: 进入分幅](../tf/index.md)<br>
    [:octicons-arrow-right-24: 工艺基础](../basics/index.md#tf)

-   :material-circle-half-full:{ .lg .middle } __CMP 研磨__

    ---

    **派工角色：唯一强闭环的区。** 上一批的厚度量测决定下一批的加工参数，研磨垫又是连续退化资源。派工与工艺控制不能分开算。

    **工艺：** 旋转的研磨垫压着晶圆、配合浆料，把表面凸起磨掉，给下一层光刻留一个全局平坦的表面。

    <span class="badge badge--done">1.0</span>

    [:octicons-arrow-right-24: 进入分幅](../cmp/index.md)<br>
    [:octicons-arrow-right-24: 工艺基础](../basics/index.md#cmp)

-   :material-water:{ .lg .middle } __Clean / Wet 清洗__

    ---

    **派工角色：走廊起点，也是最大的不确定性。** 药液槽和研磨垫一样会连续退化；清洗出槽那一刻，晶圆表面从"受控"变成"暴露"，全厂大部分 Q-time 从这儿开始计时。

    **工艺：** 用酸碱药液、超纯水、有机溶剂去掉颗粒、金属离子、有机残留和自然氧化层。几乎每道关键工序前后都要洗一次。

    <span class="badge badge--wip">0.1</span>

    [:octicons-arrow-right-24: 进入分幅](../clean/index.md)<br>
    [:octicons-arrow-right-24: 工艺基础](../basics/index.md#clean)

-   :material-ruler-square:{ .lg .middle } __Metrology 量测__

    ---

    **派工角色：两个身份。** 既是占产能的消费者，又是给别人供数据的生产者——CMP 等着它的结果才能定下一批参数。同一台机不同 recipe 时长能差 30 倍。

    **工艺：** 测膜厚、线宽、套刻精度、缺陷密度。不改变晶圆，只读取状态。

    <span class="badge badge--wip">0.1</span>

    [:octicons-arrow-right-24: 进入分幅](../metrology/index.md)<br>
    [:octicons-arrow-right-24: 工艺基础](../basics/index.md#metrology)

-   :material-swap-horizontal-bold:{ .lg .middle } __Sorter / Stocker__

    ---

    **派工角色：唯一按载具决策的区。** FOUP 数量有限且独占，载具不够机台再空也上不了料。这种假性 idle 在报表上会被误判成设备问题。

    **工艺：** 换载具、合批拆批、暂存。不加工晶圆，只改变"哪片在哪个载具里、载具在哪个位置"。

    <span class="badge badge--wip">0.1</span>

    [:octicons-arrow-right-24: 进入分幅](../sorter/index.md)<br>
    [:octicons-arrow-right-24: 工艺基础](../basics/index.md#sorter)

</div>
