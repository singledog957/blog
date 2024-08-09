---
title: TIM-PWM与OC、IC
date: 2024-01-12 20:20
tags:
  - STM32
mathjax: "true"
banner: /images/thumbs/
excerpt:
---

## 输出比较&输入捕获（OC&IC）

输出比较 (Output Compare) 可以通过比较 CNT 与 CCR(Capture/Compare Register) 寄存器值的关系，来对输出电平进行置 1、置 0 或翻转的操作，用于输出一定频率和占空比的 PWM 波形。

输入捕获 (Input Compare) 可以检测输入引脚的电平变化，当电平出现跳变时，当前 CNT 的值将被锁存到 CCR 中，可用于检测 PWM 的频率、占空比等参数。

### PWM

PWM 是一种调制技术，通过对一系列脉冲的宽度进行调制，等效地获得模拟参量。只适用于惯性系统。

> 类似于对函数进行级数展开近似的思想，只是这里的基本单位视作 0-1 电平持续的长度。

有如下几个参数：
- 频率
$$F=\frac{1}{T_s}$$
$T_s = T_{ON}+T_{OFF}$ ，即高低电平变换的时间。频率越快，模拟的信号越平稳，模拟效果越好。
- 占空比
$$D=\frac{T_{ON}}{T_{s}}$$
决定了模拟出来的电压的大小。如 5v 高电平，0v 低电平，占空比 20%，则等效于 1v 电压。
- 分辨率
占空比变化的间隔。如分辨率为 1%，就是占空比以 1% 为步长变化。

### 输出模式控制器

在 STM32 中，PWM 输出主要是通过输出模式控制器来实现的。

输出模式控制器用于比较定时器 CNT 寄存器与 CCR 的值，并根据其值的不同来给出不同的输出信号。

用于 PWM 输出时，常用 `PWM模式1`。基本逻辑如下：
![](TIM_PWM_img_1.png)
同时，当 CNT 值到达 ARR 值时，CNT 清零，一个周期结束。
![](TIM_PWM_img_2.png)
<p style="font-size: 13px" align = "center">图中黄线为ARR，红线为CCR</p>
可以通过调整CCR的值，来使波形的占空比变化。
给出PWM相关参数的计算公式：
$$F = \frac {CK\_PSC} {(PSC + 1) (ARR + 1)}$$
$$D=\frac{CCR}{ARR+1}$$
$$Resolution=\frac{1}{ARR+1}$$
注意到，$F$ 其实就是$CNT$的溢出频率。

### 舵机与电机

舵机的驱动使用 PWM 进行。通过驱动板，不同的 PWM 波形对应舵机不同的角度。在这里，PWM 起到信号的作用。
电机的驱动则由 PWM 直接映射，不同的 PWM 通过驱动板对应不同的电机转速和方向。

### 输入捕获 (IC)

#### 频率测量

- 测频法：测量一段时间 T 内上升沿出现的次数 $N$，则可得信号的频率 $$f_x=\frac{N}{T}$$
- 测周法：测量两个上升沿之间有 N 个标准频率 $f_c$ 。则
$$f_x=\frac{f_c}{N}$$

> 测周法等效于测量信号的周期，适用于低频信号；测频法适用于高频信号。

#### 测周法

![](TIM_PWM_img_3.png)
<p style="font-size: 13px" align = "center">结构图</p>
每读到一个上升沿，将当前 CNT 的值转存至寄存器中，同时对 CNT 清零。

> 清零操作使用主从触发模式完成。

同时，还有 `PWMI` 模式，使用另一个通道捕获下降沿，从而得到高电平持续时间，得到占空比。

#### 测频法

使用定时器固定一段时间 T，利用外部中断在 T 内检测上升沿出现的次数 N，即可得。

> 每出现一次上升沿就要中断一次，是否对资源要求较高？

### 代码

只写一个大概的逻辑和常用的几个函数。

基本就是配置 TIM、OC、GPIO。
TIM 基本配置与之前一致。基本配置完成后再单独配置OC。

#### OC

![](TIM_PWM_img_4.png)
<p style="font-size: 13px" align = "center">OC结构图</p>
GPIO 需要配置为复用推挽输出，将控制权交给片上外设。

基本时基单元函数与之前一致。

- TIM_OCxInit()
	用于初始化 OC 模块。

- TIM_OCStructInit()
	给 Init 结构体赋初始值。

- TIM_SelectOCxM()
	选择输出比较模式。

- TIM_SetComparex()
	单独更改 CCR 寄存器值的函数。

给出通用定时器 InitStruct 的几个成员解释：

```C
TIM_OCMode//选择输出比较模式
TIM_OutputState//输出使能
TIM_Pulse//设置CCR的值。16位INT。
TIM_OCPolarity//输出比较极性。
```

> 端口复用 (AFIO)
> 将一般的 GPIO 复用为内置外设的功能引脚。

#### 测频法

GPIO 配置为浮空输入或上拉输入，TIM 如下。

- TIM_ICInit()
	与 OC 不同，ICInit 只有一个初始化函数，具体哪一个通道在参数中选择。
	下面给出初始化结构体的成员解释：

```C
TIM_Channel//选择Init的通道
TIM_ICFilter//输入捕获的滤波器
TIM_ICPolarity//选择上升沿/下降沿触发
TIM_ICPrescaler//触发信号的分频器
TIM_ICSelection//选择直连输入/交叉输入
```

- TIM_PWMIConfig()
	配置 `PWMI` 模式。

- TIM_ICStructInit()
	给初始化结构体赋一个默认值。

- TIM_SelectInputTriger()
	选择从模式的触发源。

- TIM_SelectOutputTrigger()
	选择输出的触发源。

- TIM_SelectSlaveMode()
	从模式选择。

- TIM_SetICxPrescaler()
	配置四个通道的分频器。

- TIM_GetCapturex()
	读取对应通道的 CCR 的值。
