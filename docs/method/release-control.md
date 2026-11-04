---
title: APS 与投料控制
---

# APS 与投料控制

<div class="keyline" markdown>

**RTD 只能决定 WIP 怎么流，不能独自决定 WIP 有多少。** 如果投料把系统推到 Critical WIP 以上，再好的实时派工也只能在拥堵里重新排序，无法把 Cycle Time 拉回健康区间。

</div>

## 一句话框架

```text
需求 / 交期
  → APS：中长期产能与订单承诺
  → Release Control：控制进入 Fab 的 WIP
  → RTS：短周期目标与预约
  → RTD：分钟级现场派工
  → MES / EAP / AMHS：执行与状态回传
```

RTD/APS 一体化的关键不是把 APS 计划强压给 RTD，而是让 APS 给出**目标、窗口、负荷边界和优先级语义**；RTD 在现场状态变化时保留覆盖权，并把偏差回传给上层滚动修正。

## Factory Physics 底线

| 定律 | 工程含义 |
|---|---|
| `WIP = TH × CT` | 吞吐稳定时，降 WIP 才能降 CT；只靠派工排序降 CT 有上限 |
| `W0 = rb × T0` | Critical WIP 以上，加 WIP 主要增加 CT，不再增加 TH |
| VUT 方程 | 利用率越接近 100%，等待时间非线性爆炸；盲目冲高瓶颈利用率会拖垮全线 |
| 变异性传播 | 上游 release 波动会被下游瓶颈放大，投料节奏本身就是调度变量 |

RTD 项目常见误区是只盯 dispatch score。真正要问的是：**当前 WIP 是否已经超过产线可控区？瓶颈前 buffer 是否足够但不过量？投料是否把未来 24 小时的 Q-time 风险提前制造出来？**

## 投料控制策略

| 策略 | 核心机制 | 适用场景 | 风险 |
|---|---|---|---|
| 静态投料 | 按固定清单或节拍 release | 产品组合稳定、爬坡早期 | 对瓶颈漂移不敏感 |
| CONWIP | 出一个才放一个，控制总 WIP | routing 相对稳定、想控 CT | 产品 mix 大变时需分环 |
| CONLOAD | 控制瓶颈负荷而非总片数 | 品种变化、加工时间差异大 | 瓶颈识别错会误控 |
| SA / 安全虚拟库存 | 虚拟库存低于阈值时投料 | 防止瓶颈饥饿 | 阈值过大变相推高 WIP |
| LCONWIP | 按加工层 / 光刻次数分配 WIP | 高重入、多产品混线 | 层间 WIP 分配需要持续校准 |
| DBR | 瓶颈定鼓点，release 做绳子 | 瓶颈明确且稳定 | 多瓶颈漂移时维护成本高 |

<div class="rtd-strip" markdown>

<span>投料负责</span> 控制 WIP 上限与瓶颈饥饿风险  
<span>RTD 负责</span> 在已投入 WIP 中做局部最优流向选择

</div>

## WIP Cap 怎么定

1. **算原始基准**：估算瓶颈率 `rb`、raw process time `T0`、critical WIP `W0`。
2. **画实测点**：按产品族 / 区域记录 `(WIP, TH, CT)`，看是否落在 bad region。
3. **定控制目标**：WIP cap 不是越低越好，要保证瓶颈不饿死，同时不让 CT 被 WIP 推高。
4. **分层控制**：全厂 WIP cap、区域 WIP cap、瓶颈前 buffer、Q-time 走廊入口 cap 分开设。
5. **滚动校准**：产品 mix、良率、rework、设备可用率变化后，WIP cap 必须重算。

| 控制点 | 建议口径 |
|---|---|
| 全厂 WIP | 约束总体 CT 和资金占用 |
| 瓶颈前 buffer | 保证瓶颈不断料，不追求越多越好 |
| Q-time 走廊入口 | 控制未来窗口内待处理 Lot 数 |
| 批加工队列 | 控制组批机会与等待风险 |
| AMHS / stocker | 控制搬送系统不被 release 波峰打穿 |

## APS 给 RTD 的输入

APS/RTS 不应只丢一张“固定顺序表”给 RTD。更有效的接口是：

| 输入 | 内容 | RTD 使用方式 |
|---|---|---|
| target profile | 分产品 / 分层 / 分区域的产出目标 | 转成 dispatch score 的目标项 |
| WIP cap | 区域、瓶颈、Q-time 走廊入口上限 | 做 release / hold / 限流判断 |
| due date class | 订单承诺、客户优先级、hot lot 语义 | 形成权重但不覆盖硬约束 |
| reservation | 未来短周期设备 / 资源预约 | 派工时校验能否接续 |
| exception policy | 何时允许 RTD 覆盖上层计划 | 写入 override reason code |

## RTD 回传给 APS 的偏差

| 回传项 | 为什么重要 |
|---|---|
| dispatch actual | APS 要知道计划是否被执行，不只是是否下发 |
| override reason | 区分设备异常、资源未到、Q-time 风险、AMHS 延迟 |
| starvation / blockage | 判断投料不足还是下游堵塞 |
| rule hit log | 看规则是否长期压过计划目标 |
| effective capacity | 把 PM、rework、setup、辅助资源等待折算成实际能力 |

没有偏差回传，APS 会持续用乐观产能滚动，RTD 就会长期救火。

## 典型反模式

| 反模式 | 后果 | 修正 |
|---|---|---|
| APS 排完整顺序，RTD 只能照做 | 现场一变，计划立刻失真 | APS 给目标和窗口，RTD 保留覆盖权 |
| 为追交期无限投 Hot Lot | 普通批 CT 拉长，瓶颈前拥堵 | Hot Lot 配额 + 影响面评估 |
| 只控全厂 WIP，不控区域 WIP | 局部瓶颈被淹没 | 区域 cap + 瓶颈 buffer |
| 只控 WIP 数，不控负荷 | 高工时产品造成隐性超载 | 用 CONLOAD / 标准工时负荷 |
| release 波峰不看 AMHS | Lot 投进来但搬不动 | release 与 AMHS capacity 联动 |

## 最小落地路线

1. 建立产品族 / route step 的 WIP、TH、CT 基线。
2. 识别当前瓶颈和候选漂移瓶颈，不只看平均利用率。
3. 设全厂、区域、瓶颈前、Q-time 入口四类 WIP cap。
4. 将 APS/RTS 输出从“硬顺序”改成“目标 + 窗口 + 优先级 + cap”。
5. RTD 决策日志回传 APS，按周校准 effective capacity。

---

相关：[指标体系](metrics.md) · [RTS 与 RTD 分层](rts-rtd.md) · [Q-time 走廊](../topics/qtime-corridor.md)
