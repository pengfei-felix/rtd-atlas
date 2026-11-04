---
title: 系统架构与接口
---

# 系统架构与接口

<div class="keyline" markdown>

**RTD 的准确性由状态源决定。** 算法再好，如果 MES、EAP、AMHS、YMS、SPC 的状态延迟或冲突，派工结果就是纸面可行、现场不可执行。

</div>

## 典型系统边界

```text
APS / RTS ──目标、预约、WIP cap──▶ RTD
MES ───────Lot、Route、Hold、Q-time──▶ RTD
EAP ───────Tool / Chamber / Alarm / PM──▶ RTD
AMHS/MCS ──FOUP、Stocker、Move、ETA──▶ RTD
YMS/SPC/FDC ──质量风险、OOC、限制──▶ RTD
RTD ───────Dispatch recommendation / reason──▶ MES / Operator
RTD ───────Move request / pre-position──▶ AMHS/MCS
RTD ───────Decision log / capacity feedback──▶ APS / Data Platform
```

## 谁是状态源

| 状态 | 主源 | RTD 用途 | 风险 |
|---|---|---|---|
| Lot 当前工步 | MES | 候选集构建 | route 状态延迟会误派 |
| Hold / Release | MES / 工程系统 | 硬过滤 | hold 未同步会造成违规推荐 |
| Tool 状态 | EAP / MES | 可用性判断 | MES 状态滞后于 EAP |
| Chamber 状态 | EAP / FDC / 工程表 | chamber 级派工 | 只接 tool 状态会失真 |
| Q-time 窗口 | MES / RTD 计算 | 风险升权 / 限流 | 窗口起点定义不统一 |
| FOUP 位置 | AMHS/MCS | 搬送可行性 | stocker/port 状态不一致 |
| Reticle / Pod | RMS / AMHS / MES | Litho 资源同步 | 位置、锁定、搬送状态割裂 |
| SPC/FDC 风险 | SPC/FDC/YMS | 质量硬过滤 | 反馈滞后导致风险批继续跑 |
| 计划目标 | APS/RTS | 派工权重和预约 | 计划过期仍被执行 |

## 接口延迟分级

| 数据 | 建议延迟等级 | 说明 |
|---|---|---|
| Tool / chamber down | 秒级 | 延迟会直接造成不可执行派工 |
| Lot track-in/out | 秒到分钟级 | 决定队列和 Q-time 起点 |
| AMHS ETA | 分钟级滚动 | ETA 变化要能触发重算 |
| SPC/FDC inhibit | 秒到分钟级 | 质量硬约束，宁可保守 |
| APS target | 小时 / 班次级 | 不需要秒级，但要有版本和有效期 |
| 指标汇总 | 班次 / 日级 | 用于复盘，不用于单次派工 |

## RTD 决策服务结构

| 模块 | 职责 |
|---|---|
| State Builder | 汇总 MES/EAP/AMHS/YMS 状态，构造一致视图 |
| Candidate Generator | 按区域、工步、设备能力生成候选 Lot / Resource |
| Hard Filter | 过滤 hold、qualification、contamination、inhibit |
| Scoring Engine | 计算 Q-time、priority、setup、downstream、AMHS 等分数 |
| Optimizer Adapter | 调用局部 CP/MILP/CP-SAT 或 rolling horizon 模块 |
| Fallback Rule | 求解失败或状态缺失时兜底 |
| Explanation Builder | 输出过滤原因、加权项、最终 reason code |
| Decision Logger | 写入决策、候选、状态版本、规则版本 |

## 状态冲突处理

| 冲突 | 推荐处理 |
|---|---|
| MES 显示 available，EAP 显示 down | 以 EAP 设备实时状态为准，MES 状态标记滞后 |
| AMHS 显示 FOUP 在途，MES 显示仍在 stocker | 以 AMHS 位置和 ETA 为准，禁止重复搬送 |
| SPC 已 inhibit，MES hold 未下发 | 以质量风险为硬约束，生成待同步告警 |
| APS 预约资源，RTD 发现资源不可用 | RTD 覆盖并回传 RESOURCE_MISSING |
| 工程表限制与系统资格冲突 | 保守过滤，要求数据治理闭环 |

RTD 不能在冲突状态下静默选择一个来源。每次冲突都应有来源、时间戳和处理结果。

## 写回与回滚

RTD 输出不应该只是一条“推荐”。至少包括：

| 字段 | 说明 |
|---|---|
| decision_id | 唯一决策 ID |
| selected_lot / tool / resource | 推荐对象 |
| valid_until | 推荐有效期，过期需重算 |
| reason_code | 主原因 |
| filter_summary | 被过滤候选及原因 |
| required_moves | 需要 AMHS 执行的搬送 |
| lock_scope | 锁定哪些资源，锁多久 |
| rollback_policy | 失败时释放哪些锁、重算哪些队列 |

常见失败场景：资源锁定后 AMHS 搬送失败、Lot 被 hold、设备突发 down、下游突然不可接。每个场景都要能释放锁并重算。

## 人工 Override

人工改派不是系统失败，而是重要训练信号。

| 字段 | 用途 |
|---|---|
| original_decision_id | 找到被覆盖的系统推荐 |
| override_actor | 操作来源，区分工程、生产、设备 |
| override_reason | 标准原因码，不写自由文本为主 |
| actual_dispatch | 实际执行结果 |
| post_result | 后续是否成功、是否出问题 |

人工 override 长期集中在某类原因，说明规则、数据源或接口边界需要修。

## 架构落地顺序

1. 先统一状态视图：Lot、Tool/Chamber、Resource、Q-time、AMHS 位置。
2. 再做硬过滤：把不可违反的约束系统化。
3. 然后接派工解释日志：没有日志不要谈优化。
4. 再接 AMHS pre-position 和资源锁定。
5. 最后接局部求解器和仿真回放闭环。

---

相关：[数据模型](data-model.md) · [APS / RTS / RTD 闭环](aps-rts-rtd-loop.md) · [AMHS/MCS 联动](../topics/amhs-mcs-coupling.md)
