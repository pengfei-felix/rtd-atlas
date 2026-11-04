---
title: Lot 状态样例
---

# Lot 状态样例

## 样例表

| 字段 | 示例 | 说明 |
|---|---|---|
| lot_id | L20261104-001 | Lot 唯一 ID |
| product | P_LOGIC_A | 产品族，不写真实产品名 |
| route_step | ETCH_M1_120 | 当前工步 |
| area | ETCH | 当前区域 |
| layer | M1 | 层别 / 工艺段 |
| priority_class | NORMAL | NORMAL / HOT / SUPER_HOT |
| due_time | 2026-11-04 18:00 | 当前承诺节点，不一定是最终交期 |
| qtime_state | YELLOW | GREEN / YELLOW / RED / EXPIRED |
| qtime_remaining_min | 85 | 到窗口结束的剩余分钟 |
| current_location | STK_E12 | 当前 stocker / 设备 / buffer |
| target_tool_group | ETCH_GRP_01 | 可加工设备组 |
| required_recipe | RCP_ET_M1_A | recipe 或 recipe family |
| required_resource | CH_MATCH_G1 | chamber matching / 辅助资源 |
| hold_state | RELEASED | HOLD / RELEASED |
| last_process_end | 2026-11-04 10:20 | 用于窗口计算和回放 |
| downstream_step | CLEAN_M1_010 | 下游接续工步 |
| downstream_reservation | CLEAN_02@12:10 | 下游预约，可为空 |

## JSON 表达

```json
{
  "lot_id": "L20261104-001",
  "product": "P_LOGIC_A",
  "route_step": "ETCH_M1_120",
  "area": "ETCH",
  "layer": "M1",
  "priority_class": "NORMAL",
  "due_time": "2026-11-04T18:00:00+08:00",
  "qtime": {
    "state": "YELLOW",
    "remaining_min": 85,
    "window_id": "QT_CLEAN_TO_ETCH_M1"
  },
  "location": {
    "current": "STK_E12",
    "eta_to_candidate_tool_min": 14
  },
  "process_requirement": {
    "recipe": "RCP_ET_M1_A",
    "tool_group": "ETCH_GRP_01",
    "chamber_matching_group": "CH_MATCH_G1"
  },
  "downstream": {
    "next_step": "CLEAN_M1_010",
    "reservation": "CLEAN_02@2026-11-04T12:10:00+08:00"
  }
}
```

## 使用要点

- `priority_class` 不是最终派工分数，只是权重输入之一。
- `qtime_state` 必须按当前时间实时刷新，不能只在 MES 工步切换时算一次。
- `current_location` 要能和 AMHS ETA 打通，否则“可派”不等于“可到”。
- `downstream_reservation` 没有时，RTD 应降低高风险窗口 Lot 的派工置信度。
