---
title: CMP 研磨
---

# CMP 区派工档案

<div class="keyline" markdown>

**CMP 是派工和工艺控制耦合最强的区。** 同一台机、同一个 recipe，在不同 pad 年龄、conditioner 状态和上一批量测反馈下，实际表现不同。RTD 如果只看机台可用性，会把质量风险排进队列。

</div>

## 核心结论

1. **Pad 是连续退化资源。** 它不是可用/不可用，而是每跑一批都在变。
2. **Run-to-Run 反馈会改变派工价值。** 上一批厚度结果会影响下一批加工参数，派工和参数控制不能完全分开。
3. **清洁与污染隔离是硬边界。** Cu / non-Cu、slurry、pad、brush 状态必须进约束。
4. **CMP 的风险常在量测后暴露。** 派工当下看不出问题，后面表现为返工、re-polish、thickness drift。
5. **利用率不能单看 tool。** Head / platen / pad / conditioner 的状态决定真实能力。

<div class="rtd-strip" markdown>

<span>决策粒度</span> Lot → Tool / Platen / Head → Pad state → R2R context  
<span>核心矛盾</span> 产能 vs 稳定性 vs Pad 寿命

</div>

## 与其他区的结构差异

| 维度 | Litho | Etch | CMP |
|---|---|---|---|
| 主资源 | Scanner | Chamber | Platen / Head |
| 辅助资源 | Reticle / Pod | Chamber condition | Pad / Conditioner / Slurry |
| 状态变化 | 光罩位置变化 | 腔体状态漂移 | Pad 连续退化 |
| 反馈强度 | 中 | 中 | 强 |
| 主要风险 | 等罩、套刻 | CD / profile | 厚度漂移、返工、污染 |

CMP 的特殊性在于：**资源状态和工艺参数是一体的。** Pad 状态变了，派到这台机的 Lot 参数也要跟着变。

## 八类约束结构

### 1. Pad 年龄与退化曲线

Pad 使用后 removal rate 会变化，且不是简单线性。RTD 至少要知道 pad 处在哪个状态段。

| 状态 | 派工处理 |
|---|---|
| Break-in | 新 pad 初期，参数不稳定，限制关键 Lot |
| Stable | 稳定区，正常派工 |
| Warning | 接近寿命边界，限制高风险 Lot |
| End-of-life | 禁止新派工，触发更换 |

如果只用最大寿命做硬卡控，风险会集中在寿命末期那几批。

### 2. Conditioner 状态

Pad dressing 决定 pad 表面状态。conditioner 本身也有寿命和效率变化。

派工需要看：

- conditioner 是否可用；
- dressing 后 pad 是否处于稳定区；
- dressing 频率是否影响节拍；
- conditioner 寿命是否接近 warning。

### 3. Slurry 与化学品

Slurry 类型、供应水位、管路切换、混用限制都会影响派工。库存不是仓储问题，缺料会直接变成设备 idle。

| 资源 | 派工影响 |
|---|---|
| Slurry | 不同 recipe / layer 对应不同化学体系 |
| DI water / Chemical | 清洗能力和污染控制 |
| Brush / Cleaner | post-CMP clean 能力影响后窗 |

### 4. Platen / Head 匹配

CMP 不是单纯 tool 级资源。不同 platen / head 的状态会影响 uniformity 和 removal rate。

要避免两类错误：

1. 把同一台 tool 内所有 platen 当等效资源；
2. 把历史表现不同的 head 混在一起统计。

### 5. Run-to-Run 控制

上一批量测结果会修正下一批参数。RTD 需要知道某个 Lot 派到某台机后，是否有足够近、足够同类的反馈数据支撑。

| 情况 | 风险 |
|---|---|
| 同 product / layer 连续跑 | R2R 反馈稳定，参数可靠 |
| 长时间未跑该 recipe | 反馈过旧，参数不确定 |
| Pad 状态变化后继续用旧反馈 | 反馈失配 |
| 换机台后沿用原参数 | 工艺偏移 |

### 6. Post-CMP Q-time

CMP 后通常要接清洗、量测或下一层。抛光完成后如果清洗/量测堵住，会带来污染、氧化或反馈延迟。

派工前要检查：

- post-CMP clean 是否可接；
- metrology 是否拥堵；
- 量测结果是否会反向影响后续 Lot；
- 当前批完成后是否会造成反馈断层。

### 7. 污染隔离

Cu / non-Cu、不同 slurry、不同金属体系之间的隔离规则必须进模型。污染约束是硬边界，不是优先级。

