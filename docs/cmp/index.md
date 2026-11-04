---
title: CMP 研磨
---

# CMP 研磨区派工档案

**CMP 是派工与工艺控制耦合最强的区。** 同一台机、同一个 recipe，在不同 pad 年龄、conditioner 状态和上一批量测反馈下，实际表现都不同；只看机台可用性，就是把质量风险排进队列。 <span class="badge badge--done">1.0</span>

## 1 工艺与工序流

### 工艺内容

旋转的研磨垫压着晶圆，配合含磨料的浆料，把表面凸起的介质或金属磨掉，给下一层光刻留一个全局平坦的表面。没有它，多层堆叠后焦点压不住。

CMP 的判据是"磨掉多少"，而磨掉多少取决于 pad 状态、conditioner 状态、浆料以及上一批的结果——这是全厂唯一"上一批决定下一批"的地方，派工和参数控制分不开算。另外 Cu 与 non-Cu 的浆料、pad、刷子必须隔离，串了就是整线金属污染。

### 工序

<div class="flowloop">
  <div class="flowline">
    <div class="flow-step"><strong>装载</strong></div>
    <div class="flow-arrow">→</div>
    <div class="flow-step"><strong>主研磨</strong></div>
    <div class="flow-arrow">→</div>
    <div class="flow-step"><strong>终点控制</strong></div>
    <div class="flow-arrow">→</div>
    <div class="flow-step"><strong>清洗</strong></div>
    <div class="flow-arrow">→</div>
    <div class="flow-step"><strong>干燥</strong></div>
    <div class="flow-arrow">→</div>
    <div class="flow-step"><strong>量测</strong></div>
  </div>
  <div class="flowloop-return">磨完到量测这一段决定下一批参数，也是 R2R 的输入</div>
</div>

- **装载**：晶圆上 platen，靠 head 吸附固定，head 状态直接影响压力均匀性。
- **主研磨**：pad 与 slurry 按 recipe 去除材料，去除率随 pad 年龄连续变化。
- **终点控制**：按时间或终点信号停，磨过头不可逆，没有补回来这一步。
- **清洗**：去浆料与颗粒，刷子与药液按 Cu / non-Cu 分开。
- **干燥**：甩干或 IPA，防 watermark 与水痕。
- **量测**：测厚度与平坦度，结果回灌 R2R，同时决定下一批参数。

### 派工特点

| 特点 | 落在哪一步 | 不管会怎样 |
|---|---|---|
| **Pad 连续退化** | 主研磨 | 同一 recipe 在 pad 不同年龄上去除率不同，厚度跑偏且看不出来 |
| **R2R 反馈新鲜度** | 量测 → 下一批 | 反馈过期还按老参数跑，整批厚度偏，返工也救不回 |
| **Cu / non-Cu 污染隔离** | 清洗 / 干燥 | 串到别的区就是整线金属污染，停机清洗的代价远超产能损失 |
| **Post-CMP 接续** | 干燥 → 下一站 | 洗完到下一站等太久，颗粒与氧化回来，等于白洗 |
| **磨过头不可逆** | 终点控制 | 磨多只能返工或报废，没有"磨回来"这条路 |
| **Head / Platen 状态** | 装载 | 只看 tool 可用，实际 platen 或 head 不可用，派了也跑不了 |

### 上下游

- **上游**：成膜之后（介质或金属），或刻蚀之后的平坦化需求。
- **下游**：光刻（要平坦面）或下一次成膜。
- **横切**：Metrology 的厚度结果直接改下一批参数；Clean 承接 post-CMP 清洗。

## 2 专有派工功能

3 项，按影响程度排：先红线（校验），再可行性（约束），最后效率（策略）。

| 功能 | 类型 | 功能描述 | 解决什么问题 |
|---|---|---|---|
| CMP R2R Check | 校验 | 派工时检查 CMP APC R2R 状况，被 block 的 Lot 不派 | 对应「R2R 反馈新鲜度」：参数没更新就上机，整批厚度偏 |
| Pad Lift Time Check | 约束 | 选 ppid 时查机台的 pad lift time 信息，只选可用的 ppid | 对应「Pad 连续退化」：pad 到期还继续派，去除率断崖式变化 |
| 设定 Lot Size | 策略 | 依用户配置的小 Lot 片数决定派工，不连续派小片数 Lot | 对应「Head / Platen 状态」：小批连排把设备时间切碎，换片与整定开销翻倍 |

## 3 指标与落地

- [Pad 与 R2R](01-pad与R2R.md) — pad 状态分段、反馈新鲜度、派工策略
- [Post-CMP 与污染隔离](02-postCMP与污染隔离.md) — 接续、隔离与稳定 Run 保护
- 指标口径见 [方法论 · 指标体系](../method/metrics.md)
- 本区尚未拆分独立的落地路线页
