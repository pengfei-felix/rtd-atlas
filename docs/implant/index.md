---
title: Implant 离子注入
---

# Implant 区派工档案

<div class="keyline" markdown>

**Implant 的核心矛盾不是机台空不空，而是该不该切。** species、energy、dose、tilt 任一维度变化，都可能带来束流整定、污染隔离和量测确认。派工如果只追求“有空就上”，换线损失会把产能吃掉。

</div>

## 核心结论

1. **序列相关 setup 是主角。** 注入区的换线时间取决于前后 recipe 组合，不能建成固定常数。
2. **污染隔离是硬约束。** dopant / metal contamination / resist 状态不能为产能让步。
3. **束流稳定时间要进模型。** Beam tune、dose 校准、energy 切换不是“设备可用”后自动消失。
4. **Q-time 常在注入后引爆。** 注入完成后到 anneal / strip / clean 的窗口，不能等出片后再管。
5. **批量性弱于炉管，但强于光刻。** Implant 既要凑同类，又不能为了凑批把窗口拖爆。

<div class="rtd-strip" markdown>

<span>决策粒度</span> Lot → Tool → Setup family → Downstream window  
<span>核心矛盾</span> 少换线 vs 防饥饿 vs Q-time

</div>

## 与其他区的结构差异

| 维度 | Litho | Diffusion | Implant |
|---|---|---|---|
| 主矛盾 | Reticle / Pod 到位 | 组炉与温区 | recipe 切换序列 |
| 资源状态 | 光罩位置、寿命 | boat、石英件、炉管温区 | source、beamline、污染族 |
| 派工尺度 | 分钟级 | 小时级 | 分钟到小时级 |
| 典型损失 | 等罩白空间 | 半炉、升降温 | beam tune、换 species、确认片 |
| 风险 | Q-time、dedication | 前后窗、装载率 | contamination、剂量/能量漂移 |

Implant 的派工目标更像“排换线顺序”，不是单纯“挑下一批”。

## 七类约束结构

### 1. Setup family

把 recipe 按切换代价聚成族，是 Implant 派工的第一步。

| 切换维度 | 派工含义 |
|---|---|
| Species / Dopant | B、P、As 等切换可能引入污染和束流重调 |
| Energy | 能量档位变化影响 beamline 稳定和校准 |
| Dose | 剂量跨度大时需要重新确认稳定性 |
| Angle / Tilt | 角度切换影响机械和工艺稳定 |
| Resist / Mask 状态 | 某些状态下等待时间会影响注入结果 |

不要只按 recipe code 分组。现场真正关心的是：**从当前状态切到下一批，要付出多大代价。**

### 2. Beam tune 与稳定窗口

机台从“空闲”到“可生产”中间还有一段整定。RTD 要区分：

| 状态 | 含义 |
|---|---|
| Idle | 设备空闲，但不代表可直接加工 |
| Tuning | 束流调试中，不宜插入新任务 |
| Qualified | 当前 setup 已确认，可连续生产 |
| Drift warning | 稳定性接近边界，应限制关键批 |
| Inhibit | 禁止派工，等待工程确认 |

如果系统只看到 Idle / Busy，就会把 tune 时间藏进白空间里。

### 3. 污染隔离

不同 dopant 或材料体系之间可能存在交叉污染。污染规则必须做成硬约束，而不是排序权重。

| 规则类型 | 例子 | 处理方式 |
|---|---|---|
| 专机专用 | 某些 species 固定机台 | 硬过滤 |
| 单向切换 | A 后可跑 B，B 后不能直接跑 A | sequence rule |
| 清洁后切换 | 跨污染族必须 clean | 加 setup / clean 工时 |
| 工程审批 | 特殊产品临时放开 | 记录审批与原因码 |

污染约束一旦靠人工记忆维护，迟早会漏。

### 4. 批量与凑批

Implant 可以通过同族连续加工减少 setup，但不能无限等。

| 模式 | 适用 | 风险 |
|---|---|---|
| 同族连跑 | 到达集中，Q-time 宽 | 普通批等待变长 |
| 时间窗优先 | 注入后窗口紧 | setup 增加 |
| Hot Lot 插入 | 交期或窗口危险 | 打断 campaign |

关键是给 campaign 设置边界：最大连续时长、最大等待、Hot Lot 打断条件。

### 5. Q-time 后窗

注入后到 anneal / strip / clean 的等待窗口容易被低估。原因是 RTD 常把注入完成当成目标，而不是把下游接上当成目标。

派工前应检查：

- 预计出片时下游是否可接；
- AMHS 是否能及时搬送；
- 下游是否也有 batch / setup 限制；
- 当前插入是否会挤掉已在窗口内的 Lot。

