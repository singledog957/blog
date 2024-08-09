---
title: DMA
date: 2024-02-13 19:09
tags:
  - STM32
mathjax: "false"
banner: /images/thumbs/
excerpt:
---

DMA(Direct Memory Access) 可以直接在外设和存储器之间转运数据, 无需 CPU 干预.

DMA 共有 7 个通道, 可以通过硬件触发、软件触发.

### DMA 转运

![](DMA_img_1.png)
<p style="font-size: 13px" align = "center">STM32中的存储器</p>
DMA可以在以上存储器之间复制传输数据。
![](Pasted%20image%2020240215161601.png)
<p style="font-size: 13px" align = "center">传输框图</p>
给出部分参数解释：
- 数据宽度：字节`uint8_t`、半字`uint16_t`、字`uint32_t`
>	针对stm32的32位cpu
- 地址是否自增：传输完一次之后，指针是否自增。
- 传输计数器：每传输一次，计数器自减1。自减到0时停止DMA传输。
- 自动重装器：传输计数器置0后，重置计数器的值。
- M2M：控制触发方式为硬件触发或软件触发。

#### 地址是否自增

![](DMA_img_2.png)
<p style="font-size: 13px" align = "center">转运数组</p>
如图所示时，DMA需要依次转运DataA的每一个元素到DataB，故DataA, DataB地址都需要自增；
![](Pasted%20image%2020240215162607.png)
<p style="font-size: 13px" align = "center">ADC+DMA</p>
如图，DMA需要依次将ADC_DR的值转运到ADValue中，且不同通道存储在ADValue的不同位置，故ADC_DR (Source端) 地址不用自增，ADValue需要自增。
>使用DMA+ADC时，每一个ADC通道转换完成后都会触发DMA转运，无需配置标志位等。
