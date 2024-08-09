---
title: 
date: 2024-02-24 16:47
tags: 
mathjax: "false"
banner: /images/thumbs/
excerpt:
---

## SPI

SPI (Serial Peripheral Interface) 是一种同步、全双工的通信总线, 由 4 根通信线组成：
- `SCK(Serial CLK)`
	时钟同步线
- `MOSI(Master Output Slave Input)`
	主机输出，从机输入线
- `MISO(Master Input Slave Output)`
	主机输入，从机输出线
- `SS(Slave Select)`
	从机选择线
SPI 支持一主多从，在一条总线上挂载多个设备。每个设备 SCK、MOSI、MISO 相连。另外引出多条 SS 控制线，分别接到各从机的 SS 引脚。

主机指定与某个设备通信时，将对应设备的 SS 拉低即可。

由于无需半双工，输出引脚均配置为推挽输出，输入引脚为浮空或上拉即可。

### 基本单元

SPI 的通信通过 `移位` 操作实现，类似循环链表。通信时，主机的最高位移动到从机的最低位，以此循环往复 8 次，即可实现二者寄存器值的交换。
![](SPI_img_1.png)
这一步骤的基本单元由起始位、数据、终止位组成。与 I2C 不同，SPI 无应答。
- 起始位，将 SS 拉低即可。
- 终止位，将 SS 拉高即可。
- 八位数据

> 数据交换由采样位置的 2 种情况、交换发生的极性的2种情况，组合出现 4 种模式。

数据的交换只在 SCK 的上升沿或下降沿发生。一次 `移位` 分为两步：
	1. 将自身的寄存器的值移入 (采样)。发生在 SCK 下降沿 (奇数跳变沿) 。
	2. 移出数据 (传输)。在 SCK 上升沿 (偶数跳变沿)。
![](SPI_img_2.png)
<p style="font-size: 13px" align = "center">模式 0 (CPOL=0, CPHA=0)</p>
- CPOL控制空闲时，SCK的极性
- CPHA控制采样在第一个(CPHA=0)或第二个跳变沿(CPHA=1)。

![](SPI_img_3.png)

## W25Qxx

## 代码
stm32有两个SPI外设, 基本使用思路与软件模拟SPI相似。
在频率较高时，应使用连续传输以提高效率。
一般情况使用非连续传输即可。