---
title: Q-time 窗口样例
---

# Q-time 窗口样例

## 单窗口字段

| 字段 | 示例 | 说明 |
|---|---|---|
| window_id | QT_CLEAN_TO_ETCH_M1 | 窗口 ID |
| lot_id | L20261104-001 | Lot ID |
| start_event | CLEAN_TRACK_OUT | 起点事件 |
| end_event | ETCH_TRACK_IN | 终点事件 |
| start_time | 2026-11-04 10:20 | 起点时间 |
| max_duration_min | 240 | 最大允许时长 |
| yellow_threshold_min | 120 | 剩余 120 分钟进黄区 |
| red_threshold_min | 30 | 剩余 30 分钟进红区 |
| consumed_min | 155 | 已消耗 |
| remaining_min | 85 | 剩余 |
| state | YELLOW | 当前状态 |

## 走廊窗口

一个 Lot 经常不止一个窗口。RTD 应看走廊而不是单点。

| sequence | window | current_state | downstream_dependency |
|---:|---|---|---|
| 1 | CLEAN → ETCH | YELLOW | ETCH chamber matching |
| 2 | ETCH → STRIP | GREEN | STRIP tool availability |
| 3 | STRIP → METROLOGY | GREEN | metrology queue |

## JSON 表达

```json
{
  "lot_id": "L20261104-001",
  "qtime_corridor": [
    {
      "window_id": "QT_CLEAN_TO_ETCH_M1",
      "start_event": "CLEAN_TRACK_OUT",
      "end_event": "ETCH_TRACK_IN",
      "start_time": "2026-11-04T10:20:00+08:00",
      "max_duration_min": 240,
      "remaining_min": 85,
      "state": "YELLOW",
      "risk_driver": ["ETCH_CHAMBER_MATCHING", "AMHS_ETA"]
    },
    {
      "window_id": "QT_ETCH_TO_STRIP_M1",
      "projected_start_time": "2026-11-04T12:05:00+08:00",
      "max_duration_min": 90,
      "projected_remaining_min": 68,
      "state": "GREEN",
      "risk_driver": ["STRIP_PORT_CAPACITY"]
    }
  ]
}
```

## 派工使用方式

| 情况 | 策略 |
|---|---|
| 当前窗口 yellow，但下游可预约 | 提前派工并锁下游窗口 |
| 当前窗口 green，但下一窗口预计 red | 不应盲目 track-in，先解决下游 |
| 多个 Lot 同时 yellow | 按剩余时间、可执行性、下游接续排序 |
| 红区但资源未到 | 触发 exception，不要生成不可执行推荐 |

Q-time 不是一个倒计时字段，而是一条跨工序走廊。
