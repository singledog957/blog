---
title: OLED引脚备注
date: 2024-01-27 22:44
tags:
  - STM32
mathjax: "true"
banner: /images/thumbs/
excerpt:
---

![](OLED_img_1.png)
配套江协科技 OLED 例程。太强了，望尘莫及啊。

屏幕的范围为 $x \in (0,127),y \in (0,63)$

### 基本说明

相关库函数都封装得很好。

调用完函数需要 `OLED_Update()`，才能正常显示。

还有 `OLED_UpdateArea()` 函数，用于刷新部分屏幕，

- `OLED_Clear() OLED_ClearArea()`
	用于清空显示

- `OLED_Reverse() OLED_ReverseArea()`
	反色显示。

### 汉字与图像

汉字取模：
使用 `PCtoLCD2002`。配置如图。得到的数据放入 `OLED_Data.c` 的数组 `ChineseCell_t OLED_CF16x16`。
![](OLED_img_2.png)
<p style="font-size: 13px" align = "center">软件配置</p>
最后使用 `OLED_ShowChinese()` 实现。

> 只能显示 `16x16` 的汉字。其他大小可以通过图像实现。

> 显示图片前，需要使用 PS 对其进行二值化等处理，最后保存为需要的尺寸即可。需要保存为 `BMP` 格式。

显示图像与之类似。取模后的数据在 `const uint8_t Diode[]` 后，自定义 `const uint8_t <nameHere>[]`；

然后在头文件 `OLED_Data.h` 中声明即可。