### 8. Rework / Re-polish 回路

CMP 的返工不是简单重跑。re-polish 会改变剩余厚度、风险和后续参数。RTD 要单独处理 rework Lot，不应和正常 Lot 混用同一优先级。

## 典型派工问题

### 问题 1：Pad 末期集中跑关键 Lot

| 项 | 内容 |
|---|---|
| 现象 | 关键 Lot 在 pad 寿命末期出现厚度漂移或返工 |
| 根因 | Pad 只设硬寿命上限，没有 warning zone |
| 解法 | Pad 分段派工，warning 区限制关键层 |
| 指标 | warning-zone critical lot count、返工率 |

### 问题 2：R2R 反馈和派工脱节

| 项 | 内容 |
|---|---|
| 现象 | 同 recipe 换机台后参数不稳，量测反馈滞后 |
| 根因 | RTD 只看空闲机台，不看反馈上下文 |
| 解法 | 派工时考虑同类反馈新鲜度和机台连续性 |
| 指标 | feedback age、recipe continuity、thickness deviation |

### 问题 3：post-CMP clean 成为隐性瓶颈

| 项 | 内容 |
|---|---|
| 现象 | CMP 端准时完成，下游清洗或量测排队 |
| 根因 | RTD 未检查下游接续能力 |
| 解法 | 派工前预约 post-CMP clean / metrology 能力 |
| 指标 | CMP-out 后等待、downstream reservation hit rate |

### 问题 4：为了提升利用率，过度打断稳定 run

| 项 | 内容 |
|---|---|
| 现象 | tool 利用率提高，但厚度波动变大 |
| 根因 | 小批量插入破坏 R2R 连续性 |
| 解法 | 设置稳定 run 最小长度和打断门槛 |
| 指标 | stable-run length、parameter reset count |

## 深度案例库

| 案例 | 现场症状 | 根因判断 | RTD 策略 | 验证指标 |
|---|---|---|---|---|
| Pad 末期跑关键 Lot | 厚度漂移、re-polish 增多 | pad 只按寿命终点硬切，没有 warning zone | Pad age 分段，warning 区限制 critical layer | warning critical count、re-polish rate |
| R2R 反馈过期 | 同 recipe 久未运行后参数不稳 | 派工不看 feedback freshness | score 加 feedback age 和 recipe continuity | feedback age、thickness deviation |
| Stable run 被 Hot Lot 打断 | 利用率升高但波动变大 | Hot Lot 插入没有质量影响面评估 | stable run 最小长度 + Hot Lot 打断门槛 | stable-run length、parameter reset count |
| Post-CMP clean 堵塞 | CMP 出片后排队，污染风险上升 | 下游 clean / metrology 未预约 | track-in 前检查 post-CMP clean / metrology 容量 | CMP-out wait、reservation hit |
| Slurry / conditioner 隐性缺料 | tool 可用但不能跑对应 recipe | 消耗型资源不在 RTD 状态视图 | slurry、conditioner、brush 寿命进 resource model | material wait、consumable warning hit |

## 派工规则骨架

```text
当 CMP tool 预计空闲：
  1. 枚举候选 Lot
  2. 过滤 contamination / slurry / qualification
  3. 检查 pad age、conditioner、head/platen 状态
  4. 计算 R2R feedback freshness 与 recipe continuity
  5. 检查 post-CMP clean / metrology 下游可接续性
  6. 对 critical layer / rework lot 做风险加权
  7. 选择质量风险与产能损失综合最低的 Lot
```

## 指标体系

| 层级 | 指标 | 用途 |
|---|---|---|
| 主指标 | 有效出片、返工后有效产出 | 看真实产能 |
| 约束指标 | thickness deviation、re-polish rate、contamination violation | 防止靠冒险提产能 |
| 观测指标 | pad age distribution、feedback age、stable-run length、post-CMP wait | 定位漂移来源 |

CMP 的主指标不建议只看 throughput。返工一多，表面产出会掩盖真实损失。

## 落地路线

1. 接入 pad age / conditioner 状态，先做分段派工。
2. 建 recipe / product / tool 的反馈上下文，计算 feedback freshness。
3. 把 post-CMP clean / metrology 接进派工前校验。
4. 建 contamination rule，Cu / non-Cu 等硬隔离规则系统化。
5. 用历史数据回放，比较产出、返工率、厚度偏差和 pad 使用分布。

---

相关：[辅助资源流转](../topics/support-resource.md) · [指标冲突链](../topics/metric-conflict.md) · [约束模型](../method/constraints.md)
