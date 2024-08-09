---
title: 
date: 2024-03-28 15:33
tags: 
mathjax: "false"
banner: /images/thumbs/
excerpt:
---

### 中断

![](MessyBox_img_1.png)
使用前，EA 置 1，需要使用的中断置 1。
同时配置中断触发方式：
![](MessyBox_img_2.png)
`EX0` 对应 `INT0-P32`, `EX1` 对应 `INT1-P33`
![](MessyBox_img_3.png)
最后的初始化函数为：

```C
void InterInit()//使能外部中断0
{
	EA=1;
	EX0=0;
	IT0=0;
}
```

使用关键字 `interrputx` 来指定中断函数：

```C
void InteruptFun() interrupt0
{
	...
}
```

### 串口通信

读写 SBUF；
有标志位：`RI` `TI`，需要手动清零
接收数据中断：`interrupt 4`
需要补全：`EA=1 ES=1`

> ES 为串口中断寄存器

### Py
`ord()` 将ascii转化为数字
`chr()` 将数字转化为ascii

### DS18b20
读取例程
```C
	Init_DS18B20();            
    Write_DS18B20(0xCC);            //复位
    Write_DS18B20(0x44);            //温度转换
    Delay(1000);
    Init_DS18B20();    
	Write_DS18B20(0xCC);        //复位
    Write_DS18B20(0xBE);            //读暂存器数据
    LSB = Read_DS18B20();      
    MSB = Read_DS18B20();          
    Init_DS18B20();
```
![](MessyBox_img_4.png)
<p style="font-size: 13px" align = "center">寄存器解释</p>
高5位都是符号位，为1代表为负，为0代表为正。只显示整数的话，只考虑到`BIT4`即可。

### 数码管

- onewire正确读取
- 数码管显示
- delay里面控制数码管
- 带`.`: 原段码-8

### adc

查阅`functioning`
- 先start，然后传0x90(W)/0x91(R)
- 再传需要读写的channel(1/3)
- 再读取/写入
每个数据包之间，发送方需要`wait ack`，接收方需要`send ack` 

停止接收以后，需要`send ack 1(非应答)`

### AT24c02

`address: 0xa0(w)/0xa1(r)`
存储地址从0开始, 8位1页
