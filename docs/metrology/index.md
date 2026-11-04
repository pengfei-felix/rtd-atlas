---
title: Metrology 量测
---

# Metrology 量测区派工档案

**量测有两个身份：占用产能的消费者，给别区供数据的生产者。** 只按第一个身份排，它会被当成"服务部门"排在所有加工之后；按两个身份排，才知道有些量测比生产还急。 <span class="badge badge--wip">0.1</span>

## 1 工艺与工序流

### 工艺内容

测膜厚、线宽（CD）、套刻精度、缺陷密度。它不改变晶圆，只读取状态；但没有它，前面的工序不知道自己做得对不对，后面的工序不知道该用什么参数。

量测的特殊性在两头：一头是**采样**——不是每批都量，采样规则决定排队结构，比机台产能更影响拥堵；另一头是 **recipe 时长**——同一台机跑不同 recipe，单片时间能差十倍，按片数估产能必然估错。

### 工序

<div class="flowloop">
  <div class="flowline">
    <div class="flow-step"><strong>装载</strong></div>
    <div class="flow-arrow">→</div>
    <div class="flow-step"><strong>对位</strong></div>
    <div class="flow-arrow">→</div>
    <div class="flow-step"><strong>量测 recipe</strong></div>
    <div class="flow-arrow">→</div>
    <div class="flow-step"><strong>数据回传</strong></div>
    <div class="flow-arrow">→</div>
    <div class="flow-step"><strong>卸载</strong></div>
  </div>
  <div class="flowloop-return">晶圆状态没变，但数据必须回到上游和 R2R 才算完成</div>
</div>

- **装载**：进片、固定，部分机台要开真空或吹氮。
- **对位**：对准 mark，套刻与 CD 量测都依赖对位精度。
- **量测 recipe**：膜厚、CD、套刻、缺陷扫描，不同 recipe 时长差一个量级。
- **数据回传**：结果进 SPC、R2R 与派工系统，有时要先过判异规则。
- **卸载**：出片，回到原流程的下一站。

### 派工特点

| 特点 | 落在哪一步 | 不管会怎样 |
|---|---|---|
| **采样规则** | 装载前 | 该量的没量，数据断档；量多了，产能被无关批次吃掉 |
| **recipe 时长差十倍** | 量测 recipe | 按片数估产能会系统性高估，实际被几条长 recipe 吃穿 |
| **数据紧急度** | 数据回传 | 排太晚，上游按老参数继续跑，等结果出来已经跑坏一片 |
| **前后量同机台** | 对位 | 前测与后测不在同一台，数据不可比，前后差值变成噪声 |
| **复机 monitor** | 装载前 | PM 或宕机后不先跑 monitor，机台带病生产，问题要到后段才暴露 |
| **缺陷检测另算** | 量测 recipe | recipe 长、抽样逻辑不同，当成普通量测排，会把整条队堵死 |

### 上下游

- **上游**：所有加工区，量测点由工艺路线决定，通常挂在关键工序之后。
- **下游**：数据流向 SPC、R2R 与派工；晶圆本体回到原流程的下一站。
- **横切**：CMP 的 R2R、Litho 的套刻补偿都拿它当输入，所以它决定了别人的参数新不新。

## 2 专有派工功能

5 项，按影响程度排：先可行性（约束），再效率（策略）。这个区没有"校验"类功能，它的硬约束只有一条——前后量必须同机。

| 功能 | 类型 | 功能描述 | 解决什么问题 |
|---|---|---|---|
| 前后量同机台 | 约束 | MES 要求 Pre-Measurement 与 Post-Measurement 同机台的 Lot，派工必须遵守 | 对应「前后量同机台」：换一台量，前后差值不可比，噪声被当成漂移 |
| High WIP | 策略 | 可指定 wafer mark，WIP 超过 wafer mark 的 product lot 优先派工 | 对应「采样规则」：堆在前面的 product 不优先，采样数据会系统性偏晚 |
| 复机 Monitor 优先 | 策略 | 生产机台 PM 后或宕机后的 monitor lot 优先派工，使机台优先恢复生产 | 对应「复机 monitor」：monitor 排队，机台带病跑，问题后移 |
| 设定量测机台 ProdLot 优先 | 策略 | 可指定某些量测机台的 product lot 优先派工 | 对应「数据紧急度」：为保量测机利用率，重要数据被往后挤 |
| Key Tool 优先 | 策略 | 可自定义 Key Tool，其对应控片优先量测 | 对应「数据紧急度」：关键设备的数据晚到，R2R 一直在用老参数 |

## 3 指标与落地

- [采样规则与产能陷阱](01-采样与产能陷阱.md) — 四个陷阱与产能口径
- 指标口径见 [方法论 · 指标体系](../method/metrics.md)
- 本区尚未拆分独立的落地路线页
