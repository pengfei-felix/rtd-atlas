---
title: CONWIP / CONLOAD / LCONWIP
---

# CONWIP / CONLOAD / LCONWIP

## 三种控制机制

| 机制 | 控什么 | 适合 |
|---|---|---|
| CONWIP | 总 WIP 数 | routing 稳定、产品相对同质 |
| CONLOAD | 标准工时负荷 | 产品 mix 变化、加工时间差异大 |
| LCONWIP | 分层 / 分段 WIP | 高重入、多层次加工失衡 |

## CONWIP

```text
当成品 / 指定出口释放一个单位，入口才允许 release 一个单位。
```

优点是简单稳定，缺点是不同产品工时差异大时，一个 Lot 不等于一个 Lot。

## CONLOAD

```text
控制瓶颈负荷 = Σ(候选 release 的标准工时)
```

适合半导体多产品混线，因为不同产品、不同层别对瓶颈消耗不同。

## LCONWIP

按 layer、光刻次数或关键流程段分配 WIP cap，避免前段过多、后段饥饿。

| 分层方式 | 用途 |
|---|---|
| by litho layer count | 控制重入流层间配平 |
| by route segment | 控制瓶颈前后 WIP |
| by Q-time corridor | 控制时间窗入口 |

## 与 RTD 联动

Release control 决定“进多少”，RTD 决定“怎么流”。两者必须共享：

- WIP cap；
- 瓶颈 buffer；
- Q-time 入口容量；
- Hot Lot 配额；
- effective capacity。

---

相关：[Release 与 RTD 联动](03-release与RTD联动.md)
