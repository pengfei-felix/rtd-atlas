---
title: Rework 与 Q-time 后窗
---

# Etch Rework 与 Q-time 后窗

<div class="keyline" markdown>

**Etch 出片不是终点。** 很多风险发生在出片后：strip、clean、metrology 接不上；rework 回路突然涌入；原 chamber 约束把正常产能挤掉。

</div>

## Rework 建模

| 字段 | 用途 |
|---|---|
| rework_type | re-etch、strip 后重跑、工程确认 |
| original_chamber | 是否要求回原 chamber |
| reason_code | CD、profile、residue、endpoint |
| allowed_route | 可走哪些返工路径 |
| priority_policy | 是否继承原优先级 |
| capacity_reserve | 是否占用预留返工能力 |

Rework 不能简单当普通 Lot。它改变实际负荷，也改变质量风险。

## Q-time 后窗

Etch 后常见接续：

```text
Etch out → Strip / Ash → Clean → Metrology → Next process
```

| 风险 | 处理 |
|---|---|
| strip 不可接 | 暂缓 track-in 或改派其他 Lot |
| clean port 满 | 检查 downstream reservation |
| metrology 堵塞 | 避免集中释放关键层 |
| AMHS ETA 长 | 提前 pre-position 或换近端 Lot |

## 规则骨架

```text
if downstream_unavailable(projected_etch_out):
    penalize_or_filter(candidate)
if rework_requires_original_chamber:
    reserve_original_chamber_capacity()
if qtime_after_etch_risk_high:
    require_downstream_reservation()
```

## 指标

| 指标 | 说明 |
|---|---|
| etch-out wait | 出片后等待下游时长 |
| rework capacity share | rework 占用 chamber 产能比例 |
| original-chamber hit | 回原 chamber 命中率 |
| downstream reservation hit | 下游预约命中率 |
| normal-lot displacement | rework 对正常批等待的影响 |

## 避坑

- 不要只优化 Etch track-in；
- 不要把 rework priority 简单拉满；
- 不要忽略 rework 对原 chamber 的专属性；
- 不要把下游堵塞归因成 Etch 设备效率问题。

---

相关：[Q-time 走廊](../topics/qtime-corridor.md) · [AMHS/MCS 联动](../topics/amhs-mcs-coupling.md)
