---
title: Easy 云课堂快速跳转
date: 2024-05-16 12:22
tags:
  - 网络
mathjax: "false"
excerpt: Easy 云课堂快速跳转。
---

## Easy 云课堂快速跳转

众所周知，某学院的 FPGA 期末题库上传到了 Easy 云课堂上。这个平台的交互做得实在是遗憾，web 版连跳题功能都没有。下面简单给一个模拟点击实现快速跳转的方法。

> 小程序的题目跳转有 Bug，前半截被截断了。

顺便给个网址:[Easy云课堂 (easyketang.com)](https://www.easyketang.com/#/index)

## 极速版

在浏览器输入以下代码即可。cnt 修改为需要跳转的题号。

```js
javascript:var cnt=10;
var nextButton = document.querySelector('.next');for(var i = 0; i < cnt-1; i++) {nextButton.click();}
```

粘贴以后，需要在地址栏开头手动输入 `javascript:`。直接粘贴会导致 `javascript:` 部分被删除。
![](HardClass_img_1.png)
<p style="font-size: 13px" align = "center">正确的填写方式</p>

## 优雅版

感觉也不是很有必要了。不过写都写了

```js
// ==UserScript==
// @name         Easy云课堂题目快速跳转
// @namespace    https://www.easyketang.com/#/student/exam
// @version      0.1
// @description  快速跳转
// @author       SD
// @match        *://www.easyketang.com/*
// @grant        none
// ==/UserScript==



(function() {
    'use strict';
    if (!/^https:\/\/www\.easyketang\.com\/#\/student\/exam\?id=\d+&task_id=\d+&course_id=\d+$/.test(location.href)) return;


    setTimeout(function() {
        var nextButton = document.querySelector('.next');
        if(nextButton) {
                var x = prompt("要跳转的题号");
                var clickCount = parseInt(x);
                if (!isNaN(clickCount)) {
                    for(var i = 0; i < clickCount-1; i++) {
                        nextButton.click();
                    }
                }
        }
    }, 5000); // 等待加载
})();


```

导入油猴使用即可。没有起作用刷新即可。

## 后记

<s>这 vue 前端写得和 shit 一样</s>。小程序也烂完了。[这套系统还卖了5万块](https://zbcg.buaa.edu.cn/info/1049/48451.htm)，不如直接找软院的同学。

match url 那里卡了很久，最后摆了。subpath 里面带一个 `#`，正经 url 谁这么写啊。
