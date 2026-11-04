---
title: PM / Clean / Seasoning
---

# Etch PM / Clean / Seasoning

<div class="keyline" markdown>

**维护结束不是产能恢复。** Etch chamber 从 PM / clean 回到稳定生产，中间还有 seasoning、确认和漂移观察。RTD 如果只看 available，会把风险批派进恢复区。

</div>

## 状态机

```text
DOWN / PM
  → POST_PM
  → SEASONING
  → QUAL_CHECK
  → PRODUCTION
  → DRIFT_WARNING
  → INHIBIT / CLEAN
```

| 状态 | 派工策略 |
|---|---|
| POST_PM | 禁止 critical layer，允许 dummy / seasoning |
| SEASONING | 低风险 Lot 或 dummy，限制高价值批 |
| QUAL_CHECK | 等量测 / 工程确认，不做自动派工 |
| PRODUCTION | 正常候选 |
| DRIFT_WARNING | 降权，限制关键层 |
| INHIBIT | 硬过滤 |

## 为什么不能只看 EAP available

EAP available 只说明设备控制层允许动作，不说明工艺状态已恢复。RTD 需要把 EAP 状态、FDC/SPC、工程确认和 seasoning 进度合成一个 `dispatch_state`。

## Seasoning 规则

| 规则 | 说明 |
|---|---|
| seasoning_count | 需要跑几片/几批后恢复 |
| allowed_lot_class | 哪类 Lot 可用于 seasoning |
| critical_block | critical layer 禁止进入恢复期 |
| reset_condition | 若 OOC 或 alarm，状态回退 |
| expiry | 长时间未跑可能重新进入不稳定 |

## 典型问题

| 问题 | 根因 | 解法 |
|---|---|---|
| PM 后第一批 CD 漂 | seasoning 状态不可见 | POST_PM / SEASONING 状态进 RTD |
| Chamber 长期 yellow | 工程限制未闭环 | yellow aging 看板，超时升级 |
| Clean 后 campaign 被打断 | seasoning 与 campaign 未联动 | seasoning 完成后优先安排同 family 稳定 run |
| FDC warning 被忽略 | 质量系统未参与派工 | FDC warning 降权或 inhibit |

## 验证指标

- post-PM critical lot count；
- first-lot OOC rate；
- seasoning completion time；
- drift warning dispatch count；
- chamber state aging。

---

相关：[系统架构与接口](../method/system-architecture.md) · [指标体系](../method/metrics.md)
