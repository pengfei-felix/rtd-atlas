---
title: Post-CMP 与污染隔离
---

# Post-CMP 与污染隔离

## Post-CMP 接续

CMP 完成后通常需要 clean、metrology 或下一层接续。派工前必须看下游。

| 接续 | 风险 | RTD 策略 |
|---|---|---|
| post-CMP clean | 污染 / 氧化 | track-in 前预约 clean |
| metrology | R2R 反馈滞后 | 避免制造反馈断层 |
| next process | Q-time 后窗 | 检查走廊容量 |
| AMHS move | 搬送长尾 | ETA 入 score |

## 污染隔离

| 类型 | 处理 |
|---|---|
| Cu / non-Cu | 硬隔离 |
| slurry family | 切换需要 flush / clean |
| pad / brush 状态 | 不合格硬过滤 |
| re-polish | 单独路径和风险权重 |

## 稳定 Run 保护

Stable run 被频繁插入会破坏 R2R 连续性。

| 打断来源 | 处理 |
|---|---|
| Hot Lot | 计算参数重置和反馈断层代价 |
| Q-time | 红区可打断，黄区需评估 |
| Pad warning | 限制继续跑关键层 |
| Metrology delay | 暂缓高风险 Lot |

## 避坑清单

| 坑 | 后果 | 修正 |
|---|---|---|
| 只看 tool available | pad / head 风险不可见 | 资源状态分层 |
| R2R 与 RTD 分离 | 参数上下文被打断 | feedback freshness 入 score |
| post-CMP 不预约 | 出片后堵塞 | downstream reservation |
| re-polish 当普通重工 | 厚度风险失真 | rework model 单独建 |
| slurry 不进模型 | 缺料造成 idle | 消耗型资源建模 |

---

相关：[Pad 状态与 R2R 反馈](01-pad与R2R.md) · [AMHS/MCS 联动](../topics/amhs-mcs-coupling.md)
