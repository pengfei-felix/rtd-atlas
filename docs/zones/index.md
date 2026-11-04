---
title: 区域总览
---

# 区域派工档案 · 总览

<div class="keyline" markdown>

**九个区，三套决策粒度，两类资源结构。** 不能拿一套派工逻辑套全场——这是本文档集的核心立场。

</div>

## 为什么是九个

划分依据不是「Fab 里有哪些工艺」，是**约束签名是否同构**。同构的可以共用一套规则骨架，不同构的必须拆开。用五个问题筛：

1. 决策粒度是 Lot、chamber、batch 还是 carrier？
2. 有没有独占辅助资源？Reticle / Pod / Pad / Boat / Bath / Target / Carrier
3. setup 是序列相关还是固定代价？
4. Q-time 窗口几段、嵌套几层？
5. 有没有 run-to-run 闭环？

按这五条筛完是九个区。多于九个是拆过细（管理成本高），少于九个是拆过粗（规则会串味）。

!!! note "关于 Multichamber"
    **Multichamber 不是区域，是「决策粒度」维度的一个取值。**

    它决定的是"Lot 上 tool 还是上 chamber"，挂在 Etch / TF / Implant 这些 cluster tool 下面。把它当成区会重复计数，且会让规则库出现两套 chamber 逻辑。

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

## 按粒度分三组

粒度是划分 rule set 的第一依据，比工艺相似性更根本。同一组内的区，规则骨架可以共用大半。

| 组 | 成员 | 可共用 | 必须各配 |
|---|---|---|---|
| **A · 单片 / 机台级** | Litho、CMP、Metrology、Sorter | 排序骨架、辅助资源锁定、老化升权 | Litho 的 Reticle 约束、CMP 的闭环、Metrology 的数据紧急度、Sorter 的载具池 |
| **B · 单片 / 腔体级** | Etch、TF | chamber 状态建模、matching 分组、seasoning 逻辑 | Etch 的污染序列、TF 的靶材退化 |
| **C · 强批量** | Diffusion、Implant、Clean | 组批 / 合流框架、装载率 vs 走廊余量的权衡 | Diffusion 的升降温曲线、Implant 的 setup family、Clean 的 bath 分段 |

**A 组最容易做错的是把 Sorter 当成物流附件。** 它的粒度是 carrier，不是 lot，混进去会让整个 A 组的模型口径不统一。

**C 组三个区的批量逻辑不同：**

| 区 | 组批目的 | 欠载代价 |
|---|---|---|
| Diffusion | 填满炉管，摊薄长周期 | **高**，浪费数小时炉时 |
| Implant | 减少换气换能量次数 | 中，主要是效率损失 |
| Clean | 提高槽体利用率 | **低**，可以欠载出发 |

最后一行是关键区别：清洗槽装不满可以走，炉管装不满是实打实的浪费。把三者用同一套组批规则，清洗区会被迫等批，走廊直接断。

## 读一个区的方法

不管是哪个区，都按同一个顺序拆，**顺序不能乱**：

1. **先数辅助资源**。除了机台，还有什么东西是「同一时刻只能被一处使用」的？Litho 是 Reticle/Pod，CMP 是 Pad，Clean 是 Bath，Sorter 是载具。这一层最容易被漏，也最容易造成「计划可行、现场做不到」。
2. **再定决策粒度**。Lot 级、chamber 级、batch 级还是 carrier 级？粒度错了，后面所有优化都白做。
3. **然后区分 setup 的两类**。序列相关（换线顺序有讲究）vs 固定（换一次就是固定代价），两者在优化里的处理方式完全不同。
4. **最后看 Q-time 的嵌套层数**。单窗口好处理，多窗口串联会互相锁死。
5. **闭环反馈单独建模**。CMP 这种「上一批结果决定下一批参数」的，派工和工艺控制不能分开算。

## 各区档案

不熟悉工艺的，先看 [区域工艺基础](process-basics.md)：九个区在晶圆上各做什么、为什么这些动作会变成派工约束。

**核心五区（内容完整 1.0）**

- [Litho 光刻](../litho/index.md) — 八重约束、九类问题、技术谱系、指标与路线图
- [Etch 刻蚀](../etch/index.md) — chamber 级约束、PM/seasoning、Q-time 后窗
- [Diffusion 扩散](../diffusion/index.md) — 组炉策略、温区切换、Boat/Quartzware 约束
- [Implant 离子注入](../implant/index.md) — setup family、污染隔离、束流整定、Q-time 后窗
- [CMP 研磨](../cmp/index.md) — Pad 退化、Run-to-Run 反馈、post-CMP 接续

**补齐四区（骨架 0.1，结构与规则骨架已成形）**

- [TF 薄膜](../tf/index.md) — chamber dedication、靶材寿命分段、膜厚与应力反馈、返工回路
- [Clean / Wet 清洗](../clean/index.md) — bath 退化、批式合流、Q-time 走廊起点、出口限流
- [Metrology 量测](../metrology/index.md) — 采样规则、recipe 时长陷阱、数据紧急度、缺陷检测
- [Sorter / Stocker](../sorter/index.md) — 载具池、污染分区、slot/port、8 寸状态可见性

## 区域之上还有一层

九个区各自配好了，还是会出现「每个区都对、全厂就是不对」的情况。原因是有一批规则的作用对象跨区，不属于任何单个区。

这类规则统一放在 [通用规则层](../common/index.md)：优先级与排序、时间窗走廊、资源与污染、稳定性治理。它和区域层是**正交**的两层，不是并列的第十个区。

---

相关：[全厂派工全景](../overview/index.md) · [通用规则层](../common/index.md) · [约束四类](../legend/constraint-types.md)
