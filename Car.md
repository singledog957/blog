---
title: WHEEL TECH平衡小车大作业
date: 2023-11-22 22:37
tags:
  - STM32
mathjax: "true"
---


你说得对，但是 `单片机基础与应用` 使用了 `WHEEL Tech` 的小车，一块板子 50，一套车 500，开价足以救活嵌入式行业。

原作者：`WHEEL Tech`

感谢：`joker`。

本教程适用于：没有蓝牙、OLED 、超声波模块的小车，封装的函数足以完成大作业。

> 其实什么都没有写，只是把蓝牙控制的 `Flag` 拿出来用了而已。

[在这里](https://pan.singledog233.top/d/tmp/3.WHEELTEC%20B570%20%E5%B9%B3%E8%A1%A1%E5%B0%8F%E8%BD%A6%E6%BA%90%E7%A0%81%EF%BC%88%E5%BA%93%E5%87%BD%E6%95%B0%E7%89%88%EF%BC%89.zip) 下载打开工程即可使用。请在 `MiniBalance.c` 的 `main` 函数中的 `while(Flag_go)` 块中修改。

## 使用说明

> --- 231124 Update:
> `forward_dist()` 暂时可用了。
> 新增了 `set_velocity()` 函数。

接通小车电源后，请扶住小车一段时间，直到小车开始自平衡。

- `Flag_go`
控制小车行为的标志，默认为 0，小车在原地保持平衡。按下 `USER` 键后，`Flag_go` 置 1，小车执行设定的路径。
再次按下 `USER` 键，`Flag_go` 置 0，小车停止运行，仅在原地保持平衡。

下面其实都是废话，能看懂英语就会用了。

- `set_velocity(float setVe)`
调整小车运行速度，默认值为 `50`，范围在 `0-100`。

> 单位是什么？我也不知道。
> 前进速度较大时，请在每个动作之间添加 `stand(100)`，以保证距离的准确度。

- `stand(int time)`
小车原地保持平衡，平衡时间 `time`ms。

- `forward_time(int time)`
控制小车前进，前进时间 `time`ms。

- `forward_dist(int dist)`
控制小车前进，前进距离 `dist`cm 。

- `backward_time(int time)`
控制小车后退，后退时间 `time`ms。

- `backward_dist(int dist)`
控制小车后退，后退距离 `dist`cm 。

> `forward_dist()` 与 `backward_dist()` 只保证在 `0-130`cm 内有 $\pm 10 cm$ 的误差
> 超过这一距离时，请使用 time 估计。
> 使用这两个函数时，小车第一次通电也许不能开始自平衡。
> 请断开小车电源再接通，也许就正常了。

- `turnLeft()`
小车左转 90°。

- `turnRight()`
小车右转 90°。

> 转向函数也许存在 BUG，转角不正确时请在函数的定义中把 `while (time < 100 && Flag_go)` 中的 `100` 改小。

> 为什么小车越走越歪？大概是轮子没装对称。

> forward_dist() 函数目前还没有调出来，希望有佬能帮个忙。

大概期末之前都不会有更新了。
