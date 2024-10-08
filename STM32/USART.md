---
title: 
date: 2024-02-15 16:30
tags: 
mathjax: "false"
banner: /images/thumbs/
excerpt:
---
## 串口通信
在设备之间相互通信的接口。stm32支持如下协议：
![](USART_img_1.png)

### 基础概念
- 全双工：发送接收由两条线路完成，可以同时发送、接收，互不影响。
- 半双工：只能发送或接收
- 单工：只能单向传输数据。
- 同步：传输双方提前约定好时钟信号
- 异步：不依赖时钟，使用起始位等确定边界
- 单端：传输双方需要共GND，通过高低电平确定01
- 差分：根据两个引脚的电平差确定01

### 串口参数

- 波特率：传输的速率。传输为二进制时，单位可以视作`bit/s`
- 起始位：标志数据的开始，固定为低电平
- 数据位：传输的数据，从低位开始
- 校验位：校验数据的准确性。
>	奇校验：保证数据中有奇数个1。如8位数据发送`0000 1111`，则校验位为1，最后发送`0000 1111 1`，使数据中有5个1。偶校验同理。
- 停止位：标志数据的结束，固定为高电平

## USART
通用同步/异步收发器，可以实现串口通信。

stm32有3个usart。

- 数据帧
	可设置8位字长、9位字长。一般选择8位无校验、9位有校验。
- 噪声处理
	对每一位有标志位NE，如果检测到可能出现噪声，对NE置1。
- 标志位
	发送寄存器标志位和接收寄存器标志位，需要对其检测来判断每次数据发送是否完成。

### 数据包
由多个字节组成的连续的一组数据。
#### 格式
可使用固定包长、可定包长等，指定包头包尾。
>也可只含包头/包尾，格式灵活

包头、包尾应尽量不和数据重复。 
#### 接收
![](USART_img_2.png)
<p style="font-size: 13px" align = "center">状态转移图</p>
针对不同的状态执行不同的代码，同时给出每个状态之间转移的条件，这是状态机的思想。
数据包发送过快时，可能会出现前后两个数据包混合错位的情况。可以添加flag，在处理完成后再接收下一个数据包来避免这种情况。
### 代码
使用usart1，rx对应pa10，tx对应pa9。
rx一般配置为浮空或上拉。
>出现下拉时代表起始位。

接收时，可以使用查询、中断两种方式。
查询即不断查询RXNE标志，为1代表收到数据。
中断，即使用NVIC转存数据，适用数据包收发。