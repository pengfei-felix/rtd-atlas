---
title: 症状诊断手册
---

# 症状诊断手册

<div class="keyline" markdown>

**RTD 问题不能从规则开始查，要从现场症状开始查。** 机台空转、Q-time 超时、Hot Lot 插队、换线变多，这些症状背后可能是完全不同的约束。先归因，再改规则。

</div>

## 诊断总流程

<div class="flowline" markdown>

<div class="flow-step" markdown>
<strong>症状</strong><br>
现场看到什么
</div>
<div class="flow-arrow">→</div>
<div class="flow-step" markdown>
<strong>归因</strong><br>
资源 / 时间窗 / 计划 / 执行
</div>
<div class="flow-arrow">→</div>
<div class="flow-step" markdown>
<strong>字段</strong><br>
系统缺什么数据
</div>
<div class="flow-arrow">→</div>
<div class="flow-step" markdown>
<strong>规则</strong><br>
过滤 / 升权 / 预约 / 锁定
</div>
<div class="flow-arrow">→</div>
<div class="flow-step" markdown>
<strong>指标</strong><br>
怎么证明有效
</div>

</div>

如果跳过中间三步，直接“加一条规则”，规则库会越来越厚，问题不一定减少。

## 症状 1：机台空着，队列也有料

| 排查方向 | 判断问题 |
|---|---|
| 主资源 | 候选 Lot 是否真的符合 tool / chamber qualification？ |
| 辅助资源 | Reticle / Boat / Pad / gas 是否到位？ |
| 时间窗 | Lot 是否因为 Q-time / hold 不可派？ |
| 物流 | FOUP / Pod 是否在正确位置？AMHS 是否延迟？ |
| 规则 | 是否被某条 dedication / campaign / contamination 规则过滤？ |

常见根因：**系统只看 Lot 队列，不看辅助资源状态。**

建议动作：

- 给每次 idle 打 reason code；
- 区分 no-lot、no-resource、no-transport、no-qualification；
- 对 no-resource 类，接入位置、锁定、到位时间；
- 对 no-qualification 类，复核能力矩阵粒度是否过粗。

验证指标：white space by reason、resource-wait idle time、empty dispatch ratio。

## 症状 2：Q-time 总在红区才被发现

| 排查方向 | 判断问题 |
|---|---|
| 窗口启动 | 系统是否知道窗口什么时候开始？ |
| 剩余时间 | RTD 排序是否使用 remaining_time？ |
| 下游接续 | 派当前步前是否检查下一步可用性？ |
| 入口限流 | 是否太多 Lot 同时进入紧窗口？ |
| Hot Lot | 加急批是否挤掉已在窗口内的普通批？ |

常见根因：**Q-time 只做报警，没有进入派工模型。**

建议动作：

- 把 Q-time 状态分 green / yellow / red；
- 黄色阶段就预约下游资源；
- 入口处限制同一走廊内在窗 Lot 数；
- Hot Lot 插队前做影响面校验。

验证指标：yellow-zone dwell、red-zone rescue ratio、downstream reservation hit rate、Q-time violation by reason。

## 症状 3：Hot Lot 变多，普通批越来越慢

| 排查方向 | 判断问题 |
|---|---|
| 来源 | Hot Lot 是真实客户需求，还是普通批拖成 Hot？ |
| 配额 | 每班 / 每天 Hot Lot 是否有上限？ |
| 影响面 | 插队会不会让其他 Lot 进入黄/红区？ |
| 统计 | 是否分优先级统计 CT，而不是看总平均？ |

常见根因：**Hot Lot 收益来自普通批让路，但系统没有记录代价。**

建议动作：

- 建 Hot Lot 配额和升级审批；
- 分优先级统计 CT / OTD；
- 插队前检查被挤 Lot 的 Q-time 和 due risk；
- 对“普通批拖成 Hot”的链路做反向追踪。

验证指标：hot lot ratio、normal lot CT penalty、hot lot impact count。

## 症状 4：利用率提高，产出没提高

| 排查方向 | 判断问题 |
|---|---|
| Setup | 换线时间是否同步增加？ |
| 瓶颈 | 提高的是不是瓶颈资源利用率？ |
| 质量 | 返工 / rework 是否上升？ |
| WIP | WIP 是否堆到下游瓶颈？ |
| 批量 | 为了填空是否拆碎 batch / campaign？ |

常见根因：**提高的是表面 busy，不是有效产能。**

建议动作：

- 区分 run time、setup time、wait time、blocked time；
- 单独统计瓶颈资源有效产出；
- 把 setup loss 和 rework 放进约束指标；
- 非瓶颈区域不追求满载。

验证指标：effective output、setup loss、bottleneck utilization、good wafer throughput。

## 症状 5：规则越加越多，人工干预也越多

| 排查方向 | 判断问题 |
|---|---|
| 规则对象 | 每条规则是否有明确 scope？ |
| 冲突 | 是否有两条规则同时命中但动作相反？ |
| 下线 | 临时规则是否长期存在？ |
| 日志 | 人工 override 是否有原因码？ |
| 回放 | 新规则上线前是否跑过历史回放？ |

常见根因：**规则缺少生命周期管理。**

建议动作：

- 每条规则必须有 owner、metric、valid_until；
- 建规则命中日志和 override 日志；
- 做月度 rule audit；
- 对高冲突区域，逐步把硬优先级链改成打分模型。

验证指标：rule count、conflict count、manual override ratio、rule hit distribution。

## 症状 6：排程看起来可行，现场执行不了

| 排查方向 | 判断问题 |
|---|---|
| 资源位置 | 辅助资源是否在位？ |
| 搬送时间 | AMHS 时间是否进入计划？ |
| 锁定机制 | 计划生成后资源是否被别人抢走？ |
| RTS/RTD | RTD 是否有权覆盖失真计划？ |
| 日志 | 覆盖原因是否反馈给 RTS？ |

常见根因：**排程模型没有物理执行层，RTD 只是照单执行。**

建议动作：

- 排程输出必须带资源预约；
- RTD 覆盖 RTS 要有原因码；
- AMHS 搬送时间进入 resource ready 判断；
- RTS 滚动重排要吃进执行偏差。

验证指标：plan adherence、override ratio、resource reservation hit rate、execution failure count。

## 诊断表怎么用

1. 不要一次改多个规则；
2. 每次只锁定一个主要症状；
3. 先补字段，再加规则；
4. 先影子验证，再接管执行；
5. 每条改动都要能回放。

---

相关：[规则库设计](rule-library.md) · [数据模型](data-model.md) · [指标体系](metrics.md)
