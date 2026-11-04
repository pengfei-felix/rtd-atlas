---
title: Etch 区派工档案
---

# Etch 区派工档案

<div class="keyline" markdown>

**Etch 派工不能停在 tool 级。** 一台 cluster tool 里有多个 chamber，各自有资质、PM、seasoning、matching 和污染状态。系统如果只把整台机当成一个资源，派出来的计划会天然失真。

</div>

## 核心结论

1. **Etch 的决策粒度是 `Lot → Chamber`，不是 `Lot → Tool`。** Tool 只是外壳，真正加工的是 chamber。
2. **Chamber 状态就是辅助资源。** 它不搬动，但会退化、恢复、失配和被 recipe 污染。
3. **PM 后不是马上满血复活。** Clean / PM / seasoning 之后的 chamber 需要恢复窗口，不能按理论产能直接排。
4. **Q-time 压力通常在出片后爆。** 刻蚀完成后到 strip / clean / metrology 的等待，会把腐蚀和残留风险放大。
5. **Matching 比平均利用率重要。** 把关键层派到“不匹配但可用”的 chamber，短期看利用率高，长期看返工和漂移更贵。

<div class="rtd-strip" markdown>

<span>决策粒度</span> Lot → Tool group → Chamber → Downstream window  
<span>核心矛盾</span> Chamber matching vs Q-time vs Setup / Clean

</div>

## 与 Litho 的结构差异

| 维度 | Litho | Etch |
|---|---|---|
| 主约束 | Reticle / Pod 到位 | Chamber 状态与匹配 |
| 资源是否移动 | Reticle 会跨机搬送 | Chamber 不移动，但状态变化 |
| 决策粒度 | Lot → Scanner | Lot → Chamber |
| 主要 setup | 换罩、温层 | Recipe 切换、气体切换、clean |
| 质量闭环 | Overlay / CD 量测 | CD / depth / profile / endpoint |
| 常见风险 | 等罩空转 | 错 chamber、PM 后漂移、Q-time 后窗 |

Etch 的难点不像 Litho 那么显眼。它不是“资源没到”，而是“资源看起来可用，但状态不适合”。

## 八类约束结构

### 1. Chamber 级能力矩阵

同一台 tool 内的 chamber 不一定等价。即使型号相同，etch rate、uniformity、clean 状态、历史漂移也会不同。

| 层级 | 说明 |
|---|---|
| Tool qualification | 整台设备是否允许跑该 recipe |
| Chamber qualification | 某个 chamber 是否允许跑该 layer / product |
| Chamber matching group | 哪些 chamber 可以互相替代 |
| Critical layer lock | 关键层是否必须固定 chamber 或固定 matching group |

高频错误：MES / RTD 只维护 tool 级 qualification，现场实际靠工程师口头限制 chamber。

### 2. PM / Clean / Seasoning 状态

PM 后 chamber 不等于立刻可用。很多工艺需要 seasoning wafer 或 dummy run，让 chamber 状态回到稳定区间。

| 状态 | 派工含义 |
|---|---|
| Production | 正常可派 |
| Post-PM | 刚维护完，限制关键 Lot |
| Seasoning | 恢复中，可跑 dummy 或低风险 Lot |
| Drift warning | 参数漂移接近边界，降低优先级 |
| Inhibit | 禁止派工，等待维护或工程确认 |

RTD 要能看见这些状态。否则系统会把 Post-PM chamber 当成满能力 chamber，关键 Lot 上去以后才发现 CD 漂。

### 3. Chamber matching 与专属性

关键层为了控制 CD、profile 或 uniformity，常常要求固定 chamber 或固定 matching group。

专属性不是越紧越好。太紧会让少数 chamber 排长队，太松会把质量风险扩散到更多 chamber。比较稳的做法是分层：

| 层级 | 策略 |
|---|---|
| Critical layer | 固定 chamber / matching group |
| Semi-critical | 优先原 chamber，异常时走审批放开 |
| Non-critical | 按负载和 setup 优化 |

要点是把“放开条件”工程化，不要让现场临时拍板。

### 4. Recipe 切换与污染序列

Etch 的 setup 不只是换菜单，还包括气体切换、RF 稳定、chamber clean、污染规避。不同 recipe 的先后顺序会影响代价。

| 切换类型 | 代价 |
|---|---|
| 同 family 连跑 | 低 setup，适合 campaign |
| 相近 chemistry 切换 | 中等 setup，需要稳定时间 |
| 跨 chemistry 切换 | 高 setup，可能需要 clean |
| 污染敏感切换 | 必须按固定顺序或加清洁 |

只按“同 recipe 优先”太粗。更好的粒度是 recipe family + contamination rule。

### 5. Q-time 后窗

Etch 后的 Lot 可能需要尽快进入 strip / clean / metrology。出片后如果下游排队，风险不在 Etch 机台上，但锅最后会回到调度系统。

派工前要看：

- 预计出片时下游是否可接；
- AMHS 搬送是否拥堵；
- 是否会同时释放一批 Q-time Lot；
- 下游是否有同样的 chamber / recipe 限制。

### 6. Endpoint 与量测反馈

Etch 有较强的过程反馈：endpoint、CD、depth、profile、OOC 都可能影响后续派工。

如果某个 chamber 连续出现边缘漂移，RTD 应该自动降权或触发工程确认，而不是继续按满能力派。

### 7. Rework 回路

Etch rework 往往不只是“再跑一次”。可能要求回原 chamber、走特定 recipe、或先过工程审批。

派工要单独处理 rework Lot：

- 优先级不能简单继承原 Lot；
- 回原 chamber 是强约束还是软约束要写清；
- rework 会吃掉正常产能，要在可用产能里预留波动。

