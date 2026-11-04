---
title: 规则命中日志样例
---

# 规则命中日志样例

## 规则命中类型

| 类型 | 示例 | 处理 |
|---|---|---|
| FILTER | contamination violation | 直接剔除候选 |
| BOOST | Q-time yellow | 提高权重 |
| PENALIZE | setup cost high | 降低权重 |
| RESERVE | downstream clean slot | 锁定未来资源 |
| OVERRIDE | tool down overrides RTS | 覆盖上层计划 |
| FALLBACK | solver timeout | 回退规则 |

## 样例表

| decision_id | rule_id | hit_type | target | effect | reason |
|---|---|---|---|---|---|
| D-00018 | R-QT-020 | BOOST | L20261104-001 | +35 | Q-time yellow |
| D-00018 | R-CH-011 | FILTER | L20261104-014 | reject | chamber not qualified |
| D-00018 | R-DS-004 | FILTER | L20261104-022 | reject | downstream blocked |
| D-00018 | R-ST-008 | PENALIZE | L20261104-045 | -12 | recipe switch cost high |
| D-00018 | R-AMHS-003 | BOOST | L20261104-001 | +5 | ETA within threshold |

## JSON 表达

```json
{
  "decision_id": "D-20261104-ETCH-00018",
  "rule_version": "rtd-rule-2.0.3",
  "hits": [
    {
      "rule_id": "R-QT-020",
      "rule_name": "qtime_yellow_boost",
      "hit_type": "BOOST",
      "target": "L20261104-001",
      "effect": 35,
      "reason": "Q-time remaining 85 min, threshold 120 min"
    },
    {
      "rule_id": "R-CH-011",
      "rule_name": "chamber_qualification_filter",
      "hit_type": "FILTER",
      "target": "L20261104-014",
      "effect": "REJECT",
      "reason": "ETCH_01_CHB not qualified for RCP_ET_M2_B"
    }
  ]
}
```

## 复盘看什么

| 指标 | 含义 |
|---|---|
| rule_hit_count | 规则是否长期过度命中 |
| filter_loss | 候选集被过滤到过小的比例 |
| boost_dominance | 某条升权规则是否长期压过其他目标 |
| override_by_rule | 哪些规则经常导致覆盖 RTS |
| expired_rule_hit | 临时规则是否忘记下线 |

规则库成熟度不看规则数量，而看规则是否可解释、可统计、可下线。
