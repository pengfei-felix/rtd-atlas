---
title: 区域总览
---

# 区域派工档案 · 总览

<div class="keyline" markdown>

**九个区不是九段并列的工序。** 晶圆在一条单层循环上反复穿行，同一片要回到同一个区十几次；另外三个区不在环上，却各自卡着整条线的脖子。先看清谁喂谁，再看每个区该投什么。

</div>

## 区域流传关系

按一层典型工艺走一遍，六个主线区首尾相接，然后回到起点做下一层：

<div class="flowloop">
  <div class="flowline">
    <div class="flow-step"><strong>Litho</strong><span class="zf-meta">Lot→Tool</span></div>
    <div class="flow-arrow">→</div>
    <div class="flow-step"><strong>Etch</strong><span class="zf-meta">Lot→Chamber</span></div>
    <div class="flow-arrow">→</div>
    <div class="flow-step"><strong>Implant</strong><span class="zf-meta">Batch→Tool</span></div>
    <div class="flow-arrow">→</div>
    <div class="flow-step"><strong>Diffusion</strong><span class="zf-meta">组炉→Tool</span></div>
    <div class="flow-arrow">→</div>
    <div class="flow-step"><strong>TF</strong><span class="zf-meta">Lot→Chamber</span></div>
    <div class="flow-arrow">→</div>
    <div class="flow-step"><strong>CMP</strong><span class="zf-meta">Lot→Tool</span></div>
  </div>
  <div class="flowloop-return">↩ 下一层，回到 Litho</div>
</div>

<div class="rtd-strip" markdown>

<span>贯穿全程</span> Clean / Wet —— 不是一站，是每站前后的动作：进炉前洗、刻蚀后洗、研磨后洗。大部分 Q-time 从出槽那一刻开始计时。  
<span>挂点</span> Metrology —— 卡在关键工序之后，既是占产能的消费者，也是下游参数的来源（CMP 等它的结果才能定下一批）。  
<span>另一层</span> Sorter / Stocker —— 不在晶圆价值流上，只决定"哪片在哪个载具里"。载具不够，机台再空也上不了料。

</div>

这是典型 12 寸逻辑的单层循环；8 寸铝互连、存储与特殊工艺的次序有出入，但「六个主线区 + 三个不在主线的位置」这个结构不变。每个区在晶圆上具体做什么，见 [工艺基础](../basics/index.md)。

## 区域角色

位置相同不等于角色相同。主线六区都在环上，但 Litho 是枢纽、Diffusion 是批量代价最高的一站、CMP 是唯一强闭环——这决定了优化资源往哪儿投。

<div class="grid cards" markdown>

