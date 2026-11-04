---
title: Etch 区派工档案
---

# Etch 区派工档案

**Etch 派工不能停在 tool 级。** 真正加工晶圆的是 chamber，一台 cluster tool 里的腔体各有资质、PM 记录、seasoning 阶段和污染状态；只把整台机当成一个资源，排出来的计划会天然失真。 <span class="badge badge--done">1.0</span>

## 1 工艺与工序流

### 工艺内容

用等离子体把没被光刻胶保护的薄膜去掉，把光刻的图形真正刻进晶圆。刻不准的代价是双向的：刻不足会短路，刻过度会伤底层，所以验收看的是 CD、深度、侧壁形貌三项，不是"做完没做完"。

12 寸的刻蚀几乎都在 cluster tool 里做：晶圆进真空腔，先在传输腔里分配到某个工艺腔，刻完再传出来。8 寸干法与湿法并存，湿法靠药液腐蚀，通常用在尺寸要求不高的层。

### 工序

<div class="flowloop">
  <div class="flowline">
    <div class="flow-step"><strong>装载</strong></div>
    <div class="flow-arrow">→</div>
    <div class="flow-step"><strong>抽真空/稳定</strong></div>
    <div class="flow-arrow">→</div>
    <div class="flow-step"><strong>主刻</strong></div>
    <div class="flow-arrow">→</div>
    <div class="flow-step"><strong>终点检测</strong></div>
    <div class="flow-arrow">→</div>
    <div class="flow-step"><strong>过刻</strong></div>
    <div class="flow-arrow">→</div>
    <div class="flow-step"><strong>卸载</strong></div>
  </div>
  <div class="flowloop-return">全程在真空腔内，出腔即开始计走廊时间</div>
</div>

- **装载**：晶圆先进 load lock，再换到真空。
- **抽真空 / 稳定**：气压、温度、气体配比到位才能起辉，这一段是固定开销。
- **主刻**：等离子体刻穿目标膜，刻蚀率与形貌由 recipe 和腔体状态共同决定。
- **终点检测**：靠 OES 或干涉信号判断刻到界面，终点判错就过刻或欠刻。
- **过刻**：按比例多刻一点，把残留清干净，代价是底层被削薄。
- **卸载**：破真空出片，进 strip 或清洗。

### 派工特点

| 特点 | 落在哪一步 | 不管会怎样 |
|---|---|---|
| **Chamber 资质与 matching** | 分配腔体 | 关键层派到不匹配的腔，CD 与 profile 一起漂，返工比空等贵 |
| **PM / clean / seasoning 恢复期** | 主刻 | PM 完直接按满产能排，前几片良率掉；刚洗过的腔没跑 season 片就抢产能，恢复期被打乱 |
| **Recipe 切换与污染序列** | 换 recipe | 先刻金属再刻多晶的交叉污染，整批报废；切换序列没锁住，前后批互相带毒 |
| **Q-time 后窗** | 卸载 → strip / clean | 刻完的侧壁暴露在空气里，等得越久腐蚀越深，单段都合规也可能串起来超时 |
| **R2R 参数漂移** | 量测 → 下一批主刻 | 参数没跟上量测结果，CD 连续漂移，直到返工才暴露 |
| **Rework 回路** | 检测不合格 → 重刻 | 重刻要走一遍同样的腔体约束，把队列再堵一次，原 Q-time 也已经消耗掉 |

### 上下游

- **上游**：光刻显影之后，图形已经定好；部分层先过一次清洗。
- **下游**：strip 去胶 → 清洗 → 量测（CD / 深度），之后进注入或成膜。
- **横切**：Metrology 取 CD 与缺陷；Clean 管刻后清洗，也是 Q-time 后窗的起点。

## 2 专有派工功能

6 项，按影响程度排：先红线（校验），再效率（策略），最后协调（接口）。这个区的差异不在"有没有机台"，在**腔体状态能不能用**。

| 功能 | 类型 | 功能描述 | 解决什么问题 |
|---|---|---|---|
| Etch R2R Check | 校验 | 派工前检查 APC R2R 规则，被 R2R 系统 block 的 Lot 不派工 | 对应「R2R 参数漂移」：参数没改就上机，整批 CD 偏，返工也救不回来 |
| Chamber IDLE Time | 策略 | 选择 ppid 时看各腔体当前 idle 时长，优先派给先空闲的腔 | 对应「PM / clean / seasoning 恢复期」：总用同一批腔，其余空转，恢复节奏也被打乱 |
| Chamber Count | 策略 | 选 ppid 时优先派能用腔体更多的程序 | 对应「Chamber 资质与 matching」：只走单腔，等于把并行度锁在 1 |
| Recipe Train | 策略 | 看上一批 Lot 的 recipe，同组 Lot 提高优先级，避免频繁换层做 season | 对应「Recipe 切换与污染序列」：换层就要做 season，一次换层浪费的是整段产能 |
| Leading Qty | 策略 | 看腔体当前 Lot 的剩余加工片数，按水位控制下一批上货时间 | 对应「Q-time 后窗」：上游放得太快，腔口排队，下片时间被推后 |
| Seq Run | 接口 | 需要搭 season 或 dummy 时预留足够的 Load，CDW 与 Product 一起预约上机台 | 对应「PM / clean / seasoning 恢复期」：不带 season 片，腔体恢复不了，关键层上来就漂 |

## 3 指标与落地

- [Chamber 级建模](01-chamber级建模.md) — 三层资源模型与候选集生成
- [PM / Clean / Seasoning](02-PM-clean-seasoning.md) — 状态机与恢复窗口
- [避坑清单](05-避坑清单.md) — 上线前的最小检查
- 指标口径见 [方法论 · 指标体系](../method/metrics.md)
