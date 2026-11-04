---
title: 数据模型
---

# 数据模型

<div class="keyline" markdown>

**RTD 的专业度，最后会落到数据模型上。** 页面里讲的约束、规则、Q-time、辅助资源，如果没有对应字段，就只能靠人工判断，不能被系统稳定执行。

</div>

## 模型边界

这不是数据库设计说明，而是 RTD 建模时最少要认清的对象集合。

| 对象 | 作用 |
|---|---|
| Lot | 被派工对象 |
| Step | 工艺路线上的当前节点 |
| Tool / Chamber | 主加工资源 |
| Auxiliary Resource | Reticle、Pod、Boat、Pad 等辅助资源 |
| Q-time Window | 时间窗约束 |
| Rule | 派工规则 |
| Decision | 一次派工决策 |
| Event | 现场执行事件 |

## Lot 模型

| 字段 | 说明 |
|---|---|
| lot_id | 批次号 |
| product_id | 产品 |
| route_id / step_id | 当前路线与工步 |
| layer / operation | 层别或工序名 |
| priority_class | normal / hot / super hot 等 |
| due_date | 交付时间 |
| release_time | 投料时间 |
| current_location | 当前物理位置 |
| hold_state | 是否 hold |
| qtime_state | green / yellow / red / violated |
| rework_flag | 是否重工 |
| dedication_requirement | 是否要求固定机台 / chamber |

Lot 不是一行队列记录。它要带路径位置、时间风险和资源需求。

## Tool / Chamber 模型

| 字段 | 说明 |
|---|---|
| tool_id | 设备号 |
| chamber_id | 子资源号，没有 chamber 的设备可为空 |
| area | Litho / Etch / Diffusion / Implant / CMP |
| state | up / down / pm / idle / processing / warning |
| available_time | 预计可用时间 |
| qualified_recipe | 可跑 recipe 集合 |
| matching_group | matching 分组 |
| dedication_group | 专属性分组 |
| setup_state | 当前 recipe / family / temperature / species |
| pm_state | PM / clean / seasoning 状态 |
| capacity | 批量或并行能力 |

Etch 这类区域必须到 chamber 级；Diffusion 必须到 tube；CMP 至少要能识别 platen/head 相关状态。

## 辅助资源模型

| 字段 | 说明 |
|---|---|
| resource_id | 资源编号 |
| resource_type | reticle / pod / boat / pad / gas / slurry |
| compatible_scope | 可服务产品、layer、recipe 或 family |
| location | 当前位置 |
| transfer_time | 到目标点预计搬送时间 |
| lock_owner | 当前锁定对象 |
| release_time | 预计释放时间 |
| remaining_life | 剩余寿命或剩余次数 |
| warning_limit / inhibit_limit | 预警与禁止阈值 |
| clean_state / inspection_state | 清洁或检查状态 |
| group_id | Pod、Boat 这类成组资源的组号 |

辅助资源模型的关键是：位置、锁定、寿命、到位时间。少一个，派工都会偏乐观。

## Q-time 模型

| 字段 | 说明 |
|---|---|
| window_id | 时间窗编号 |
| lot_id | 对应 Lot |
| start_event | 窗口启动事件 |
| end_event | 窗口结束事件 |
| start_time | 实际启动时间 |
| due_time | 截止时间 |
| remaining_time | 剩余时间 |
| severity | green / yellow / red / violated |
| downstream_step | 窗口结束对应工步 |
| recovery_action | 可采取动作：rush / hold / rework |

Q-time 如果只存在 MES 报警里，RTD 就只能救火；进入模型后，RTD 才能提前排序。

## Rule 模型

| 字段 | 说明 |
|---|---|
| rule_id | 规则编号 |
| scope | 作用范围 |
| object_type | lot / tool / chamber / resource / qtime |
| condition | 触发条件 |
| action_type | filter / boost / penalty / reserve / alert |
| priority | 冲突时优先级 |
| owner | 业务 owner |
| valid_from / valid_until | 有效期 |
| metric | 验证指标 |
| rollback_plan | 回滚方式 |

Rule 模型不是为了好看，是为了支持冲突检测、回放和下线。

## Decision 模型

一次派工决策必须能复盘。

| 字段 | 说明 |
|---|---|
| decision_id | 决策编号 |
| timestamp | 决策时间 |
| area / tool / chamber | 决策资源 |
| candidate_lots | 候选 Lot 集合 |
| candidate_resources | 候选辅助资源 |
| filtered_candidates | 被过滤对象与原因 |
| score_breakdown | 各项评分 |
| selected_lot | 选中的 Lot |
| selected_resource | 同步锁定的辅助资源 |
| reason_code | 最终原因码 |
| rts_plan_id | 对应 RTS 计划 |
| override_flag | 是否覆盖 RTS |
| operator_override | 是否被人工覆盖 |

没有 Decision 日志，就不能解释“为什么选它”。也不能用历史回放改进规则。

## Event 模型

| 事件 | 来源 | 用途 |
|---|---|---|
| lot_arrive | MES / AMHS | 更新候选队列 |
| track_in / track_out | MES / EAP | 更新加工状态 |
| tool_state_change | EAP | 更新资源状态 |
| resource_move_start / complete | AMHS | 更新辅助资源位置 |
| qtime_start / end | MES | 更新时间窗 |
| hold / release | MES | 更新 lot 可派状态 |
| pm_start / end | EAP / MES | 更新可用产能 |
| measurement_result | YMS / metrology | 更新反馈与 rework 风险 |

RTD 的事件流要按时间顺序可重放。不能重放，就不能做可靠回放验证。

## 最小可行数据集

如果第一版只能接有限数据，优先级如下：

1. Lot 当前 step、priority、location、hold、qtime；
2. Tool / chamber state、qualification、available_time；
3. 辅助资源位置、锁定、到位时间；
4. setup state / family；
5. track-in/out 事件流；
6. decision log；
7. measurement / rework 反馈。

没有前 3 项，不建议谈优化算法。没有第 6 项，不建议上线自动派工。

---

相关：[约束模型](constraints.md) · [规则库设计](rule-library.md) · [RTS 与 RTD 分层](rts-rtd.md)
