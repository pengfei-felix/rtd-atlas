---
title: 派工决策日志样例
---

# 派工决策日志样例

<div class="keyline" markdown>

**派工日志的目标不是证明系统选了谁，而是解释为什么选它、为什么没选别人。** 没有候选集、过滤原因和规则版本，决策无法复盘。

</div>

## 一次 Etch 派工样例

| 字段 | 示例 |
|---|---|
| decision_id | D-20261104-ETCH-00018 |
| timestamp | 2026-11-04 11:42:10 |
| trigger | CHAMBER_AVAILABLE |
| area | ETCH |
| tool_id | ETCH_01 |
| chamber_id | ETCH_01_CHB |
| selected_lot | L20261104-001 |
| selected_recipe | RCP_ET_M1_A |
| reason_code | MIN_QTIME_RISK_WITH_MATCHING |
| rule_version | rtd-rule-2.0.3 |
| state_snapshot_id | S-20261104-114210 |

## 候选集解释

| Lot | 处理 | 原因 |
|---|---|---|
| L20261104-001 | selected | Q-time yellow，chamber matching 命中，下游 clean 已预约 |
| L20261104-014 | filtered | CHAMBER_NOT_QUALIFIED |
| L20261104-022 | filtered | DOWNSTREAM_BLOCKED |
| L20261104-031 | scored lower | Normal priority，Q-time green，ETA 较长 |
| L20261104-045 | scored lower | recipe switch cost 高 |

## JSON 表达

```json
{
  "decision_id": "D-20261104-ETCH-00018",
  "timestamp": "2026-11-04T11:42:10+08:00",
  "trigger": "CHAMBER_AVAILABLE",
  "context": {
    "area": "ETCH",
    "tool_id": "ETCH_01",
    "chamber_id": "ETCH_01_CHB",
    "state_snapshot_id": "S-20261104-114210",
    "rule_version": "rtd-rule-2.0.3"
  },
  "selected": {
    "lot_id": "L20261104-001",
    "recipe": "RCP_ET_M1_A",
    "reason_code": "MIN_QTIME_RISK_WITH_MATCHING"
  },
  "candidate_summary": [
    {
      "lot_id": "L20261104-001",
      "result": "SELECTED",
      "score": 92.4,
      "reason": ["QTIME_YELLOW", "MATCHING_HIT", "DOWNSTREAM_RESERVED"]
    },
    {
      "lot_id": "L20261104-014",
      "result": "FILTERED",
      "reason": ["CHAMBER_NOT_QUALIFIED"]
    },
    {
      "lot_id": "L20261104-022",
      "result": "FILTERED",
      "reason": ["DOWNSTREAM_BLOCKED"]
    }
  ],
  "required_actions": [
    {
      "type": "MOVE_REQUEST",
      "target": "ETCH_01_PORT_2",
      "valid_until": "2026-11-04T11:55:00+08:00"
    }
  ]
}
```

## 必须保留的复盘字段

| 字段 | 没有它会怎样 |
|---|---|
| candidate_set | 只能知道选了谁，不知道漏了谁 |
| filtered_by | 无法判断硬约束是否过严 |
| score_breakdown | 权重失控时查不出来 |
| state_snapshot_id | 状态变了以后无法复现当时决策 |
| rule_version | 规则改版后无法比较前后效果 |
| valid_until | 推荐过期后仍被执行 |
| required_actions | 无法追踪 AMHS 搬送和资源锁定 |
