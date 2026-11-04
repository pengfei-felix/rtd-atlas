---
title: Little 法则与 Critical WIP
---

# Little 法则与 Critical WIP

<div class="keyline" markdown>

**RTD 不是魔法。** 当系统 WIP 已经超过 Critical WIP，继续加 WIP 只会增加 Cycle Time，不会增加 Throughput。这个边界不处理，派工只能救火。

</div>

## 三个核心量

| 量 | 含义 |
|---|---|
| `T0` | raw process time，理论加工时间之和 |
| `rb` | bottleneck rate，瓶颈长期产出率 |
| `W0` | critical WIP，`W0 = rb × T0` |

## 性能区间

| 区间 | 表现 | 管理动作 |
|---|---|---|
| WIP < W0 | TH 随 WIP 增加 | 防止瓶颈饥饿 |
| WIP ≈ W0 | TH 接近瓶颈能力 | 稳定控制区 |
| WIP > W0 | TH 不再明显增加，CT 增长 | release 限流，削波动 |

## 对 RTD 的含义

| 现象 | 判断 |
|---|---|
| WIP 高、CT 高、TH 不升 | 投料层问题，不是 RTD 排序问题 |
| 瓶颈常饿料 | WIP 或 buffer 太低，release 需增加或重分配 |
| 某区 WIP 爆但全厂 WIP 正常 | 区域 cap 或重入流失衡 |
| Q-time 频繁黄区 | 入口 release 超过走廊容量 |

## 实施步骤

1. 按产品族估算 T0；
2. 按瓶颈能力估算 rb；
3. 得到 W0，作为 WIP cap 初值；
4. 用历史 `(WIP, TH, CT)` 点校准；
5. 按区域和工艺层拆 cap。

---

相关：[CONWIP / CONLOAD / LCONWIP](02-CONWIP-CONLOAD-LCONWIP.md)
