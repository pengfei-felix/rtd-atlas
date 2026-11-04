---
title: Diffusion 区派工档案
---

# Diffusion 区派工档案

**Diffusion 的核心不是"派哪台炉"，而是"这一炉该不该现在开"。** 炉管工时长、批量大、升降温慢，开错一炉几小时内都改不了；错误不会立刻暴露，而是在几小时后变成装载率低、Q-time 爆窗和瓶颈漂移。 <span class="badge badge--done">1.0</span>

## 1 工艺与工序流

### 工艺内容

在高温炉管里做氧化（长二氧化硅）、扩散（把掺杂推进硅里）、退火（修复晶格、激活杂质），以及 LPCVD 薄膜沉积。共同点是慢、热、批量：一炉几十到上百片同进同出，一次几小时到几十小时。

单片快速热处理（RTP）也归在这个区，但逻辑不同——秒级升降温、单片进出，更像注入后的即时补救。8 寸炉管以立式为主、批次更小，12 寸炉管一次开炉的代价更大。

### 工序

<div class="flowloop">
  <div class="flowline">
    <div class="flow-step"><strong>装舟</strong></div>
    <div class="flow-arrow">→</div>
    <div class="flow-step"><strong>升温</strong></div>
    <div class="flow-arrow">→</div>
    <div class="flow-step"><strong>稳定</strong></div>
    <div class="flow-arrow">→</div>
    <div class="flow-step"><strong>反应</strong></div>
    <div class="flow-arrow">→</div>
    <div class="flow-step"><strong>降温</strong></div>
    <div class="flow-arrow">→</div>
    <div class="flow-step"><strong>卸舟</strong></div>
  </div>
  <div class="flowloop-return">整炉同进同出，一开就是几小时到几十小时</div>
</div>

- **装舟**：整批装进石英舟，片间距与舟位直接影响批内均匀性。
- **升温**：炉体升到目标温度，速率受炉型与石英件状态限制。
- **稳定**：等炉内温度场稳定，否则批内批间均匀性不保。
- **反应**：氧化、扩散、退火或沉积，时长由配方决定。
- **降温**：降到可卸载温度，降太快会把应力引进膜里。
- **卸舟**：出片，进入出炉后的 Q-time 计时。

### 派工特点

| 特点 | 落在哪一步 | 不管会怎样 |
|---|---|---|
| **组炉与装载率** | 装舟 | 半炉开一次，浪费的是整炉炉时；为了凑满硬等，又把窗口等爆 |
| **温区切换** | 升温 / 降温 | 升降温和温区来回切，损失比加工本身还大，且换一次就是一段不可打断的占用 |
| **前后 Q-time 双窗** | 进炉前 / 出炉后 | 前窗超了界面自然氧化，后窗超了残留难清；两窗夹着中间那炉，容错极小 |
| **多道 Q-time 串联** | 炉与炉之间 | 单段都合规，串起来超时；第二站超时还会倒逼第一站延后 |
| **Boat / 石英件占用** | 装舟 | 炉空着但没有干净舟具，一样开不了炉；舟具清洁也能卡住整批 |
| **出炉波峰** | 卸舟 | 多炉同时到点，下游一次涌进大量 WIP，走廊与清洗机同时被压满 |

### 上下游

- **上游**：清洗（进炉前必须无自然氧化层与颗粒），与 WET 构成 WET-DIFF 双窗。
- **下游**：清洗 → 量测（膜厚 / 方块电阻），或直接进光刻。
- **横切**：Metrology 供膜厚与电阻数据；Clean 的走廊与它绑得最紧。

## 2 专有派工功能

11 项，按影响程度排：先红线（校验），再可行性（约束），然后效率（策略），最后协调（接口）。这个区的功能大半围着**组批**和**窗口**转。

| 功能 | 类型 | 功能描述 | 解决什么问题 |
|---|---|---|---|
| 比对 Process 与 Q-time | 校验 | 比对 process time 与 Q-time 上限，控制 batch 派货时间，避免过早进入 Q-time 回路 | 对应「前后 Q-time 双窗」：进得太早，在走廊里干等，直接 over |
| 多道 Diffusion Q-time | 校验 | 对 Wet-Diff-Diff 连续两站做预组批，评估第二站会不会 over；会则暂缓第一站与 WET | 对应「多道 Q-time 串联」：只看单段，串起来必爆 |
| ≥2 Batch 时控 WET 派货 | 校验 | 炉台已有 2 个以上 preformed batch 时，控制对应 batch 在 WET 的派货时间 | 对应「前后 Q-time 双窗」：前段放太快，炉台接不住，WET 端就得卡住 |
| Diffusion Batch Zone | 约束 | 组批时考虑 zone inhibit 与 loading effect | 对应「组炉与装载率」：只看片数凑满，温区限制会把这一炉废掉 |
| Diffusion Batch 派工 | 策略 | 依 WET-DIFF 预组批结果，尽量把同批 Lot 一起 jobPrep | 对应「组炉与装载率」：批内 Lot 分批上机，等于把一炉拆成两半跑 |
| Batch Merge Priority | 策略 | 按批内 Lot 的优先级算批优先级，含高等级 Lot 的批优先，片数多的批也优先 | 对应「出炉波峰」：优先级不继承到批，急单被压在大批后面 |
| Diffusion Batch 等待阈值 | 策略 | 设定机台快 idle 时，是继续等未到站的 production Lot 一起组批，还是先把当前到站 Lot 组批上机 | 对应「组炉与装载率」：没有阈值，等与不等全靠现场拍脑袋 |
| Diffusion Batch Score | 策略 | 无 Q-time 压力也需组批时，综合 Lot 优先级、batch size、等待时间打分，权重可调 | 对应「组炉与装载率」：只按片数排，急单永远排在最后 |
| Diffusion Batch Recipe | 策略 | 按 MES 的 layout recipe，在可能范围内尽量选最多片数去组批 | 对应「组炉与装载率」：配方不兼容硬凑，炉装上才发现跑不了 |
| WET-DIFF Q-time | 接口 | 考虑 WET 与 Diff 之间较短的 Q-time，在 WET 对每台扩散机做 batch preforming | 对应「前后 Q-time 双窗」：WET 不知道炉台要什么批，双窗根本无法对齐 |
| Diffusion Manual Batch | 接口 | 支持人工 manual form batch，并优先采纳用户组批结果 | 对应「组炉与装载率」：特殊批（试片、工程批）只能人工定，系统要让路 |

## 3 指标与落地

- [组炉与 SRB](01-组炉与SRB.md) — 组批对象、判断链与等待时间标定
- [Q-time 与温区](02-Qtime与温区.md) — 两类窗口与 Campaign 边界
- [Boat 与出炉波峰](03-Boat与出炉波峰.md) — 舟具建模与波峰预测
- [避坑清单](04-避坑清单.md) — 上线前的最小检查
- 指标口径见 [方法论 · 指标体系](../method/metrics.md)
