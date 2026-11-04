---
title: Chamber 级建模
---

# Etch Chamber 级建模

<div class="keyline" markdown>

**Etch 的真实资源不是 tool，而是 chamber。** Tool 级派工能解决“这台机能不能跑”，但解决不了“哪一个 chamber 跑才稳、才快、才不会破坏 matching”。

</div>

## 三层资源模型

| 层级 | 作用 |
|---|---|
| Tool | port、robot、cluster 外壳、整体 PM 状态 |
| Chamber | qualification、matching、seasoning、clean、漂移状态 |
| Downstream | strip / clean / metrology 的接续窗口 |

派工对象应是：

```text
Dispatch = Lot + Tool + Chamber + Recipe family + Downstream reservation
```

## Chamber 字段

| 字段 | 用途 |
|---|---|
| chamber_id | 唯一 chamber |
| parent_tool | 所属 tool |
| qualified_recipe | 可跑 recipe / family |
| matching_group | 可替代组 |
| dedication_level | critical / semi-critical / normal |
| seasoning_state | post-PM、seasoning、qualified |
| drift_state | green / yellow / inhibit |
| last_recipe | 计算切换代价 |
| pm_due_time | 预测未来可用性 |
| contamination_state | 污染族 / clean 需求 |

## 候选集生成

```text
1. 从 MES 队列取可加工 Lot
2. 按 tool group 找候选 tool
3. 展开到 chamber 粒度
4. 过滤 chamber qualification / hold / contamination
5. 计算 matching、setup、Q-time、ETA、downstream
6. 输出 Lot-Chamber pair 排序
```

重点是第 3 步。只要不展开到 chamber，后面的 matching 和 seasoning 都只能靠人工。

## Matching 分层

| 层级 | 策略 |
|---|---|
| Critical | 固定 chamber 或固定 matching group |
| Semi-critical | 优先原 group，异常时受控放开 |
| Normal | 按负载、setup、ETA 优化 |

Matching 不应永久写死。每个限制要有原因、复核周期和临时放开机制。

## 常见建模错误

| 错误 | 后果 |
|---|---|
| tool 可用 = chamber 可用 | Post-PM chamber 被误派 |
| chamber qualification 存在 Excel | 系统推荐长期被人工覆盖 |
| matching group 不带版本 | 工艺更新后规则不可追溯 |
| 不记录被过滤候选 | 不知道产能损失来自哪里 |

---

相关：[PM/Clean/Seasoning](02-PM-clean-seasoning.md) · [Recipe Family 与污染序列](03-recipe-family与污染序列.md)
