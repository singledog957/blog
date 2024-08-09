---
title: GPIO
date: 2023-10-08 13:33
tags:
  - STM32
---

STM32 的 GPIO 口一共有八种输入输出模式，输出范围为 0~3.3v，部分输入可容忍 5v 电压。
![](GPIO_img_1.png)
<p style="font-size: 13px" align = "center">GPIO对应表</p>
>不要使用`PA12`。该io默认上拉，不受控制。
>建议是确定自己代码没问题，而且出现意料外情况的时候，换io口吧。

### 输入模式

1. 上拉输入
   悬空时默认高电平
2. 下拉输入
   悬空时默认低电平
3. 浮空输入
   电平不确定
4. 模拟输入

### 输出模式

1. 推挽输出
   高低电平都有驱动能力
2. 开漏输出
   高电平为高阻态，没有驱动能力
3. 复用开漏输出
4. 复用推挽输出
   两种复用输出不通过数据寄存器，直接通过外设进行控制。

### GPIO 的种类与速度

根据连接到的模块的不同，GPIO 被命名为 GPIOA,GPIOB 等，在功能上没有明显区别。**每个模块都连接到 APB2 总线上**。GPIO 引脚的速度表示该引脚翻转的次数，一般配置为 50Mhz 即可。

## GPIO 库

### 初始化

- `GPIO_InitTypeDef`

用于 GPIO 初始化的一个结构体。

```C
GPIO_InitTypeDef GPIO_InitStructure;
```

有三个成员：

- `GPIO_Pin`
    指定要配置的引脚。

```C
GPIO_InitStructure.GPIO_Pin = GPIO_Pin_0;
```

多个引脚需要配置时，可以使用：

```C
GPIO_InitStructure.GPIO_Pin = GPIO_Pin_0 | GPIO_Pin_1 | ...;
```

> 为什么？
> GPIO_Pin_x 本质是十六进制的宏定义，使用 `|` 操作符可以取出这些十六进制的并集。
> 其他需要选择多个引脚的地方也可以这样使用。

- `GPIO_Mode`
    控制 GPIO 的工作模式。有八种模式可选：

```C
浮空输入(GPIO_Mode_IN_FLOATING）
上拉输入(GPIO_Mode_IPU)
下拉输入(GPIO_Mode_IPD)
模拟输入(GPIO_Mode_AIN)

开漏输出(GPIO_Mode_Out_OD)
开漏复用功能(GPIO_Mode_AF_OD)
推挽输出(GPIO_Mode_Out_PP)
推挽复用功能(GPIO_Mode_AF_PP)

Example:
GPIO_InitStructure.GPIO_Mode=GPIO_Mode_Out_PP;//配置为推挽输出
```

- `GPIO_Speed`
    配置引脚的速度。

```C
GPIO_InitStructure.GPIO_Speed=GPIO_Speed_50MHz;
```

- `GPIO_Init(GPIO_TypeDef* GPIOx, GPIO_InitTypeDef* GPIO_InitStruct)`
    初始化 GPIO 引脚的函数。STM32 中，许多初始化函数都以 `Init` 结尾。

```C
GPIO_Init(GPIOA, &GPIO_InitStructure);//第二个参数传入地址
```

- `GPIO_DeInit(GPIO_TypeDef* GPIOx)`
    复位指定的 GPIO。参数为需要复位的引脚。

### 输出

- `GPIO_SetBits(GPIO_TypeDef* GPIOx, uint16_t GPIO_Pin)`
    将指定引脚设置为高电平。

```C
GPIO_SetBits(GPIOA,GPIO_Pin_0);
```

- `GPIO_ResetBits(GPIO_TypeDef* GPIOx, uint16_t GPIO_Pin)`
    将指定引脚设置为低电平。
    参数与 `GPIO_SetBits()` 一致。

- `GPIO_WriteBit(GPIO_TypeDef* GPIOx, uint16_t GPIO_Pin, BitAction BitVal)`

前两个参数与 `GPIO_SetBits` 一致。`BitAction` 可选：

```C
Bit_SET//置高电平
Bit_RESET//置低电平

Example:
GPIO_WriteBit(GPIOA,GPIO_Pin0,Bit_SET);
```

也可以直接写为：

