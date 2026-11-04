---
title: Q-time 走廊
---

# Q-time 走廊

<div class="keyline" markdown>

**Q-time 不是一个个独立闹钟，而是一条会被持续消耗的时间走廊。** 前一段多等十分钟，后一段就少十分钟。等系统报警时，通常已经没有好选择，只剩插队、hold 或返工。

</div>

## 先给结论

Q-time 管不好的根因，通常不是“规则不够急”，而是系统只看单个窗口，没有看整条走廊。

真正有效的做法有三条：

1. **入口限流**：不要让太多 Lot 同时进入紧窗口区。
2. **中段保余量**：黄色阶段就动作，不要等红色报警。
3. **出口防传染**：一个窗口的抢救动作，不能把下一个窗口挤爆。

<div class="rtd-strip" markdown>

<span>窗口视角</span> 这段有没有超时  
<span>走廊视角</span> 这批 Lot 后面还有多少时间预算

</div>

## 窗口和走廊的区别

| 视角 | 看什么 | 典型动作 | 问题 |
|---|---|---|---|
| **单窗口** | 当前 Step 是否快超时 | 剩余时间短的先做 | 容易全场插队，局部救火 |
| **走廊** | 一串 Step 的累计余量 | 控制入口、锁定下游、提前改派 | 需要跨区状态和未来 WIP |
| **全厂** | 多条走廊之间的资源竞争 | 投料节奏、瓶颈缓冲、campaign | 需要 APS / RTS 配合 |

单窗口适合做报警，走廊才适合做派工。

## 一条 Q-time 走廊长什么样

<div class="timeline" markdown>

<div class="tl-node hot" markdown>
<strong>清洗</strong><br>
窗口启动
</div>
<div class="tl-line"></div>
<div class="tl-node warn" markdown>
<strong>光刻</strong><br>
紧窗口
</div>
<div class="tl-line"></div>
<div class="tl-node hot" markdown>
<strong>刻蚀</strong><br>
腐蚀风险
</div>
<div class="tl-line"></div>
<div class="tl-node normal" markdown>
<strong>去胶</strong><br>
释放风险
</div>
<div class="tl-line"></div>
<div class="tl-node normal" markdown>
<strong>量测</strong><br>
闭环确认
</div>

</div>

这条路上每个节点都可能“看起来没超”，但累计余量已经被吃完。现场最烦的就是这种：查单点记录都合规，整条链路就是不断擦边。

## 三种失效模式

<div class="grid cards" markdown>

-   :material-alert-circle-outline:{ .lg .middle } __单点超时__

    ---

    某个窗口直接被撑爆。

    **表现**：集中、偶发、容易定位。  
    **成因**：设备宕机、AMHS 延迟、料未到。  
    **处置**：异常处理 + 备机策略。

-   :material-chart-bell-curve-cumulative:{ .lg .middle } __累积挤压__

    ---

    每一段都没超，但每一段都在吃余量。

    **表现**：持续擦边，一有扰动就成片爆。  
    **成因**：投料过密、瓶颈前排队、下游未锁定。  
    **处置**：走廊限流 + 黄色预警。

-   :material-swap-vertical-bold:{ .lg .middle } __优先级倒挂__

    ---

    Hot Lot 被救下，普通 Lot 被挤爆。

    **表现**：加急批达标，普通批持续违规。  
    **成因**：优先级只看 due date，不看走廊占用。  
    **处置**：Hot Lot 配额 + 影响面评估。

</div>

## RTD 应该怎么管

### 1. 入口限流：别把 Lot 推进危险区

Q-time 最好的处理点不是红灯前一分钟，而是在窗口启动之前。

| 做法 | 说明 |
|---|---|
| 设置安全等待点 | 让 Lot 在窗口外等待，而不是进窗口后排队 |
| 限制在窗 Lot 数 | 同一紧窗口段内 Lot 数超过上限就暂停放行 |
| 看下游可用性 | 下游机台、辅助资源、搬送能力不足时不启动窗口 |

一句现场话：**宁可门口等，不要走廊里堵。**

### 2. 黄色预警：把动作前移

| 状态 | 判据示例 | RTD 动作 |
|---|---|---|
| 绿 | 剩余时间充足，且下游可用 | 正常排序 |
| 黄 | 剩余时间进入缓冲区，或下游资源不确定 | 提权、预约下游、锁定搬送 |
| 红 | 低于安全阈值 | 抢占、专机、通知值班，必要时 hold |

红色阶段已经没有优雅解。系统如果长期靠红色阶段救火，说明规则设计不合格。

### 3. 下游预约：不要只救当前一步

派工一个 Q-time Lot 时，不能只看当前机台。至少要检查：

