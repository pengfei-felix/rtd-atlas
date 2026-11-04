---
title: Sorter / Stocker
---

# Sorter / Stocker 区派工档案

**全厂唯一按载具决策的区。** 它回答的不是"这批料上哪台机"，而是"进哪个 FOUP、放哪个格子、允不允许和别的料挨着"；载具不够，机台再空也上不了料。 <span class="badge badge--wip">0.1</span>

## 1 工艺与工序流

### 工艺内容

Sorter 把晶圆从一个载具换到另一个载具（合批、拆批、换 FOUP）；Stocker 是载具的立体仓库，带分区和自动存取。它不加工晶圆，只改变"哪片在哪个载具里、载具在哪个位置"。

严格说它不是工艺区，进区域分幅的理由是它有自己的约束结构：载具池、slot、污染分区，和加工区完全不同。而且它带污染等级——放错分区就是交叉污染。8 寸和 12 寸差一个量级：12 寸是 OHT + Stocker 网络，8 寸大量是人手暂存区。

### 工序

<div class="flowloop">
  <div class="flowline">
    <div class="flow-step"><strong>拆载具</strong></div>
    <div class="flow-arrow">→</div>
    <div class="flow-step"><strong>逐片搬运</strong></div>
    <div class="flow-arrow">→</div>
    <div class="flow-step"><strong>合批/拆批</strong></div>
    <div class="flow-arrow">→</div>
    <div class="flow-step"><strong>建新载具</strong></div>
    <div class="flow-arrow">→</div>
    <div class="flow-step"><strong>入库</strong></div>
  </div>
  <div class="flowloop-return">8 寸这一段大量靠人手，12 寸走 OHT 与 Stocker 网络</div>
</div>

- **拆载具**：从源 FOUP 取片，载具污染等级决定能不能进这个工位。
- **逐片搬运**：Sorter 机械手逐片搬，片数与搬运时间成正比。
- **合批 / 拆批**：按目标 lot 结构重组，合了再拆要多走一次。
- **建新载具**：写入新载具 ID 与片序，载具从池子里划走。
- **入库**：送进 stocker 对应污染分区，等待下一站。

### 派工特点

| 特点 | 落在哪一步 | 不管会怎样 |
|---|---|---|
| **载具池** | 建新载具 | FOUP 不够，机台再空也上不了料；报表上是设备 idle，实际是没载具 |
| **污染分区** | 入库 | 放错分区就是交叉污染，而且事后很难追溯是哪一次放错的 |
| **合批即锁定** | 合批 / 拆批 | 合了之后这批不能再拆给别的机台，后续派工的可行解被提前锁死 |
| **假性 idle** | 载具池 | 假性 idle 被当成设备问题去修，真正的瓶颈一直在载具上 |
| **8 寸与 12 寸量级差** | 全程 | 12 寸的 OHT + Stocker 可以精细化调度，8 寸人手暂存区只能按班次粗排 |
| **空载具回流** | 入库 | 空 FOUP 回不来，新一批有片没框，Sorter 干等 |

### 上下游

- **上游**：所有加工区出片之后，都要经过它换载具或暂存。
- **下游**：所有加工区上片之前，载具与位置决定这一批能不能开工。
- **横切**：AMHS / MCS 决定"送到哪儿"，Sorter 决定"哪片在哪个载具里、载具在哪个位置"。

## 2 专有派工功能

8 项，按影响程度排：先可行性（约束），再效率（策略），最后协调（接口）。前 4 项是 Sorter 自身的，后 4 项是「下一站去哪儿」，属全厂搬送去向规则，按接口归在这里。

| 功能 | 类型 | 功能描述 | 解决什么问题 |
|---|---|---|---|
| Carrier Status Check | 约束 | 排序时同时考虑 source lot、target lot、source carrier、target carrier 的状态 | 对应「载具池」：只看片不看框，派了也没框装 |
| Loading Balance | 策略 | 考虑不同 Function（Wafer Start、Wafer Out、Inline Transfer、Adhoc Transfer）对应的 Golden Tool 与水位 | 对应「假性 idle」：Sorter 自己被几个大类挤爆，别的工位空转 |
| 大小批穿插 | 策略 | Sorter High WIP 时把 Lot Size 纳入考虑，执行大批与小批穿插上机 | 对应「载具池」：大批连续占框，小批永远排不上 |
| IDLE Time 优先 | 策略 | 机台群内选机优先选 idle 时间最长的，其次未来最先 idle 的机台 | 对应「假性 idle」：总选同一台，其余设备空转却看不出来 |
| OHB Group 优先级 | 策略 | 选下一 location 时优先下一机台的 dedicate OHB，其次 Bay OHB，最后 default stocker | 对应「空载具回流」：放错位置，下一次搬送要多绕一段 |
| 空目的机台 | 策略 | Lot 无下一站对应机器时放到当前机器对应的存储位置，按 dedicate OHB → Bay OHB → default stocker 兜底 | 对应「假性 idle」：没地方放，料只能挂在机台上等 |
| Job Merge Seq | 接口 | 考虑 sorter job 信息，同一个 job 内的 lot 一起 jobPrep | 对应「合批即锁定」：一个 job 拆成几次做，载具被反复占用 |
| 空 Carrier | 接口 | 支持空 foup / fosb 在 stocker 之间搬送，不同楼层或区域可选不同 stocker | 对应「空载具回流」：空框回不去，有片没框 |

## 3 指标与落地

- [载具池与污染分区](01-载具池与污染分区.md) — 载具台账、三个状态维度、三套分区规则
- 指标口径见 [方法论 · 指标体系](../method/metrics.md)
- 本区尚未拆分独立的落地路线页
