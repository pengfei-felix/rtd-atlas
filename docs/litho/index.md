---
title: Litho 区派工档案
---

# Litho 区派工档案

**Litho 是全厂的物流配送中心。** 同一片晶圆要回到这里 18~40 次，队列来自所有层、所有产品；真正稀缺的不是设备本身，而是 Reticle 和机台的温层状态。 <span class="badge badge--done">1.0</span>

## 1 工艺与工序流

### 工艺内容

涂胶 → 曝光 → 显影，把 Reticle 上的图形转印到光刻胶上，成为下一道刻蚀或注入的掩膜。光刻不增材也不减材，只决定图形的形状与位置，精度是纳米级套刻。

一片晶圆在量产里要回到这里 18~40 次；先进节点同一层还要多次曝光（LELE / SADP），所以层内也会重入。8 寸多用接触式 / 接近式曝光或 i-line 步进，Track 也有手动与半自动的；12 寸是步进扫描加全自动 Track。

### 工序

除曝光外，其余五步都在 Track 上：

<div class="flowloop">
  <div class="flowline">
    <div class="flow-step"><strong>增粘/涂胶</strong></div>
    <div class="flow-arrow">→</div>
    <div class="flow-step"><strong>前烘</strong></div>
    <div class="flow-arrow">→</div>
    <div class="flow-step"><strong>曝光</strong></div>
    <div class="flow-arrow">→</div>
    <div class="flow-step"><strong>后烘</strong></div>
    <div class="flow-arrow">→</div>
    <div class="flow-step"><strong>显影</strong></div>
    <div class="flow-arrow">→</div>
    <div class="flow-step"><strong>坚膜</strong></div>
  </div>
  <div class="flowloop-return">曝光在 Scanner；其余五步在 Track</div>
</div>

- **增粘 / 涂胶**：先做 HMDS 气相增粘，再旋涂光刻胶。转速与胶黏度一起决定胶厚。
- **前烘（PAB）**：把胶里的溶剂烘掉、稳定胶膜，温度和时间会影响后续感度。
- **曝光**：Scanner 按 Reticle 图形曝光，剂量与焦深决定最后能画出多细的线。
- **后烘（PEB）**：加热让光酸扩散，这一步直接决定 CD（线宽）。
- **显影**：碱液洗掉曝光过的区域（正胶），图形成形。
- **坚膜（Hard bake）**：固化胶膜、提高抗刻蚀能力，然后进刻蚀。

量测在显影之后插入（CD / 套刻 / 缺陷），归 Metrology 区。

### 派工特点

| 特点 | 落在哪一步 | 不管会怎样 |
|---|---|---|
| **曝光后延迟（PED）** | 曝光 → 后烘 | 酸扩散过深、CD 漂移；超时只能返工，而返工要重走一遍涂胶曝光 |
| **涂胶后延迟** | 前烘 → 曝光 | 溶剂残留、感度漂移，胶厚和 CD 一起偏 |
| **Reticle 独占** | 全程 | Reticle 锁在别的机台、还在搬送途中，本机只能空转；报表上表现为假性 idle |
| **换层 setup** | 曝光 | 换 Reticle 与照明条件要重新整定，matching 不到的机台上不了，候选集被压窄 |
| **层内重入** | 同层多次曝光 | 队列来自全厂所有层，排完一轮又回来；按单站算负荷会失真 |
| **CD 反馈（R2R）** | 量测 → 下一批曝光 | 参数没跟着上一批的量测结果改，整批 CD 偏移，返工也救不回来 |

### 上下游

- **上游**：清洗出槽 → 本区。全厂大部分 Q-time 走廊从出槽那一刻开始计时。
- **下游**：刻蚀或注入，各自再带一段 Q-time。
- **横切**：Metrology 在显影后取数（CD / 套刻 / 缺陷）；Sorter / Stocker 管载具。

## 2 专有派工功能

12 项，按影响程度排：先红线（校验），再可行性（约束），最后效率与协调（策略 / 接口）。其中 7 项围着 Reticle 和温层转——**这个区的差异点不在设备，在辅助资源**。

| 功能 | 类型 | 功能描述 | 解决什么问题 |
|---|---|---|---|
| Reticle Status Check | 校验 | 查 Reticle 的 startlight 与 printdown 检验，wafer count 或使用时长到上限就剔除该罩，对应 Lot 不派 | 对应「Reticle 独占」：用了不该用的罩，整批报废或返工 |
| Litho R2R Check | 校验 | 派工前查 APC R2R 是否 block 该 Lot | 对应「CD 反馈」：R2R 说这批参数要改，硬派就是一批废片 |
| Domapath | 校验 | Domapath 失败时把 Lot Hold 住，等工程师处理 | 数据约束，不属工艺特点：路径没算出来就派，现场跑错工序 |
| 垂直机限 | 约束 | 同一 Lot 在上一层用了哪台机，下一层必须跑同一台 | 对应「层内重入」：跨层同机是硬约束，不满足就排不出可行解 |
| Reticle 机限 / 高低温切换 | 约束 | 按光刻版连续作业、前后站机台限定、高低温切换过滤可用机台 | 对应「Reticle 独占 / 换层 setup」：能上罩的机台本就少，切换再把候选集压窄 |
| Reticle Train | 策略 | 派工时看上一批用的是哪块 Reticle，同罩的 Lot 优先，把换罩次数压到最低 | 对应「Reticle 独占 / 换层 setup」：每换一次罩就是一次锁定加一次整定，队列全在等罩 |
| Temperature Train | 策略 | 识别机台当前温度状态，优先派不需要升降温的 Lot，减少热整定 | 对应「换层 setup」：每次升降温是一段固定损失，期间机台接不了别的活 |
| 群组 Loading 均衡 | 策略 | 在 Litho 机台群组内算 WIP、机台 / recipe / ppid / MES 约束，把 load 摊平 | 对应「层内重入」：队列来自全厂所有层，不摊平就有机台被某一层堵死 |
| Chuck 排货 | 策略 | 按 Chuck 的顺序与状态派货，避免频繁切 Chuck | 对应「换层 setup」：Chuck 切换是隐性 setup，报表上看不出来 |
| 设定 Lot Size | 策略 | 避免连续派小片数 Lot，减少小批吃掉的设备时间 | 对应「层内重入」：小批连排等于把产能切碎，大 Lot 被挤到后面 |
| Reticle Change Cost | 策略 | 把 Reticle 从 Stocker 到机台的搬送时间或距离折成成本，优先派搬送代价小的 Lot | 对应「Reticle 独占」：罩在远处，机台只能空等，报表上是假性 idle |
| Reticle List | 接口 | 提前给出每台机未来要用的 Reticle 列表，便于人或搬送系统先把罩送到位 | 对应「Reticle 独占」：开工才发现缺罩，机台白等 |

## 3 指标与落地

- [指标体系](05-指标体系.md) — 用哪几个数判断这个区跑得好不好
- [落地路线图](06-落地路线图.md) — Phase 0–4 的上线顺序
- [避坑清单](08-避坑清单.md) — 上线前的最小检查
- [附录](09-附录.md) — 公式骨架与参考来源
