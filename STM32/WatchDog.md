---
title: 
date: 2024-06-03 22:12
tags: 
mathjax: "false"
banner: /images/thumbs/
excerpt:
---

## 看门狗

看门狗 (watchdog) 是一个能产生复位信号的计数器。在计数到特定值后，程序会被复位。可以在计数到指定值之前重置计数器，实现监控程序是否跑飞的目的。这一操作被称为 `喂狗`。

stm32 有 `独立看门狗(IWDG)` 与 `窗口看门狗(WWDG)`。
独立看门狗的时钟由外部 40Khz 提供，可能漂移，适合对时间精度要求不高的场景。

看门狗的计数是递减的，复位发生在计数器的值减到特定值时。对于独立看门狗，这个值是 0；对于窗口看门狗，这个值是 0x40。
二者共同需要配置
- 时钟预分频
- 递减计数器的值
对于窗口看门狗，其还有一个 `上限值W`。只有在 $[0\text{x}40,W]$ 内喂狗，才是有效的。

在计数器的值递减到 0x40 时，可配置产生中断，然后再复位。

产生复位的条件：
1. 当递减计数器的值小于 0x40，(若看门狗被启动) 则产生复位。
2. 当递减计数器在窗口外 ($[0\text{x}40,W]$ 之外) 被重新装载，(若看门狗被启动) 则产生复位。

产生中断的条件：启动了看门狗并且允许中断，当递减计数器等于 0x40 时产生早期唤醒中断 (EWI)。中断程序执行的时间由WDGTB的值决定。