### 8. 特气与设施约束

特种气体、exhaust、abatement、pump 状态有时会成为隐性约束。单台机看着可用，但设施侧限制会让一组机同时降载。

这类约束不一定第一版就进 RTD 优化，但至少要进风险看板：哪些 recipe 在同一时段不能过度集中。

## 典型派工问题

### 问题 1：Tool 级派工导致 chamber 空闲和排队并存

| 项 | 内容 |
|---|---|
| 现象 | 某台 tool 显示 busy，但内部有 chamber 空；另一些 chamber 长队 |
| 根因 | RTD 只看到 tool，不看 chamber qualification |
| 解法 | 建 chamber 级能力矩阵和 matching group |
| 指标 | chamber utilization、tool 内部负载标准差 |

### 问题 2：PM 后 chamber 被过早派关键 Lot

| 项 | 内容 |
|---|---|
| 现象 | PM 后前几批 CD / profile 波动偏大 |
| 根因 | PM 后恢复状态未进派工模型 |
| 解法 | 设置 seasoning 状态；关键 Lot 避开恢复期 |
| 指标 | Post-PM lot performance、seasoning hit rate |

### 问题 3：Q-time Lot 在 Etch 出片后堵住

| 项 | 内容 |
|---|---|
| 现象 | Etch 按时完成，但后段 strip / clean 排队导致超窗 |
| 根因 | 派工只看 Etch 当前完成时间，不看下游接续 |
| 解法 | 出片前检查下游窗口，必要时延后或换 chamber |
| 指标 | Etch-out 后等待、downstream reservation hit rate |

### 问题 4：Recipe campaign 把 Hot Lot 饿死

| 项 | 内容 |
|---|---|
| 现象 | 同 family 连跑提高了 setup 效率，但急件等待过长 |
| 根因 | campaign 没有饥饿保护和打断条件 |
| 解法 | 设置 campaign 最大长度、Hot Lot 打断门槛、Q-time 红线 |
| 指标 | campaign length、hot lot wait、setup loss |

### 问题 5：Rework 回路挤占正常产能

| 项 | 内容 |
|---|---|
| 现象 | 重工 Lot 突然增多，正常 Lot CT 拉长 |
| 根因 | 产能模型按理论正常流量排，没预留 rework 波动 |
| 解法 | 按历史重工率预留 chamber capacity buffer |
| 指标 | rework wait、rework capacity share、normal lot CT |

## 派工规则骨架

```text
当 chamber 预计空闲：
  1. 枚举候选 Lot，而不是只看 tool 队列
  2. 过滤 chamber qualification / matching / dedication
  3. 检查 chamber 状态：PM、clean、seasoning、drift warning
  4. 计算 recipe switch cost 和 contamination risk
  5. 检查 Q-time 后窗和下游可接续性
  6. 对 Hot Lot / Rework 做影响面校验
  7. 选择综合风险最低的 Lot，并记录派工原因
```

这里的“综合风险最低”，不是单纯分数最高。Etch 很多时候要避开坏选择，而不是追求理论最优。

## 深度案例库

| 案例 | 现场症状 | 根因判断 | RTD 策略 | 验证指标 |
|---|---|---|---|---|
| Chamber matching 失衡 | A chamber 长队，B chamber 空闲但工程不愿放 | matching group 过紧或历史漂移未复核 | 建 critical / semi-critical / normal 三层 matching，semi-critical 支持受控放开 | matching hit rate、chamber load std、OOC rate |
| Post-PM seasoning 误派 | PM 后第一批关键 Lot 量测偏移 | PM 后状态被建成 available | 增加 `POST_PM`、`SEASONING`、`QUALIFIED` 状态；关键层避开恢复期 | post-PM critical lot count、first-lot OOC |
| Campaign 与 Q-time 冲突 | 同 recipe 连跑节省 setup，但黄区 Lot 被饿死 | campaign 没有打断门槛 | 设置最大 campaign 时长、黄区强制插入、红区硬打断 | campaign length、yellow dwell、setup loss |
| Rework 回原 chamber 冲击 | 重工批集中回流，正常批 CT 拉长 | rework 当普通 Lot 排 | rework 单独候选池，按历史 rework rate 预留能力 | rework wait、normal CT、capacity share |
| AMHS 远端 Lot 造成空等 | 系统选了高分 Lot，但 FOUP 到不了 | score 未考虑 ETA | ETA 入分；设备即将空闲时优先近端可执行 Lot | tool wait for material、ETA error |

## 指标体系

| 层级 | 指标 | 用途 |
|---|---|---|
| 主指标 | 有效出片、瓶颈 chamber 利用率 | 看产能 |
| 约束指标 | Q-time 违规、OOC / rework、关键层 matching 命中率 | 防止靠冒险提产能 |
| 观测指标 | setup loss、post-PM 派工、chamber load balance、campaign length | 定位损失 |

Etch 的利用率要看 chamber，不要只看 tool。Tool 利用率漂亮，chamber 内部失衡仍然可能很严重。

## 落地路线

1. **先把 tool 级拆到 chamber 级**：能力矩阵、状态、qualification。
2. **接入 PM / clean / seasoning 状态**：不要把维护后 chamber 当满能力。
3. **建立 recipe family 和 contamination rule**：把 setup 从“同/不同 recipe”升级为序列相关。
4. **把 Q-time 后窗接进派工**：出片前先看下游。
5. **做派工解释日志**：每次为什么选这个 chamber，要能复盘。

---

相关：[Q-time 走廊](../topics/qtime-corridor.md) · [辅助资源流转](../topics/support-resource.md) · [区域对比](../zones/index.md)
