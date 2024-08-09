---
title: 
date: 2024-04-02 23:08
tags: 
mathjax: "false"
banner: /images/thumbs/
excerpt:
---
几个常见Bug
## LED全点亮

选中Y4以后，P0之前的值会被传过去。需要在138Sel前保证P0的值为可控的。
### 初始化