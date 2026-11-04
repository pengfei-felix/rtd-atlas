---
title: 辅助资源流转
---

# 辅助资源流转

<div class="keyline" markdown>

**机台不是唯一资源。** 很多时候队列里有 Lot，机台也空着，但就是不能开工。原因通常不在 dispatch rule，而在 Reticle、Pod、Boat、Pad、Chamber 状态这些辅助资源没有同步到位。

</div>

## 先给结论

RTD 如果只做 `Lot → Tool`，最多是一个排序器。真正能落地的 RTD，至少要同时回答三件事：

1. **Lot 能不能做**：工艺路线、设备资质、Q-time、优先级。
2. **资源在不在位**：Reticle / Pod / Boat / Pad / Chamber 状态是否可用。
3. **到位要多久**：搬送、装载、清洁、检查、升温、恢复会不会吃掉计划余量。

这三件事要在同一个派工动作里判断。先派 Lot，再临时找资源，现场看到的就是“系统显示已派工，机台还在等”。

<div class="rtd-strip" markdown>

<span>错误顺序</span> RTD 派 Lot → 发现资源不在 → 人工协调 → 计划漂移  
<span>正确顺序</span> Lot 候选 + 资源候选 + 到位时间一起算 → 同步下发

</div>

## 四种资源，不是一个模型

| 类型 | 现场特征 | 典型资源 | 派工处理 |
|---|---|---|---|
| **独占型** | 同一时刻只能服务一个对象 | Reticle、专用夹具、关键治具 | 提前锁定、到期释放、冲突检测 |
| **成组型** | 一组资源被一起锁住 | Pod、Boat、Carrier set | 组内组合优化，避免一错锁一组 |
| **退化型** | 越用状态越差，到阈值必须维护 | CMP Pad、石英件、Chamber 内壁状态 | 寿命曲线进模型，不只看可用/不可用 |
| **消耗型** | 使用即消耗，补货有周期 | Slurry、特气、靶材、化学品 | 库存水位 + 补给提前期 + 风险阈值 |

高频错误是把这些资源都建成一个布尔字段：`available = Y/N`。这会漏掉最关键的信息：**它在哪里、还能用多久、到位要多久、被谁提前占了。**

## 一张辅助资源地图

<div class="flowline" markdown>

<div class="flow-step" markdown>
<strong>资源实体</strong><br>
Reticle / Pod / Boat / Pad / Gas
</div>

<div class="flow-arrow">→</div>

<div class="flow-step" markdown>
<strong>当前位置</strong><br>
库房 / Stocker / Tool / 清洁 / 检查
</div>

<div class="flow-arrow">→</div>

<div class="flow-step" markdown>
<strong>状态窗口</strong><br>
寿命 / 检查限 / 温度 / Seasoning
</div>

<div class="flow-arrow">→</div>

<div class="flow-step" markdown>
<strong>派工动作</strong><br>
锁定 / 搬送 / 清库 / 释放
</div>

</div>

这张图的意思很简单：辅助资源不是“附属信息”，而是一条独立流。Lot 流和资源流必须在派工点汇合。

## 分区看，卡点完全不同

| 区域 | 辅助资源主角 | 真正卡点 | RTD 必须知道什么 |
|---|---|---|---|
| **Litho** | Reticle + Pod + Tool Library | 罩在别处、Pod 被锁、库位满 | 光罩位置、Pod 组合、搬送时间、检查限、Tool Library 水位 |
| **Etch** | Chamber 状态 | 资质不等、PM 后状态未恢复、Recipe 切换 | chamber 级能力、clean/seasoning 状态、matching 分组 |
| **Diffusion** | Boat / Quartzware / Tube 状态 | 组炉后才发现舟具不匹配，或石英件寿命不够 | boat 可用性、石英件寿命、炉管温区、清洁周期 |
| **CMP** | Pad / Conditioner / Slurry | Pad 已退化但系统仍按满能力排 | pad age、dressing 状态、slurry 库存、Run-to-Run 反馈 |
| **Implant** | Source / Beamline / 气体 | 换 species、换能量代价被低估 | setup family、contamination 规则、beam tune 时间 |

所以辅助资源专题不能只讲 Litho。Litho 是最典型，但不是唯一。

## 典型场景：为什么“队列有料，机台空转”

### 场景 A：Reticle 已被另一台机锁住

表面看：机台空，队列里有 Lot。  
实际看：该 Lot 需要的 Reticle 在另一台机的 Tool Library 里，且那台机后面还有同罩批次。  
正确动作：不要只看当前空闲机台，要看 **未来 10–15 分钟内哪台机更需要这张罩**。

### 场景 B：Diffusion 凑满一炉，但 Boat 不合适

表面看：同 recipe Lot 已够一炉。  
实际看：可用 Boat 的材质、尺寸或清洁状态不满足；换 Boat 要等清洁或搬送。  
正确动作：组炉时把 Boat 当成约束，不要等炉管空了才找舟具。

