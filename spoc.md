---
title: Spoc小助手使用说明
date: 2023-12-02 18:01
tags:
  - 网络
mathjax: "false"
---

> 刷课有风险，请自行承担。

> 请各位尽量**不要**使用微信打开本站，微信日益收紧的审查机制足以把我和我的垃圾桶送进 GFW 的黑名单里。如果有其他平台的群组，务必拉我。

一个快速完成 BUAA spoc 国家安全 系列视频的脚本。

## 安装说明

### Step 1. 安装 Tamper Monkey 插件

[Tampermonkey油猴插件——安装与使用教程 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/128453110)

> `GreasyMonkey` 应该也行，我没有测试。欢迎反馈。

### Step 2. 添加本脚本

打开 [Spoc Helper (greasyfork.org)](https://greasyfork.org/zh-CN/scripts/481227-spoc-helper)，点击 `安装此脚本`，在弹出的窗口中点击 `安装`。

### Step 3. 打开课程网址

> 总结：没起作用就多刷新

第一次打开时可能不能直接成功，刷新后应该可以。

切换到新课程时，也请等待一段时间后刷新页面。

## 风险说明

> 刷课有风险，请自行承担。
> 刷课有风险，请自行承担。
> 刷课有风险，请自行承担。

网页的源码里是有观看时长统计的函数的，但是没有调用，目前尚不清楚是否会在服务器端统计。

如果您因使用本脚本收到了任何形式的处罚，请**立刻**[联系我](singledog957@gmail.com) 或者在 [这里](https://github.com/singledog957/Spoc-Helper/issues) 提 issue，感谢您为广大 BUAAer 做出的贡献。

## 后记

> Copilot with `GPT-3.5`

我不会 js，感谢 GPT。一开始方向错了，导致浪费了很多时间在劫持 response 上面。

不出问题应该不会更新了，不过更好的实现方法应该可以不用每次刷新，直接完成。欢迎 pr。
