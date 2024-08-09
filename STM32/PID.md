---
title: 
date: 2024-02-29 16:10
tags: 
mathjax: "false"
banner: /images/thumbs/
excerpt:
---

## Introduction

PID (proportion integration differentiation) 控制算法，适用于对系统没有精确建模的控制系统。基本思路就是闭环控制。
假设我们需要一个水池的水位达到 10m，现在我们通过控制入水水管的流速来达到目标。假设当前水位为 `x`m，引入变量 `err=x-10`。显然 `err` 与时间 `t` 相关。
给出 PID 的数学公式：
![](PID_img_1.png)

### Proportion

Proportion，比例。其将 err 乘上一个比例系数 $K_p$，然后直接施加作用。这里令 K_p=1。
当 t=0 时，err=10，则调节水位上升速度到 $10m/s$;
t=1 时，err=9，下一步水位上升速度变为 $9m/s$。
如此重复，当 $t \to \infty$，$err \to 0$。

### Integral

现在假设水池在以漏水，水位下降速度 $1m/s$。
当 err=1 时，设定水位上升速度 V=1m/s，刚好与漏水速度抵消，则水位就保持在 9m。
这一误差被称为稳定误差。I 项的引入消除了稳定误差。
Integral，积分。其累加过去的 err 值，乘上 $K_i$ 后施加作用。
引入 I 项后，当 err=1 时，I 项值不为 0，则水池水位此时受 I 控制而上升，直到达到 10m。

### Differentation

阻尼，整体控制水位上升的速度。可以一定程度上限制积分饱和现象。
