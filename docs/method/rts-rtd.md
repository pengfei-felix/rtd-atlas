---
title: RTS 与 RTD 分层
---

# RTS 与 RTD 分层

<div class="keyline" markdown>

**RTS 管节奏，RTD 管现场。** RTS 给未来几十分钟到几小时的计划框架，RTD 在秒级到分钟级处理真实状态。RTD 不能只是 RTS 的执行器，否则现场一变，系统就会执行一张已经失真的计划。

</div>

## 核心原则

调度（RTS，分钟级到小时级计划）和派工（RTD，秒级到分钟级决策）必须分层，而且 **RTD 要有条件地覆盖 RTS**。

| 层级 | 时间尺度 | 典型问题 | 输出 |
|---|---|---|---|
| APS | 天 / 周 | 投多少、优先满足谁、瓶颈产能怎么分 | release plan、capacity plan |
| RTS | 分钟 / 小时 | 未来一段时间机台/资源怎么预排 | short-term schedule、reservation |
| RTD | 秒 / 分钟 | 当前空闲资源该接哪个 Lot | dispatch decision |
| MES / AMHS | 实时执行 | 状态记录、搬送执行、track-in/out | execution event |

分层的意义不是多一个系统名，而是让每层只解决自己能解决的问题。

## 为什么 RTD 不能完全服从 RTS

RTS 计划生成后，现场可能立刻变化：

- 设备宕机；
- chamber 从 production 变成 warning；
- Reticle / Boat / Pad 不在位；
- Q-time Lot 提前进入黄区；
- AMHS 拥堵导致搬送延迟；
- Hot Lot 临时释放；
- 上游 WIP 到达偏离预测。

如果 RTD 仍机械执行旧计划，系统看起来“按计划”，现场实际在等、堵、超窗。

## 覆盖边界

RTD 可以覆盖 RTS，但不能随便覆盖。必须有清晰原因码。

| 覆盖原因 | 例子 | 处理 |
|---|---|---|
| RESOURCE_NOT_READY | Reticle / Boat / Pad 未到位 | 换候选或延后 |
| TOOL_STATE_CHANGED | 机台/chamber 状态变化 | 重新过滤候选 |
| QTIME_RISK | Lot 进入黄/红区 | 提权或抢救 |
| DOWNSTREAM_BLOCKED | 下游不可接 | 暂缓放行 |
| AMHS_DELAY | 搬送时间超过计划余量 | 重新估算到位时间 |
| HOT_LOT_RELEASED | 急件释放 | 按影响面评估插入 |
| SAFETY_OR_PROCESS | 污染/工艺风险 | 硬禁止 |

覆盖必须留痕，否则 RTS 和 RTD 会互相甩锅。

## 偏离阈值

公开仿真研究给出的量级是：RTD 完全服从 RTS 时，吞吐可能大幅下降；给 RTD 留几分钟级别的偏离容忍带，通常更稳。这个数字不能照抄，但方向明确：**偏离阈值要标定，不要拍脑袋。**

| 阈值过小 | 阈值过大 |
|---|---|
| RTD 频繁推翻 RTS，计划失去约束 | RTD 变成执行器，异常响应太慢 |
| 系统震荡，规则互相打架 | 设备等料、Q-time 红区才动作 |
| 适合高度不稳定但要谨慎 | 适合稳定期但抗扰动差 |

## 阈值标定方法

1. 取历史事件流：设备状态、Lot 到达、资源位置、Q-time、AMHS 事件。
2. 固定 RTS 计划，模拟不同偏离阈值下 RTD 覆盖次数。
3. 比较主指标和约束指标：throughput、CT、Q-time、setup、move。
4. 找到“收益不再明显增加，但覆盖次数开始暴涨”的拐点。
5. 分区设置阈值，不要全厂一个值。

Litho、Etch、Diffusion 的阈值不会一样。光刻受辅助资源影响大，扩散受长炉程影响大，不能共用一个参数。

## 覆盖日志字段

| 字段 | 说明 |
|---|---|
| plan_id | 被覆盖的 RTS 计划 |
| timestamp | 覆盖发生时间 |
| area / tool / resource | 影响范围 |
| original_lot | RTS 原计划 Lot |
| selected_lot | RTD 实际选择 Lot |
| reason_code | 覆盖原因 |
| threshold_value | 当时阈值 |
| expected_delta | 预计影响，如等待减少 / Q-time 风险下降 |
| actual_outcome | 实际结果 |
| reviewed_by | 是否人工复盘 |

日志不是为了追责，是为了标定阈值和改进 RTS。

## RTS 如何吸收偏差

RTD 覆盖之后，RTS 不能装作没发生。滚动重排时必须吃进去：

- 已经加工的 Lot；
- 已经锁定的辅助资源；
- 已经改变的 Q-time 状态；
- 已经发生的 AMHS 延迟；
- 计划和实际的偏差原因。

否则下一轮 RTS 会继续基于旧世界排程，偏差越滚越大。

## 接口边界

| 接口 | 从谁到谁 | 内容 |
|---|---|---|
| Plan feed | RTS → RTD | 计划顺序、时间窗、资源预约 |
| State feed | MES/EAP/AMHS → RTD | Lot、tool、resource、搬送状态 |
| Override log | RTD → RTS | 覆盖原因、实际派工、影响范围 |
| Execution event | MES/AMHS → RTS | track-in/out、move complete、hold/release |
| Replan trigger | RTD / MES → RTS | 偏差超过阈值时触发滚动重排 |

## 反模式

1. **RTD 只执行计划。** 现场异常时无法响应。
2. **RTD 任意覆盖计划。** RTS 失去意义，系统震荡。
3. **覆盖不留痕。** 没法判断阈值是否合理。
4. **RTS 不吃执行偏差。** 计划与现场越走越远。
5. **全厂统一阈值。** 不同区域时间尺度不同，统一阈值必然失真。

---

相关：[规则库设计](rule-library.md) · [数据模型](data-model.md) · [Q-time 走廊](../topics/qtime-corridor.md)
