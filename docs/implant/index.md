---
title: Implant 离子注入
---

# Implant 区派工档案

**Implant 的核心矛盾不是机台空不空，而是该不该切。** species、energy、dose、tilt 任一维度变化，都要重新整定束流并做剂量确认；派工如果只追求"有空就上"，换线损失会把产能吃掉。 <span class="badge badge--done">1.0</span>

## 1 工艺与工序流

### 工艺内容

把硼、磷、砷等离子加速到一定能量，直接打进硅片表面，改变导电类型和电阻率。阱、源漏、阈值调整都靠它。

注入本身很快，慢的是准备：换气体、换能量、换剂量都要重新整定束流。一次 setup 可能比一批的加工时间还长，所以这个区的目标不是"减少空转"，是"减少切换"。另外污染隔离是硬边界：不同 dopant、金属污染、光刻胶状态不能为产能让步。

### 工序

<div class="flowloop">
  <div class="flowline">
    <div class="flow-step"><strong>装载</strong></div>
    <div class="flow-arrow">→</div>
    <div class="flow-step"><strong>换气/换源</strong></div>
    <div class="flow-arrow">→</div>
    <div class="flow-step"><strong>束流整定</strong></div>
    <div class="flow-arrow">→</div>
    <div class="flow-step"><strong>扫描注入</strong></div>
    <div class="flow-arrow">→</div>
    <div class="flow-step"><strong>剂量校验</strong></div>
    <div class="flow-arrow">→</div>
    <div class="flow-step"><strong>卸载</strong></div>
  </div>
  <div class="flowloop-return">换气与整定占了大部分时间，注入本身只是最后几步</div>
</div>

- **装载**：进片、抽真空，晶圆贴到扫描盘上。
- **换气 / 换源**：按 species 换气体或换源，这是 setup 的大头，也是污染风险的来源。
- **束流整定**：调能量、调束流，等稳定；稳定时间与上一种 recipe 强相关。
- **扫描注入**：晶圆在束流下扫描或平移，剂量由扫过次数与束流乘积决定。
- **剂量校验**：用 Faraday cup 或测片确认实际剂量，偏了要返工。
- **卸载**：出片，进入注入后窗口计时，下一步通常是退火。

### 派工特点

| 特点 | 落在哪一步 | 不管会怎样 |
|---|---|---|
| **Setup family 与换线序列** | 换气 / 换源 | 换来换去，整定时间把产能吃光；同类不连着跑，等于每批都付一次 setup |
| **污染隔离** | 换源 | 不同 dopant 或金属污染的交叉是硬事故，产能再紧也不能让 |
| **高能力机保护** | 束流整定 | 高能/大束流机被拿去打普通层，关键层到时候没机可用 |
| **注入后窗口** | 卸载 → 退火 | 等得太久，胶硬化难去、掺杂扩散，后段全部被动 |
| **剂量 / 能量漂移** | 扫描注入 | 参数漂了整批电性不合格，而且要等量测才发现 |
| **Train limit** | 连续同 setup | 同一 setup 连跑超过上限，机器与束流状态下滑，良率开始抖 |

### 上下游

- **上游**：光刻与刻蚀已经定义出注入窗口，进机前通常先清洗。
- **下游**：去胶 → 退火（RTP / 炉管，归 Diffusion）→ 清洗 → 量测。
- **横切**：Metrology 测方块电阻与结深；Diffusion 承接注入后的退火窗口。

## 2 专有派工功能

3 项，按影响程度排：先可行性（约束），再效率（策略）。这个区的规则数量不多，但每条都直接决定换线成本。

| 功能 | 类型 | 功能描述 | 解决什么问题 |
|---|---|---|---|
| Train Limit | 约束 | 依 setup 定义与 train limit 计算同组 setup 内连续跑过的片数，超限就派其他 setup 的 Lot | 对应「Train limit」：不设上限，机器和束流状态一路下滑，良率抖了才发现 |
| Recipe Group | 约束 | 按 Recipe Group 分配机台（Implant / TF 共用） | 对应「Setup family 与换线序列」：配方组没锁住，机台会被派到不认识的 recipe 上 |
| Setup Cost | 策略 | 依 setup 定义与 cost，比较 source、energy、gas 之间的换线代价，优先派 cost 最小的 Lot | 对应「Setup family 与换线序列」：只顾眼前空闲，换线成本比空转还高 |

!!! note "清单覆盖面"
    这一区目前只有 3 条规则进清单，比 Litho 少得多。污染隔离、高能力机保护、注入后窗口这几条**目前还只是约束描述，没有独立功能项**——后面如果要补，就从这三处补。

## 3 指标与落地

- [Setup Family](01-setup-family.md) — 换线矩阵与 Campaign 边界
- [污染隔离与高能力机](02-污染隔离与高能力机.md) — 隔离规则与保护策略
- [注入后窗口与避坑](03-注入后窗口与避坑.md) — Q-time 后窗与上线检查
- 指标口径见 [方法论 · 指标体系](../method/metrics.md)