-   :material-image-filter-center-focus:{ .lg .middle } __Litho 光刻__

    ---

    <span class="zf-tag">图形转印</span>

    **派工角色：物流配送中心。** 单片要反复返回 18~40 次，队列来自全厂所有层、所有产品。同时要解 `Lot → 机台`、`Reticle/Pod → 机台`、`机台 → 温层状态` 三个耦合方程。

    **工艺：** 涂胶 → 曝光 → 显影，把 Reticle 上的图形转印到光刻胶上。这层胶就是下一道刻蚀或注入的掩膜。

    <span class="badge badge--done">1.0</span>

    [:octicons-arrow-right-24: 进入分幅](../litho/index.md)<br>
    [:octicons-arrow-right-24: 工艺基础](../basics/index.md#litho)

-   :material-layers-triple:{ .lg .middle } __Etch 刻蚀__

    ---

    <span class="zf-tag">图形刻蚀</span>

    **派工角色：粒度最细的区。** 决策对象是 chamber 而不是 tool，一台 cluster tool 内的多个腔体各有资质、PM 记录与 seasoning 阶段。

    **工艺：** 用等离子体把没被光刻胶保护的薄膜刻掉，图形真正落到晶圆上。刻不足短路，刻过度伤底层。

    <span class="badge badge--done">1.0</span>

    [:octicons-arrow-right-24: 进入分幅](../etch/index.md)<br>
    [:octicons-arrow-right-24: 工艺基础](../basics/index.md#etch)

-   :material-atom:{ .lg .middle } __Implant 离子注入__

    ---

    <span class="zf-tag">掺杂</span>

    **派工角色：换线成本主导的区。** 气体、能量、剂量三维切换，单次 setup 可能超过一批的加工时间。优化目标从"减少空转"变成"减少切换"。

    **工艺：** 把硼、磷、砷等离子加速打进硅片表面，改变导电类型和电阻率。阱、源漏、阈值调整都靠它。

    <span class="badge badge--done">1.0</span>

    [:octicons-arrow-right-24: 进入分幅](../implant/index.md)<br>
    [:octicons-arrow-right-24: 工艺基础](../basics/index.md#implant)

-   :material-thermometer:{ .lg .middle } __Diffusion 扩散__

    ---

    <span class="zf-tag">热处理</span>

    **派工角色：批量代价最高的区。** 核心决策不是"哪个 Lot 上哪台机"，而是"哪些 Lot 拼成一炉"。组炉做错，代价是数小时的炉管时间被浪费。

    **工艺：** 高温炉管里做氧化、扩散、退火，以及 LPCVD 薄膜沉积；也有单片的快速热处理。

    <span class="badge badge--done">1.0</span>

    [:octicons-arrow-right-24: 进入分幅](../diffusion/index.md)<br>
    [:octicons-arrow-right-24: 工艺基础](../basics/index.md#diffusion)

-   :material-layers-plus:{ .lg .middle } __TF 薄膜__

    ---

    <span class="zf-tag">成膜</span>

    **派工角色：增材区，"长多少"比"谁来做"更重要。** 决策粒度是腔体，靶材是消耗型资源，末期直接影响膜厚和颗粒。关键层 dedication 很强。

    **工艺：** PVD、CVD、ALD 在晶圆表面长金属或介质膜。导电层、阻挡层、绝缘层、硬掩膜都从这里来。

    <span class="badge badge--wip">0.1</span>

    [:octicons-arrow-right-24: 进入分幅](../tf/index.md)<br>
    [:octicons-arrow-right-24: 工艺基础](../basics/index.md#tf)

-   :material-circle-half-full:{ .lg .middle } __CMP 研磨__

    ---

    <span class="zf-tag">平坦化</span>

    **派工角色：唯一强闭环的区。** 上一批的厚度量测决定下一批的加工参数，研磨垫又是连续退化资源。派工与工艺控制不能分开算。

    **工艺：** 旋转的研磨垫压着晶圆、配合浆料，把表面凸起磨掉，给下一层光刻留一个全局平坦的表面。

    <span class="badge badge--done">1.0</span>

    [:octicons-arrow-right-24: 进入分幅](../cmp/index.md)<br>
    [:octicons-arrow-right-24: 工艺基础](../basics/index.md#cmp)

-   :material-water:{ .lg .middle } __Clean / Wet 清洗__

    ---

    <span class="zf-tag">贯穿全程</span>

    **派工角色：走廊起点，也是最大的不确定性。** 药液槽和研磨垫一样会连续退化；清洗出槽那一刻，晶圆表面从"受控"变成"暴露"，全厂大部分 Q-time 从这儿开始计时。

    **工艺：** 用酸碱药液、超纯水、有机溶剂去掉颗粒、金属离子、有机残留和自然氧化层。几乎每道关键工序前后都要洗一次。

    <span class="badge badge--wip">0.1</span>

    [:octicons-arrow-right-24: 进入分幅](../clean/index.md)<br>
    [:octicons-arrow-right-24: 工艺基础](../basics/index.md#clean)

-   :material-ruler-square:{ .lg .middle } __Metrology 量测__

    ---

    <span class="zf-tag">挂点 · 产数据</span>

    **派工角色：两个身份。** 既是占产能的消费者，又是给别人供数据的生产者——CMP 等着它的结果才能定下一批参数。同一台机不同 recipe 时长能差 30 倍。

    **工艺：** 测膜厚、线宽、套刻精度、缺陷密度。不改变晶圆，只读取状态。

    <span class="badge badge--wip">0.1</span>

    [:octicons-arrow-right-24: 进入分幅](../metrology/index.md)<br>
    [:octicons-arrow-right-24: 工艺基础](../basics/index.md#metrology)

-   :material-swap-horizontal-bold:{ .lg .middle } __Sorter / Stocker__

    ---

    <span class="zf-tag">另一层 · 载具</span>

    **派工角色：唯一按载具决策的区。** FOUP 数量有限且独占，载具不够机台再空也上不了料。这种假性 idle 在报表上会被误判成设备问题。

    **工艺：** 换载具、合批拆批、暂存。不加工晶圆，只改变"哪片在哪个载具里、载具在哪个位置"。

    <span class="badge badge--wip">0.1</span>

    [:octicons-arrow-right-24: 进入分幅](../sorter/index.md)<br>
    [:octicons-arrow-right-24: 工艺基础](../basics/index.md#sorter)

</div>