```C
GPIO_WriteBit(GPIOA,GPIO_Pin0,BitAction(1));//等效于GPIO_WriteBit(GPIOA,GPIO_Pin0,Bit_SET);
GPIO_WriteBit(GPIOA,GPIO_Pin0,BitAction(0));//等效于GPIO_WriteBit(GPIOA,GPIO_Pin0,Bit_RESET);
```

### 输入

- `GPIO_ReadInputDataBit(GPIO_TypeDef* GPIOx, uint16_t GPIO_Pin)`
    读取输入数据寄存器中某一个端口的输入值。
    参数为 `(GPIOX,GPIO_Pin_X)`。
    返回值类型为 `uint8_t`，代表该端口的高低电平。
- `GPIO_ReadInputData(GPIO_TypeDef* GPIOx)`
    读取整个输入数据寄存器，参数为 `(GPIO_X)`，用来指定外设。
    返回值为 `uint16_t`，是 16 位的数据，每一位代表一个端口值。
- `GPIO_ReadOutputDataBit(GPIO_TypeDef* GPIOx, uint16_t GPIO_Pin)`
    读取输出数据寄存器的某一位。一般用于输出模式下，获取端口的输出。
    参数为 `(GPIOX,GPIO_Pin_X)`。
    返回值类型为 `uint8_t`，代表该端口的高低电平。
- `GPIO_ReadOutputData(GPIO_TypeDef* GPIOx)`
    读取整个输出数据寄存器，参数为 `(GPIO_X)`，用来指定外设。
    返回值为 `uint16_t`，是 16 位的数据，每一位代表一个端口值。

### GPIO 实战 -- 按键点亮 LED

初始化过程就不一一列出了，主要想说一说按键检测的逻辑。

先给出 `main.c` 的内容：

```C
int main(void)

{

    LED_Init();   // 调用初始化 LED 函数，引用"led.h"后可使用

    delay_init(); // 调用初始化延迟函数，引用"delay.h"后可使用

    KEY_Init();

    u8 step = 0;

    while (1)

    {

        step += KEY_Scan();

        step %= 2;

        if (step)

            GPIO_SetBits(GPIOA, GPIO_Pin_4);

        else

            GPIO_ResetBits(GPIOA, GPIO_Pin_4);

    }

}
```

重点在于 `KEY_Scan()` 函数的实现。

定义 `KEY` 为输入引脚的状态，即:`#define KEY GPIO_ReadInputDataBit(GPIOA,GPIO_Pin_5)`

使用下拉输入模式，`KEY` 的值默认为 0。

我们需要读取一次完整的按键按下 - 松开的过程，这一过程中按键有两个状态，我们应该基于此做如下分析：

1. 状态一：按键未被按下
由于 `KEY_Scan()` 函数被不断循环执行，所以 `KEY` 未改变时，应默认返回 0。
2. 状态二：按键被按下
按键被按下以后，`KEY` 的值改变且恒为 `1`；此时可以返回 1，表示按键被按下。

但是此时按键未松开，**完整的按键过程还未结束**，所以不能直接回到状态一。

应当继续检测按键状态，直到其松开，再回到状态一。因此，我们需要一个 `static flag`，记录上次调用函数时按键的状态；回到状态一的条件即 `flag == 0 && KEY == 1`。

此外，还可以利用 `flag` 优化读取逻辑，只在 `flag != KEY` 时消抖，可以减少 `delay_ms()` 带来的阻塞时间。

> 其实 `delay_ms(10)` 的优化聊胜于无吧

因此，我们给出如下代码：

```C
uint8_t KEY_Scan()

{

    static u8 flag = 0;

    if (flag != KEY)

    {

        delay_ms(10);//消抖

        flag = 1;

        return KEY;

    }

    else if (KEY == 0&&flag ==1)

    {

        flag = 0;

    }

    return 0;

}
```

### 后记

有一个很重要的思想：`while` 中的函数是不断被执行的，并且每秒循环上百万次，这意味着我们需要对每一个小状态延长考虑。我最开始想当然的在 `KEY_Scan()` 中直接返回了 `KEY` 的值，还是不够习惯单片机的工作方式，<s>当然不说明我是 cb</s>。

### 后后记

最近配 ``
