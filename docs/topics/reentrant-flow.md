---
title: 重入流路径
---

# 重入流路径

<div class="keyline" markdown>

**重入流让 Fab 不是流水线，而是一张回路网。** 同一片晶圆会多次返回同一工艺区，当前队列只是一个瞬时切片。RTD 如果只看眼前队列，就会低估未来 30 分钟马上回来的 WIP。

</div>

## 先给结论

重入流带来三个派工后果：

1. **负荷会被层数放大。** 产品层数一变，瓶颈就可能转移。
2. **队列构成持续变脸。** 同一区里混着不同层、不同产品、不同窗口状态。
3. **未来 WIP 比当前队列更重要。** 辅助资源和下游能力必须提前准备。

<div class="rtd-strip" markdown>

<span>核心问题</span> 当前队列不是全部需求  
<span>派工重点</span> 回流预测 + 层间配平 + 负荷换算

</div>

## 三种重入形态

| 形态 | 描述 | 派工影响 |
|---|---|---|
| **同区重入** | Lot 多次返回同一工艺区 | 队列构成复杂，层间争资源 |
| **跨区循环** | Litho → Etch → Clean → Litho 这类回路 | Q-time 与下游预约强耦合 |
| **反馈重入** | 量测 / rework 后回原区 | 产能随机波动，优先级需要重算 |

三种形态叠在一起，才是 Fab 派工和普通车间排产的本质区别。

## 负荷如何被放大

重入次数不是背景信息，而是负荷乘数。

```text
区域负荷 ≈ Σ(产品投料量 × 该产品经过该区次数 × 单次加工时间)
```

举个现场化的判断：

| 变化 | 表面看 | 实际影响 |
|---|---|---|
| 高层数产品占比上升 | 投料总量没变 | Litho / Etch 负荷上升 |
| Rework 率上升 | 正常 route 没变 | 对应区域出现额外回流 |
| 某层 Q-time 收紧 | 工艺没变 | 派工自由度下降 |
| 产品 mix 切换 | 总 WIP 近似不变 | 瓶颈位置可能改变 |

所以瓶颈判定不能只看设备利用率，也不能只看今天队列长度。必须看“未来负荷”。

## 当前队列为什么会骗人

<div class="timeline" markdown>

<div class="tl-node normal" markdown>
<strong>Lot A</strong><br>
Layer 3，刚进区
</div>
<div class="tl-line"></div>
<div class="tl-node warn" markdown>
<strong>Lot B</strong><br>
Layer 18，Q-time 黄区
</div>
<div class="tl-line"></div>
<div class="tl-node hot" markdown>
<strong>Lot C</strong><br>
Rework，需回原机
</div>
<div class="tl-line"></div>
<div class="tl-node normal" markdown>
<strong>Lot D</strong><br>
30 分钟后回流
</div>

</div>

在系统里，它们都可能只是“等待 Litho/Etch 的 Lot”。但派工意义完全不同：

- A 可以等；
- B 需要保窗口；
- C 需要原机台或原 chamber；
- D 还没进队列，但它可能决定现在该不该搬 Reticle 或维持 campaign。

## 回流预测要看什么

| 预测对象 | 数据来源 | 用途 |
|---|---|---|
| 未来到达 Lot | MES route + 当前 step + 标准加工时间 | 预测 15/30/60 分钟后队列构成 |
| 辅助资源需求 | route layer / recipe / reticle / pad / boat | 提前搬送或锁定资源 |
| 下游窗口 | Q-time / Timelink / downstream availability | 防止当前派工把后段堵死 |
| Rework 概率 | 量测历史 / OOC / chamber/tool 状态 | 预留随机回流产能 |
| 产品 mix | APS / release plan | 判断瓶颈是否会漂移 |

回流预测不必一开始很精确。哪怕只做未来 30 分钟的粗预测，也比完全不看未来强。

## 层间配平

同一区里的不同层，不应只按“谁先到”排序。目标是维持整条路线节拍。

| 策略 | 作用 | 风险 |
|---|---|---|
| 先进先出 | 简单稳定 | 忽略层间紧迫度 |
| Due date 优先 | 保交付 | 易打断 campaign |
| Layer balance | 保持各层 WIP 分布 | 需要目标 WIP 曲线 |
| Bottleneck feeding | 优先喂瓶颈下游 | 依赖瓶颈识别准确 |

更好的做法是把层间 WIP 设成一条目标曲线：某层低于下限就补，某层高于上限就压。

## 重入流和辅助资源的关系

Litho 是最典型的例子：未来回流 Lot 还没到，但它需要的 Reticle 现在就该开始搬。

```text
如果只看当前队列：
  当前没有同罩 Lot → 不搬 Reticle

如果看回流预测：
  20 分钟后会回来 3 批同层 Lot → 提前搬 Reticle，维持 Tool Library 水位
```

这也是“料到了罩没到”的根因：Lot 流和资源流没有同步预测。

## 重入流和 Q-time 的关系

重入路径上的 Q-time 不一定单段很紧，但会串起来。

```text
Litho out → Etch in → Clean → Metrology → 下一层 Litho
```

前一段为了清当前队列多等了 10 分钟，下一层可能就少 10 分钟。RTD 不能把每段当独立事件。

## 验证指标

| 指标 | 说明 | 用途 |
|---|---|---|
| Return forecast error | 预测回流到达和实际到达的偏差 | 判断预测质量 |
| Layer WIP balance | 各层 WIP 相对目标曲线偏差 | 判断层间是否失衡 |
| Starvation by no-match | 有机台但无匹配 Lot 的白空间 | 判断当前队列是否不足 |
| Resource preposition hit rate | 提前搬送资源后被命中的比例 | 判断资源前置是否有效 |
| Rework capacity share | 重工回流占用产能比例 | 判断随机回流影响 |

## 落地顺序

1. 先做 route-step 级未来到达预测，不求精细。
2. 把预测窗口分成 15 / 30 / 60 分钟三档。
3. 对 Litho / Etch 先接辅助资源需求预测。
4. 对 Q-time 走廊接下游窗口预测。
5. 最后做 layer WIP 目标曲线和瓶颈联动。

---

相关：[全厂派工全景](../overview/index.md) · [辅助资源流转](support-resource.md) · [Q-time 走廊](qtime-corridor.md) · [瓶颈判定](../overview/bottleneck.md)
