---
title: Boat 与出炉波峰
---

# Boat 与出炉波峰

<div class="keyline" markdown>

**炉管空不等于可开炉，出炉完成不等于风险结束。** Boat / Quartzware 是开炉前的隐性产能；出炉波峰是开炉后几个小时才暴露的下游风险。

</div>

## Boat / Quartzware 建模

| 字段 | 用途 |
|---|---|
| resource_id | 唯一标识 |
| compatible_recipe_family | 可支持哪些 family |
| contamination_group | 污染隔离 |
| clean_state | clean / dirty / pending clean |
| remaining_life | 剩余寿命或使用次数 |
| current_location | 炉旁、清洗、库存、搬送中 |
| lock_owner | 已被哪个 batch 预约 |
| valid_until | 锁定有效期 |

Boat 不是“附属品”，而是 batch feasibility 的组成部分。

## 锁定策略

| 策略 | 适用 | 风险 |
|---|---|---|
| 开炉前即时锁定 | 简单场景 | 找不到 Boat 导致炉管空等 |
| 黄色窗口提前锁定 | Q-time 压力高 | 过早锁死资源 |
| Campaign 级锁定 | 同 family 连续跑 | 影响其他产品族 |
| 动态释放 | 预约过期自动释放 | 需要可靠事件触发 |

建议：所有 Boat 预约都必须有 `valid_until`，避免资源被“僵尸锁”长期占用。

## 出炉波峰

同一批炉管若集中出炉，会把下游 clean、metrology、AMHS port 打穿。

| 波峰来源 | 表现 |
|---|---|
| 同步开炉 | 几小时后同步出炉 |
| 同 recipe campaign | 同类 Lot 同时涌向下游 |
| Q-time 救火 | 多个黄区/红区批次集中释放 |
| PM 后集中恢复 | 维护后设备群同时上线 |

## 出炉前预测

开炉前至少计算：

```text
projected_out_time = start_time + process_time + unload_time
projected_downstream_load(projected_out_time ± horizon)
AMHS_available(projected_out_time)
post_furnace_qtime_remaining
```

如果预计出炉时下游不可接，当前开炉可能只是把问题延后几个小时。

## 深度案例

| 案例 | 根因 | 解法 |
|---|---|---|
| 炉管利用率高但下游爆 | 只优化入口，不看出炉波峰 | 开炉前预测 downstream load |
| Boat 清洗队列卡住 | Boat clean 未纳入资源模型 | Boat clean 当作资源工序管理 |
| 高风险 recipe 找不到合规 Boat | 污染族和寿命未提前锁 | yellow 阶段提前预约 |
| 出炉后 AMHS 堵塞 | 搬送系统未参与计划 | 出炉窗口与 AMHS capacity 联动 |

## 指标

| 指标 | 说明 |
|---|---|
| boat wait time | 因 Boat 不到位导致炉管等待 |
| boat lock miss | 预约 Boat 未能按时到位 |
| downstream burst index | 出炉后单位时间进入下游的 Lot 数 |
| post-furnace wait | 出炉后等待下游的时间 |
| zombie lock count | 过期未释放的资源锁 |

---

相关：[AMHS/MCS 联动](../topics/amhs-mcs-coupling.md) · [辅助资源流转](../topics/support-resource.md)
