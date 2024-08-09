---
title: TIM-编码器
date: 2024-01-29 22:56
tags: 
mathjax: "false"
banner: /images/thumbs/
excerpt:
---

## 编码器接口

通过读取正交编码器旋转产生的方波信号，判断编码器的旋转速度、位置、方向等。

> 正交编码器：产生两个相位相差 90° 的方波信号。

STM32 的通用定时器和高级计时器提供编码器接口功能，只在 CH1，CH2 通道支持。原理是把编码器产生的方波信号等效为外部时钟，使 `CNT` 对不同的方波信号自增或自减。

旋转速度越快，方波频率越高，可以通过测频法测量速度；通过正交编码器正反转产生的两个方波的相位的关系，可以判断旋转方向；通过读取 CNT 的值，可以判断旋转位置。

### 代码

![](TIM-Encoder_img_1.png)
<p style="font-size: 13px" align = "center">配置结构图</p>
开启GPIO；TIM初始化为`IC`即可。
- `TIM_EncoderInterfaceConfig()`
	配置TIM为编码器接口模式。

> 需要选择接在 `TIM_CH1` 与 `TIM_CH2` 的 GPIO。
> TIM 的预分频 `Prescaler` 值取 0，`ARR` 取 65535-1，

测速时，每隔一段时间 T 读取一次 CNT 的值 N，$Speed=CNT/T$。