### 6. 机台能力矩阵

能量范围、剂量范围、束流稳定性、污染族，会形成非对称资格矩阵。高能力机可以跑低要求任务，低能力机不能反过来。

派工时要避免把高能力机长期占在低要求任务上，导致高要求任务无机可上。

### 7. 量测与确认片

某些切换后需要确认片或量测反馈。确认片不是“附加动作”，它占用设备、量测资源和时间窗口。

RTD 应把确认片纳入可用产能扣减，否则排产会系统性偏乐观。

## 典型派工问题

### 问题 1：频繁换 species，产能被 tune 吃掉

| 项 | 内容 |
|---|---|
| 现象 | 机台利用率不低，但有效出片上不去 |
| 根因 | 排序只看 Lot 紧迫度，没看 setup family |
| 解法 | family campaign + 最大等待保护 |
| 指标 | setup 占比、同 family 连跑批数、有效出片 |

### 问题 2：为了少换线，把 Q-time 等爆

| 项 | 内容 |
|---|---|
| 现象 | campaign 很漂亮，但注入后窗口频繁擦边 |
| 根因 | 只优化当前机台 setup，不看下游窗口 |
| 解法 | campaign 打断条件引入 Q-time 风险 |
| 指标 | Q-time violation、yellow-zone dwell、campaign interruption |

### 问题 3：高能力机被低要求任务占满

| 项 | 内容 |
|---|---|
| 现象 | 某些高能量 / 高剂量任务排长队，低要求任务却占着关键机 |
| 根因 | qualification 只看“能不能跑”，不看“替代性” |
| 解法 | 引入机台柔性度：柔性低的任务优先拿稀缺能力 |
| 指标 | scarce-tool occupation、关键任务等待时长 |

### 问题 4：污染规则靠人工兜底

| 项 | 内容 |
|---|---|
| 现象 | 系统可派，现场工程师手动 hold 或改派 |
| 根因 | contamination rule 没进 RTD |
| 解法 | 污染族、单向切换、clean 后切换规则表化 |
| 指标 | 人工改派次数、污染规则拦截数 |

## 深度案例库

| 案例 | 现场症状 | 根因判断 | RTD 策略 | 验证指标 |
|---|---|---|---|---|
| Setup family 矩阵缺失 | 频繁换 species / energy，tune 时间吞掉产能 | recipe code 粒度过细或过粗，未建序列代价 | 建 from-to setup matrix，按 family campaign 排序 | setup share、same-family run length |
| 单向污染规则漏建 | 系统可派，现场工程师手动拦截 | contamination rule 靠人工记忆 | 污染族、单向切换、clean 后切换硬过滤 | manual override、contamination block count |
| 高能力机被低要求任务占用 | 高能量任务排队，普通任务跑关键机 | qualification 只看能不能跑，不看稀缺性 | 引入 tool flexibility index，低柔性任务优先稀缺机 | scarce capacity occupation、critical wait |
| 注入后退火窗口失控 | Implant 准时完成，anneal 入口堵 | 派工目标停在 implant track-out | 派工前检查 anneal / strip 接续，必要时延后 track-in | implant-out wait、downstream reservation hit |
| Confirmation wafer 未扣产能 | 计划产能乐观，实际 move 不达标 | 切换确认片未进入 effective capacity | 把 confirmation wafer 建成占用事件 | tune+confirm time、effective TH error |

## 派工规则骨架

```text
当 Implant tool 预计空闲：
  1. 枚举候选 Lot
  2. 过滤 capability / contamination / dedication
  3. 计算 current setup → candidate setup 的序列相关代价
  4. 检查 beam tune / qualification / confirmation wafer 需求
  5. 检查注入后 Q-time 与下游接续
  6. 评估 campaign 延续收益与饥饿风险
  7. 选择总代价最低且不触发硬约束的 Lot
```

## 指标体系

| 层级 | 指标 | 用途 |
|---|---|---|
| 主指标 | 有效出片、瓶颈机台产出 | 看产能 |
| 约束指标 | contamination violation、Q-time 违规、关键层命中率 | 防止冒险换产能 |
| 观测指标 | setup 占比、beam tune 时间、同 family 连跑、Hot Lot 打断 | 定位损失 |

## 落地路线

1. 建 setup family：species / energy / dose / angle。
2. 建 contamination rule：硬约束优先，不要当权重。
3. 接入 beam tune / confirmation 状态。
4. 把注入后 Q-time 与下游预约接进派工。
5. 用历史回放标定 campaign 最大长度和打断条件。

---

相关：[Q-time 走廊](../topics/qtime-corridor.md) · [指标冲突链](../topics/metric-conflict.md) · [约束模型](../method/constraints.md)
