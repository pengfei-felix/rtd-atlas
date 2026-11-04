---
title: Release 与 RTD 联动
---

# Release 与 RTD 联动

<div class="keyline" markdown>

**投料控制不好，RTD 会变成消防队。** 过量 release 制造 Q-time 红区，过少 release 饿死瓶颈；两者都不是单次派工能解决的。

</div>

## 联动接口

| Release 给 RTD | RTD 回 Release |
|---|---|
| WIP cap | 实际 WIP 与堵塞点 |
| release priority | 派工执行偏差 |
| Q-time entrance cap | 走廊拥堵和违规归因 |
| planned mix | effective capacity |
| Hot Lot quota | Hot Lot 影响面 |

## Hot Lot 配额

Hot Lot 不是越多越好。

| 指标 | 说明 |
|---|---|
| hot lot share | Hot Lot 占总 WIP 比例 |
| displacement time | Hot Lot 对普通批造成的等待 |
| priority inversion | 普通批长期无法获得资源 |
| recovery time | Hot Lot 波峰后系统恢复时间 |

## 反模式

| 反模式 | 后果 |
|---|---|
| 交期紧就无限 release | 全厂 CT 拉长 |
| APS 下死顺序给 RTD | 现场异常后顺序失效 |
| RTD 覆盖不回传 | APS 继续用错误产能假设 |
| 只控全厂 WIP | 区域 WIP 失衡 |

## 落地路线

1. 建 WIP cap 表；
2. 建 Q-time corridor cap；
3. RTD 记录 override reason；
4. 每班回传 effective capacity；
5. 每周调整 release policy。

---

相关：[APS / RTS / RTD 闭环](../aps-rts-rtd-loop.md)
