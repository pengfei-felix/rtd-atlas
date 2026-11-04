---
title: TF 薄膜
---

# TF 薄膜区派工档案

**TF 是"长多少"比"谁来做"更重要的区。** 厚度、均匀性、应力三项一旦偏了，返工是整层剥离重做；所以派工不是找空闲机台，是找"能把这一层做对"的腔体。 <span class="badge badge--wip">0.1</span>

## 1 工艺与工序流

### 工艺内容

用 PVD（溅射）、CVD（化学气相沉积）、ALD（原子层沉积）在晶圆表面长金属或介质膜，导电层、阻挡层、绝缘层、硬掩膜都从这里来。和刻蚀正好相反：刻蚀减材，薄膜增材，多一层就不能回退。

长出来的膜要同时满足三件事——厚度落在窗口内、片内片间均匀、应力不把晶圆拉弯。三者都是连续量而不是合格/不合格，所以派工要看腔体当前状态，不能只看"这台机能不能跑"。12 寸的 PVD/CVD 多在同一台 cluster tool 上分腔，8 寸有不少单片与批式并存的老设备。

### 工序

<div class="flowloop">
  <div class="flowline">
    <div class="flow-step"><strong>装载</strong></div>
    <div class="flow-arrow">→</div>
    <div class="flow-step"><strong>除气</strong></div>
    <div class="flow-arrow">→</div>
    <div class="flow-step"><strong>前处理</strong></div>
    <div class="flow-arrow">→</div>
    <div class="flow-step"><strong>沉积</strong></div>
    <div class="flow-arrow">→</div>
    <div class="flow-step"><strong>冷却</strong></div>
    <div class="flow-arrow">→</div>
    <div class="flow-step"><strong>卸载</strong></div>
  </div>
  <div class="flowloop-return">沉积在真空腔里做，前处理到沉积之间不能久等</div>
</div>

- **装载**：进片、抽真空。
- **除气**：加热把吸附的水汽与残留赶走，否则膜里带气泡。
- **前处理**：预清洗或表面活化，决定膜的附着力。
- **沉积**：PVD 溅射靶材、CVD 通前驱体、ALD 逐层循环，厚度由时间或循环数控制。
- **冷却**：出炉前降温，降太快会把应力锁进膜里。
- **卸载**：出片，进量测。

### 派工特点

| 特点 | 落在哪一步 | 不管会怎样 |
|---|---|---|
| **靶材寿命分段** | 沉积 | 靶材末期膜厚与颗粒一起漂，临界段还派关键层，返工就是整层剥离 |
| **Chamber dedication** | 沉积 | 关键层锁死腔体不是偏好是不许换，硬换等于把这一层做废 |
| **前驱体余量** | 沉积 | 余量不够跑一半停，整批报废，剩下半批还得重排 |
| **膜厚与应力双约束** | 沉积 | 只盯厚度，应力漂了会在后续光刻和键合上爆出来 |
| **前处理与 Q-time** | 前处理 → 沉积 | 前处理完等太久界面重新氧化，附着力下降，膜会起皮 |
| **返工代价** | 检测不合格 | 镀错要 strip 重做，中间可能把已完成的层一起破坏 |

### 上下游

- **上游**：刻蚀或清洗之后，界面必须干净；部分层先经 CMP 平坦化。
- **下游**：光刻（要平坦面时再进 CMP），或下一道刻蚀。
- **横切**：CMP 负责平坦化，Metrology 测膜厚与应力。

## 2 专有派工功能

清单里本区只有 1 项。这个区的约束（靶材寿命、dedication、前驱体余量）目前还写在工艺块的「派工特点」里，尚未拆成独立功能项。

| 功能 | 类型 | 功能描述 | 解决什么问题 |
|---|---|---|---|
| Recipe Group | 约束 | 按 Recipe Group 分配机台（Implant / TF 共用） | 对应「Chamber dedication」：配方组没锁住，关键层会被派到不具备条件的腔体 |

!!! note "清单缺口"
    TF 与前七个区不同：功能清单里几乎没有它的条目。如果按现有规则补，至少还缺 **靶材分段准入、dedication 白名单、前驱体余量校验** 三项。补之前，这页的清单只有一行是正常的，不要误以为是漏写。

## 3 指标与落地

- [Dedication 与靶材](01-dedication与靶材.md) — 三层 dedication 与靶材寿命模型
- [膜厚反馈与返工](02-膜厚反馈与返工.md) — 反馈新鲜度与返工回路
- 指标口径见 [方法论 · 指标体系](../method/metrics.md)
- 本区尚未拆分独立的落地路线页
