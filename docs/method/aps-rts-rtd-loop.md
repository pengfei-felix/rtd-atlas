---
title: APS / RTS / RTD 闭环
---

# APS / RTS / RTD 闭环

<div class="keyline" markdown>

**先进 Fab 的调度不是一个大脑，而是一组不同时间尺度的闭环。** APS 看周/月，RTS 看小时/班次，RTD 看分钟/事件。三者混成一层，系统不是太慢，就是太短视。

</div>

## 三层职责

| 层级 | 时间尺度 | 决策对象 | 不该做什么 |
|---|---:|---|---|
| APS | 周 / 月 | 订单承诺、产能分配、投料节奏、瓶颈负荷 | 不直接决定下一批 Lot 上哪台机 |
| RTS | 小时 / 班次 | 短周期目标、局部预约、瓶颈节拍 | 不把过时计划强制压给现场 |
| RTD | 秒 / 分钟 / 事件 | 当前可执行派工、资源锁定、异常覆盖 | 不独自改变全厂 release 策略 |

三层都要有输出，也都要有边界。RTD 没有上层目标会变成局部贪心；APS 没有 RTD 反馈会变成纸面最优。

## 闭环数据流

<div class="flowline" markdown>

<div class="flow-step" markdown>
<strong>APS</strong><br>
产能 / 投料 / 目标
</div>

<div class="flow-arrow">→</div>

<div class="flow-step" markdown>
<strong>RTS</strong><br>
短周期预约 / 节拍
</div>

<div class="flow-arrow">→</div>

<div class="flow-step" markdown>
<strong>RTD</strong><br>
实时派工 / 覆盖
</div>

<div class="flow-arrow">→</div>

<div class="flow-step" markdown>
<strong>反馈</strong><br>
偏差 / 原因 / 能力
</div>

</div>

## RTS 到 RTD 的正确接口

RTS 输出不是“一串 Lot 顺序”，而是可被 RTD 消化的短周期约束包。

| 接口项 | 示例 | RTD 处理方式 |
|---|---|---|
| target move | 未来 4 小时某层 move 目标 | 做目标函数项，不做硬过滤 |
| reservation | scanner A 10:00-11:00 预留某产品族 | 若资源未到或 Q-time 冲突，可覆盖 |
| priority envelope | 产品族 A 高于 B，但 B 进入红区时可反超 | 转成动态权重 |
| load balance target | 控制某 tool group 负载差 | 作为 tie-breaker 或惩罚项 |
| exception threshold | 偏离 RTS 超过 5 分钟需记录原因 | 写 override log |

## RTD 覆盖 RTS 的原因码

| 原因码 | 触发条件 | 处理 |
|---|---|---|
| TOOL_DOWN | 计划设备不可用 | 重选设备，回传 effective capacity |
| RESOURCE_MISSING | Reticle / Boat / Pad / FOUP 未到位 | 重排或触发资源搬送 |
| QTIME_RISK | 按计划执行会进入黄/红区 | 提前派工或入口限流 |
| AMHS_DELAY | 搬送 ETA 超过可接受窗口 | 改派近端资源或延后 |
| QUALITY_INHIBIT | SPC/FDC/YMS 风险限制 | 硬过滤，等待工程确认 |
| SETUP_EXCESS | 计划造成异常 setup / clean 损失 | 改走同族或延后 |
| DOWNSTREAM_BLOCKED | 下游不可接收 | 暂缓，避免制造 WIP 波峰 |

覆盖不是“RTD 不听计划”，而是现场状态对上层计划的受控修正。关键是：**每次覆盖必须可解释、可统计、可回灌。**

## 滚动节奏

| 节奏 | 动作 | 输出 |
|---|---|---|
| 每事件 | RTD 根据最新状态派工 | dispatch decision + reason code |
| 每 5–15 分钟 | RTS 重算短周期预约 | updated reservation / priority envelope |
| 每班 | APS 校准投料和区域目标 | release plan / WIP cap |
| 每周 | 复盘 effective capacity 与规则表现 | capacity adjustment / rule change |

不同 Fab 的节奏可以不同，但原则不变：短周期只修正近期，长周期只修正结构，避免每层都抢同一个决策。

## 冲突处理

| 冲突 | 优先级 |
|---|---|
| 安全 / 污染 / 质量硬约束 vs 交期 | 硬约束优先 |
| Q-time 红区 vs setup 节省 | 红区优先 |
| 瓶颈饥饿 vs 普通区域平衡 | 瓶颈优先 |
| Hot Lot vs 大量普通批 CT | Hot Lot 有配额和影响面约束 |
| RTS 预约 vs 现场资源缺失 | 资源状态优先，记录覆盖 |

## 验收指标

| 指标 | 解释 |
|---|---|
| plan adherence | RTS 预约被执行的比例，不能越高越好，要结合覆盖原因看 |
| override rate | RTD 覆盖比例，过高说明上层计划或状态数据有问题 |
| override aging | 同一原因码持续多久未消除 |
| reservation hit rate | 预约资源按时到位并成功执行的比例 |
| rolling stability | 连续滚动计划变化幅度，过大说明系统抖动 |
| effective capacity error | APS 产能假设与现场实际差距 |

## 工程落地建议

1. 先定义三层输出物，不急着统一算法。
2. RTD 决策日志必须带 `source_plan_id`，否则无法追踪计划执行偏差。
3. 覆盖原因码要少而硬，避免写成自由文本。
4. RTS 预约需要过期时间，过期自动释放，不要变成长期锁死。
5. 每周按原因码复盘：哪些覆盖应修 APS，哪些应修规则，哪些应修数据源。

---

相关：[RTS 与 RTD 分层](rts-rtd.md) · [规则库设计](rule-library.md) · [数据模型](data-model.md)