- 下一步设备有没有窗口；
- 下一步需要的辅助资源是否在位；
- AMHS 搬送时间是否够；
- 当前插队会挤掉哪些已在窗口内的 Lot。

这就是 Q-time 走廊和辅助资源流转的交叉点。

## 排序权重怎么写

不要只用“剩余时间最短优先”。它会把全厂变成急诊室，频繁打断 campaign 和 setup，最后每个人都急。

更稳的写法是把 Q-time 当成非线性风险：

```text
Risk = f(remaining_time / window_length)

当 remaining_ratio > 0.5：风险低，按正常规则排
当 0.2 < remaining_ratio <= 0.5：风险快速上升，开始预约下游
当 remaining_ratio <= 0.2：风险陡升，进入抢救模式
```

再叠加三个校验：

1. **可行性校验**：即使插队，是否真的能在窗口内完成。
2. **影响面校验**：被挤掉的 Lot 会不会进入红区。
3. **setup 校验**：频繁换线会不会把节省的时间吃掉。

## 归因要分清

Q-time 超时不能只记一条“违反”。至少要拆成下面几类，否则复盘没有用。

| 归因 | 典型原因 | 改进方向 |
|---|---|---|
| 设备侧 | 宕机、PM 延长、chamber 不可用 | 备机策略、PM 避峰、dedication 放宽流程 |
| 物流侧 | OHT 拥堵、Stocker 排队、资源搬送慢 | 搬送预约、AMHS 优先级、路径避堵 |
| 计划侧 | 投料过密、同窗口 Lot 过多 | 入口限流、APS 调整、瓶颈缓冲 |
| 派工侧 | 规则只看当前窗口、Hot Lot 过度插队 | 走廊权重、影响面校验、Hot Lot 配额 |
| 工艺侧 | 窗口设置不合理、重工回路未考虑 | 工艺复核、窗口分级、重工专用策略 |

## 分区差异

| 区域 | Q-time 典型压力 | 派工重点 |
|---|---|---|
| **Litho** | 涂胶/显影/刻蚀之间多段窗口，且常伴随 Reticle 约束 | 下游锁定 + Reticle 到位预判 |
| **Etch** | 刻蚀后到 strip / clean 的腐蚀风险 | chamber 级快速改派，避免出片后排队 |
| **Diffusion** | 清洗后进炉、出炉后后段处理；炉管长工时放大风险 | 组炉前做窗口可行性，不要硬凑满炉 |
| **CMP** | 抛光后清洗/量测窗口，受 pad 状态和量测回路影响 | Run-to-Run 反馈与队列时间联动 |
| **Implant** | 注入后退火窗口，换 species 会吃掉余量 | setup family 与窗口优先级一起算 |

## 和 APS / RTS 的关系

RTD 可以救急，但不能无限救急。若同一条走廊长期处在黄色或红色，根因通常在计划层：投料节奏、瓶颈缓冲、campaign 安排不对。

| 层级 | 该管什么 |
|---|---|
| **APS** | 决定未来几天哪些产品、哪些层会进入紧窗口区 |
| **RTS** | 做分钟到小时级的下游资源预留和瓶颈协调 |
| **RTD** | 根据现场状态做秒级到分钟级改派和抢救 |
| **MES** | 记录窗口启动/停止、hold、track-in/out 事实 |

最怕的是 APS 放进来，RTS 不预留，RTD 最后靠插队救。那不是智能调度，是把问题推给最后一棒。

## 验证指标

| 指标 | 说明 | 判断重点 |
|---|---|---|
| Q-time violation count | 违规次数 | 总量是否下降 |
| Yellow-zone dwell time | Lot 在黄色区停留时间 | 是否提前动作 |
| Red-zone rescue ratio | 红区靠抢救完成的比例 | 是否仍在救火模式 |
| Corridor WIP | 同一走廊内在窗 Lot 数 | 入口是否失控 |
| Downstream reservation hit rate | 预约下游后成功接上的比例 | 下游锁定是否有效 |
| Hot Lot impact count | Hot Lot 插队导致其他 Lot 进红区次数 | 优先级是否倒挂 |

## 常见坑

1. **只在报警时动作。** 报警不是控制点，是失败信号。
2. **剩余时间最短优先一把梭。** 会牺牲 setup 和全局稳定性。
3. **不看下游资源。** 当前做完了，下游排队，窗口照样爆。
4. **Hot Lot 无上限。** 所有批都变急，等于没有急件。
5. **违规不归因。** 只统计次数，不知道该找设备、计划还是派工。

---

相关：[Litho 八重约束结构](../litho/01-八重约束.md) · [辅助资源流转](support-resource.md) · [指标冲突链](metric-conflict.md) · [RTS 与 RTD 分层](../method/rts-rtd.md)
