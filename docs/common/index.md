---
title: 通用层总览
---

# 通用规则层 · 总览

<div class="keyline" markdown>

**通用层不是"每个区都有的规则"，是"作用对象跨两个区以上"的规则。** 它和区域层是正交的两层，不是并列的第十个区。

</div>

## 为什么必须单独成层

区域档案里各写一遍 Hot Lot，是最常见的做法，也是最容易烂的做法。三个具体理由：

| 理由 | 后果 |
|---|---|
| **冲突仲裁必须集中** | Hot Lot 撞上 Q-time 红区谁压谁，是全局判断。九份分散的规则，改一次策略要改九处，必然改漏 |
| **瓶颈会漂移** | 这月 Litho 堵，下月变 CMP。「瓶颈优先」写死在任何一个区里都是错的 |
| **有些规则不属于任何区** | Q-time 走廊横穿 Clean → Litho → Etch → Diffusion，单一区看不到整条链 |

## 准入标准

一条规则要进通用层，必须同时满足：

1. **作用对象跨两个区以上**，或者
2. **需要在全局唯一确定**（优先级、冻结期、污染等级），或者
3. **需要跨区数据才能计算**（走廊余量、瓶颈位置）

只要一个区自己就能判断的，一律留在区里。这条标准要写进规范，否则通用层会变成垃圾桶——谁都往里塞，最后没人敢动。

!!! warning "临时双轨的代价"
    通用层不是一天能建完的。G2（时间窗走廊）和 G3（资源与污染）依赖链路数据和资源台账，投入大、见效慢。

    先跳过是可以的，但必须在文档里标成**临时双轨**并设补期——各区的 Q-time 规则先自己维护一份。永久双轨比一开始就不做更糟：两套逻辑同时生效，现场会以为系统在随机决定。

## 四个模块

<div class="grid cards" markdown>

-   :material-sort-variant:{ .lg .middle } __G1 优先级与排序__

    ---

    Hot Lot 分级与跨区传递、瓶颈识别与漂移、下游可接性、老化升权。

    <span class="badge badge--done">0.1</span>

    [:octicons-arrow-right-24: 展开](01-priority.md)

-   :material-timer-sand:{ .lg .middle } __G2 时间窗走廊__

    ---

    跨区 Q-time 串联、硬窗与软窗、下游产能预留、超时降级策略。

    <span class="badge badge--wip">0.1</span>

    [:octicons-arrow-right-24: 展开](02-qtime-corridor.md)

-   :material-package-variant:{ .lg .middle } __G3 资源与污染__

    ---

    辅助资源全局预留、污染等级矩阵、载具池分配。

    <span class="badge badge--wip">0.1</span>

    [:octicons-arrow-right-24: 展开](03-resource-contamination.md)

-   :material-shield-sync:{ .lg .middle } __G4 稳定性治理__

    ---

    派工冻结期、重排门槛、override 策略与回写、规则生命周期。

    <span class="badge badge--done">0.1</span>

    [:octicons-arrow-right-24: 展开](04-stability.md)

</div>

## 仲裁序列

通用层独立出来后，必须配套一条显式优先级序列。它写在 G1 里，但约束全部九区。

```text
P0  硬约束         qualification / 污染隔离 / 不合格机台
P1  Q-time 红区    即将超时，余量低于安全线
P2  Hot Lot        按等级继承，跨区不衰减
P3  瓶颈优先       当前瓶颈区的队列优先供料
P4  常规排序       setup 最小化 / WIP 平衡 / 老化升权
```

没有这条序列，通用规则和区域规则冲突时，日志里看起来就是"系统随机决定"。P0 到 P4 之间不允许被权重覆盖，只允许被更高优先级覆盖。

## 和区域层怎么配合

| | 通用层 | 区域层 |
|---|---|---|
| `scope` | fab | area / tool group |
| 回答的问题 | 谁优先、能不能放、什么时候不许改 | 这个区里哪台机、哪个腔体、什么顺序 |
| 维护人 | RTD owner + 计划 | 区域 owner |
| 变更频率 | 低，版本化发布 | 中，随工艺调整 |
| 冲突时 | 高优先级胜出，且必须留痕 | 低优先级降权或丢弃 |

区域层的详细规则见 [区域派工档案](../zones/index.md)；规则字段标准和生命周期见 [规则库设计](../method/rule-library.md)，那页讲的是"一条规则怎么管"，这页讲的是"规则分几层、谁管谁"。

## 落地顺序

不是四个模块同时上。按这个顺序走，前期投入产出比最高：

| 阶段 | 做什么 | 理由 |
|---|---|---|
| 一期 | G1 + G4 | 不需要跨区数据打通，靠现有字段就能配，且立刻能解释现场 90% 的"为什么派它" |
| 二期 | G3 | 依赖辅助资源台账，先把 Reticle / Pod / Boat / Pad 的状态接齐 |
| 三期 | G2 | 依赖完整链路数据，最重，但也最容易出效益 |

一期做完，通用层就已经能撑住日常派工；G2 没上之前，Q-time 规则先在区域层临时维护，按上文标注双轨。

---

相关：[规则库设计](../method/rule-library.md) · [区域对比](../zones/index.md) · [Q-time 走廊专题](../topics/qtime-corridor.md)
