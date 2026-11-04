---
title: 约束模型
---

# 约束模型

<div class="keyline" markdown>

**现场约束只有翻译成资源、状态、窗口和动作，RTD 才能计算。** 如果约束只停留在“这批最好不要跑那台机”这种口头经验，系统一定会把不可行计划排出来，再由人兜底。

</div>

## 四类资源

任何区的派工约束，最终都能归到四类资源里。

| 类别 | 定义 | 典型例子 | 常见错误 |
|---|---|---|---|
| **主资源** | 真正执行加工的设备或子单元 | tool、chamber、tube、platen、head | 粒度过粗，只建 tool 不建 chamber |
| **辅助资源** | 加工必需但不能随意共享的资源 | Reticle、Pod、Boat、Pad、gas、slurry | 建成 available 布尔值，漏位置/寿命/搬送 |
| **时间窗资源** | 只能在指定时间内消耗的机会 | Q-time、Timelink、hold time | 只做报警，不进派工目标 |
| **外部资源** | 受库存、班次、设施能力限制的投入 | AMHS、工程师、chemical、exhaust | 认为“不归 RTD 管”就不建模 |

判断一个约束属于哪一类，不看它叫什么，看它怎么限制派工：限制加工对象的是主资源，限制可开工条件的是辅助资源，限制时间的是时间窗，限制支撑能力的是外部资源。

## 区域映射表

| 区域 | 主资源 | 辅助资源 | 时间窗资源 | 外部资源 |
|---|---|---|---|---|
| Litho | Scanner / Track | Reticle、Pod、Tool Library | 涂胶→曝光、显影→刻蚀 | ARHS/OHT、BRS、Reticle inspection |
| Etch | Chamber | chamber condition、gas、clean kit | Etch out→strip/clean/metrology | abatement、exhaust、special gas |
| Diffusion | Furnace / Tube | Boat、Quartzware、temperature profile | clean→furnace、furnace out→next step | gas、operator、clean capacity |
| Implant | Implanter | source、beamline state、setup family | implant→anneal/strip | gas、beam tune、confirmation metrology |
| CMP | Platen / Head | Pad、conditioner、slurry | CMP out→clean/metrology | slurry supply、post-CMP clean、metrology |

这张表是建模入口。每新写一个区域，都先把四类资源填完，再写派工规则。

## 资源字段标准

| 字段组 | 字段 | 说明 |
|---|---|---|
| 身份 | id、type、area、owner | 资源是谁，归哪个区 |
| 位置 | location、target_location、transfer_time | 资源在哪里，到位要多久 |
| 能力 | qualified_recipe、product、layer、family | 能跑什么 |
| 状态 | state、available_time、lock_owner | 当前能不能用，被谁占 |
| 寿命 | usage_count、warning_limit、inhibit_limit | 还能用多久 |
| 维护 | pm_due、clean_due、inspection_due、seasoning_state | 是否临近维护 |
| 约束 | exclusive_scope、group_id、capacity | 独占、成组、容量限制 |

最小可用模型不一定所有字段都齐，但结构要预留。否则后面接辅助资源时会推倒重来。

## 两类建模错误

### 1. 把连续量建成布尔量

Reticle 不是“有/没有”，Pad 不是“好/坏”，chamber 也不是“可用/不可用”。它们都有中间状态。

| 资源 | 错误模型 | 正确模型 |
|---|---|---|
| Reticle | available | location + lock + transfer_time + inspection_limit |
| Pad | usable | age + removal_rate_segment + warning_zone |
| Chamber | up/down | qualification + PM state + seasoning + drift |
| Boat | available | material + location + clean state + compatible family |

布尔化的后果是：系统认为“可用”，现场认为“不适合”。

### 2. 把辅助资源留在模型外

只对主资源建模，辅助资源靠现场临时调度，是白空间最常见来源。

```text
RTD：Tool 空闲，Lot 可加工 → 派工
现场：Reticle 不在 / Boat 不合适 / Pad 末期 → 等
结果：系统显示已派工，机台实际空转
```

正确做法是把辅助资源作为候选集的一部分，而不是派工后的补丁。

## 目标函数怎么写

目标函数不是越复杂越好。第一版可以从可解释的加权评分开始：

```text
score(lot, resource) =
  w1 · due_risk
+ w2 · qtime_risk
+ w3 · setup_cost_inverse
+ w4 · resource_ready_score
+ w5 · bottleneck_feed_score
- w6 · downstream_block_risk
- w7 · auxiliary_move_cost
```

关键不是公式，而是每个权重有业务含义、能回放、能解释。

## 权重标定方法

| 方法 | 适用 | 注意 |
|---|---|---|
| 专家初值 | 项目早期 | 必须配回放验证 |
| 历史回放 | 有完整事件流 | 最推荐，用旧数据比较新规则 |
| 影子模式 | 上线前 | 系统给建议但不执行，看命中率与风险 |
| A/B 对比 | 局部上线 | 要控制产品 mix 和设备状态 |
| 自动调参 | 规则稳定后 | 不能绕过硬约束和解释要求 |

别一上来就自动调参。约束没建对，调出来的是错误局部最优。

## 从现场话到系统约束

| 现场说法 | 系统翻译 |
|---|---|
| 这批不要上那台机 | dedication / qualification rule |
| 这张罩快到检查限 | resource remaining_life warning |
| 这台 PM 后先别跑关键层 | post-PM seasoning state |
| 这个 recipe 最好连着跑 | setup family / campaign rule |
| 这批再等就要超时 | qtime_risk nonlinear weight |
| 这台高能力机别跑低要求活 | scarce capability protection |

RTD 项目成败，很大程度取决于这张翻译表做得细不细。

## 建模检查清单

- [ ] 主资源粒度是否正确到 chamber / tube / platen？
- [ ] 辅助资源是否有位置、锁定、寿命、搬送时间？
- [ ] Q-time 是否进入派工排序，而不是只报警？
- [ ] setup 是否区分序列相关和固定代价？
- [ ] 规则是否有原因码和日志？
- [ ] 每条软约束是否有验证指标？
- [ ] 硬约束是否绝不被权重覆盖？

---

相关：[辅助资源流转](../topics/support-resource.md) · [Q-time 走廊](../topics/qtime-corridor.md) · [数据模型](data-model.md)