### 场景 C：CMP Pad 还没到报警，但已经不适合关键 Lot

表面看：Pad 未到寿命上限，可以继续跑。  
实际看：Pad 已进入性能衰退段，关键层 Lot 上去会提高返工风险。  
正确动作：Pad 不能只设硬上限，要有 warning zone；关键 Lot 要避开末期 Pad。

## RTD 建模字段：最少要有这些

| 字段组 | 关键字段 | 用途 |
|---|---|---|
| 身份 | resource_id、resource_type、compatible_recipe / layer / product | 判断能不能用 |
| 位置 | current_location、target_location、transfer_time | 判断何时到位 |
| 状态 | available_state、lock_owner、release_time | 判断是否被占用 |
| 寿命 | usage_count、warning_limit、inhibit_limit、remaining_life | 避免把快到限资源派给大批量任务 |
| 维护 | clean_due、inspection_due、seasoning_state | 避免排出马上要停的计划 |
| 约束 | capacity、group_id、exclusive_scope | 处理 Pod、Boat 这类成组锁定 |

字段不一定一次全上，但要先把数据结构留出来。否则后面再补，会变成接口返工。

## 三条派工规则骨架

### 1. 资源前置锁定

<div class="code-title">适用：Reticle、Boat、专用夹具</div>

```text
当 Tool 预计在 T 分钟内空闲：
  枚举可加工 Lot
  对每个 Lot 找匹配资源
  计算资源到位时间 + 搬送风险
  若资源不能在 Tool 空闲前到位：降低该 Lot 优先级或剔除
  对最终候选同时锁定 Tool 与资源
```

重点不是“提前多久”，而是提前窗口要跟资源搬送时间匹配。10 分钟能解决 Reticle，不一定能解决 Boat 清洁。

### 2. 退化资源分层使用

<div class="code-title">适用：Pad、Quartzware、Chamber condition</div>

```text
资源寿命分三段：
  Green：普通 Lot 正常使用
  Yellow：限制关键 Lot，优先安排低风险 Lot
  Red：禁止新派工，只允许完成已锁定任务或触发维护
```

这类资源最忌讳“不到上限不管”。到上限才停，往往已经把质量风险传给下一批。

### 3. 成组资源防锁死

<div class="code-title">适用：Pod、Boat、Carrier set</div>

```text
派工前检查：
  这次锁定会不会连带锁住组内其他关键资源？
  组内资源是否还有更高优先级用途？
  锁定时间是否超过阈值？
若会造成高价值资源长时间闲置：换候选 Lot 或换资源组
```

成组资源的风险不是“一个资源用错”，而是“一组资源一起被错误占住”。

## 验证指标

| 指标 | 说明 | 用来判断 |
|---|---|---|
| Resource-wait idle time | 机台因辅助资源不到位造成的空转 | 资源流是否拖住 Lot 流 |
| Empty dispatch ratio | RTD 已派工但无法开工的比例 | 派工是否只停留在系统逻辑可行 |
| Resource move count | 单位时间资源搬送次数 | 是否过度搬运，挤占 AMHS |
| Lock conflict count | 资源锁定冲突次数 | 资源预约机制是否有效 |
| Late release count | 资源用完未及时释放 | 状态回写是否及时 |
| Warning-zone usage | 退化资源在 warning 段承接关键 Lot 的次数 | 风险是否被前移管理 |

## 和 APS / AMHS 的边界

| 系统 | 该负责什么 | 不该负责什么 |
|---|---|---|
| **APS / RTS** | 给出层级产能、campaign 倾向、未来 WIP 预测 | 不直接决定秒级资源锁定 |
| **RTD** | 在现场状态下联合选择 Lot、Tool、辅助资源 | 不直接优化全厂月度投料 |
| **AMHS / MCS** | 执行搬送、反馈位置、处理路径拥堵 | 不替 RTD 决定哪个 Lot 更该做 |
| **MES** | 维护路线、状态、hold、track-in/out | 不替 RTD 做复杂优先级计算 |

一句话：**APS 给方向，RTD 做选择，AMHS 去搬，MES 记账。** 边界不清，最后一定变成现场人工兜底。

## 落地顺序

1. **先接位置**：资源在哪里，是否被锁，预计何时释放。
2. **再接寿命**：检查限、寿命、水位、清洁周期。
3. **再做预约**：RTD 能提前锁定，且能自动释放。
4. **最后做优化**：把搬送、寿命、锁定冲突放进目标函数。

不要一上来就做复杂优化。没有资源状态，模型再漂亮也是空的。

---

相关：[Litho 八重约束结构](../litho/01-八重约束.md) · [约束四类图例](../legend/constraint-types.md) · [Etch 区派工档案](../etch/index.md) · [Diffusion 区派工档案](../diffusion/index.md)
