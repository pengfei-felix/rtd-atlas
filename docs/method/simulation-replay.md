---
title: 仿真与历史回放
---

# 仿真与历史回放

<div class="keyline" markdown>

**RTD 规则不能直接上线赌现场。** 可靠路径是历史事件回放、影子模式、局部接管、扩区上线。没有回放体系，规则调整靠经验；有回放体系，规则调整才有证据。

</div>

## 四级验证链

| 阶段 | 目的 | 输出 |
|---|---|---|
| 历史事件回放 | 用历史状态复现当时现场，比较新旧规则 | counterfactual decision |
| 离散事件仿真 | 在可控假设下测试策略边界 | throughput / CT / WIP 曲线 |
| 影子模式 | 在线接实时数据，但不下发执行 | recommendation vs actual |
| 局部接管 | 低风险区域或低风险产品先执行 | controlled rollout |

回放回答“如果当时用新规则会怎样”；仿真回答“在假设条件变化时策略是否稳”；影子模式回答“实时数据质量和接口能否支撑”。

## 事件流模型

最小事件集：

| 事件 | 必要字段 |
|---|---|
| lot_arrive | lot_id、step、area、arrival_time、priority、qtime_state |
| track_in | lot_id、tool_id、chamber_id、resource_id、recipe、timestamp |
| track_out | lot_id、tool_id、chamber_id、timestamp、result_state |
| tool_state_change | tool/chamber、old_state、new_state、reason、timestamp |
| resource_move | resource_id、from、to、eta、actual_arrival |
| hold_release | lot_id、hold_code、release_time、owner |
| metrology_result | lot_id、metric、value、spec_state、timestamp |
| dispatch_decision | candidate_set、selected、filtered_by、reason_code |

事件流必须按时间排序，且状态更新必须可重建。否则回放会变成“拿结果倒推原因”。

## 回放引擎怎么跑

```text
for event in historical_events_by_time:
  1. 更新仿真状态：Lot / Tool / Chamber / Resource / Q-time
  2. 若触发派工点：调用新规则生成推荐
  3. 记录推荐与历史实际派工的差异
  4. 推进加工、搬送、等待、下游接续状态
  5. 统计指标并标记不可复现段
```

派工点不一定只在设备空闲时触发，也包括 Q-time 进入黄区、资源到位、上层计划更新、AMHS ETA 变化。

## 新旧规则对比

| 对比项 | 说明 |
|---|---|
| selected_lot_diff | 新规则是否选了不同 Lot |
| feasible_after_dispatch | 推荐派工在当时是否真的可执行 |
| qtime_projection | 推荐后未来窗口风险是否下降 |
| setup_delta | setup / clean / tune 损失变化 |
| downstream_delta | 下游饥饿或堵塞变化 |
| rule_hit_diff | 哪些规则命中变化最大 |
| reason_explainability | 是否能解释为什么不同 |

不要只看最终吞吐。很多规则短期吞吐看起来好，但靠牺牲 Q-time、质量风险或普通批 CT 换来。

## 仿真边界

| 可以较好仿真 | 容易失真 |
|---|---|
| 队列等待、设备可用率、setup、搬送 ETA 分布 | 人工临时决策、工程师经验放行、数据延迟 |
| WIP cap、release 节奏、batch 规则 | 质量风险、工艺漂移、未记录的隐性约束 |
| Q-time 走廊容量、下游预约 | 现场异常响应速度、跨部门沟通延迟 |

仿真不是为了“证明一定提升”，而是为了提前发现策略边界和风险。

## 影子模式

影子模式要求系统在线接实时数据，生成推荐，但不下发执行。

| 检查项 | 合格标准 |
|---|---|
| 数据时延 | 决策前关键状态已到达，延迟有统计口径 |
| 推荐可执行率 | 推荐没有被现场硬约束立即否决 |
| 人工差异原因 | 与人工实际派工差异能归因 |
| 状态一致性 | MES / EAP / AMHS 状态冲突可识别 |
| 解释完整性 | 每次推荐有过滤、加权、最终原因 |

影子模式不要只跑 1–2 天，至少覆盖 PM、异常、Hot Lot、换线、周末低人力等典型场景。

## 数据缺失处理

| 缺失 | 处理 |
|---|---|
| chamber 级 track_in/out 缺失 | 降级为 tool 级仿真，并标记结论不可用于 chamber 规则 |
| AMHS ETA 缺失 | 用历史分布抽样，不得写成固定搬送时间 |
| 人工 override 缺失 | 将异常派工标记为 unknown override，不纳入规则优劣结论 |
| 辅助资源状态缺失 | 回放只能验证主资源规则，不能验证资源同步策略 |
| 量测结果滞后 | 反馈类规则需要按实际可见时间回放，不能提前看未来 |

## 上线门槛

| 门槛 | 建议 |
|---|---|
| 推荐可执行率 | 先看硬约束失败率，再看收益 |
| Q-time 风险 | 不允许靠增加红区次数换吞吐 |
| 质量风险 | SPC / FDC / YMS 限制必须硬过滤 |
| 人工 override | 影子推荐被人工频繁否决时，不上线 |
| 指标稳定性 | 至少按班次、产品族、区域分层看 |

## 最小落地路线

1. 先建一周事件流，覆盖 Lot、Tool、Resource、Q-time、AMHS 关键状态。
2. 选一个低风险区域做历史回放，比如非关键层 Etch 或部分 Diffusion recipe family。
3. 生成新旧规则差异报告，不直接用总吞吐下结论。
4. 在线影子模式跑 2–4 周，记录推荐与人工实际差异。
5. 只接管一类决策，例如 Q-time 黄区升权或同 family campaign 边界。
6. 上线后继续保留回放，把每次规则变更变成可复现实验。

---

相关：[数据模型](data-model.md) · [指标体系](metrics.md) · [症状诊断手册](symptom-playbook.md)
