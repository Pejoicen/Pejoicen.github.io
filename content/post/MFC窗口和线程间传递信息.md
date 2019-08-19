---
title: "MFC窗口和线程间传递信息"
date: 2019-08-15T16:56:43+08:00
description: ""
draft: false
tags: ["MFC","线程"]
categories: ["软件"]
---

MFC窗口和线程间传递信息的注意事项和方法。
<!--more-->

1. 线程向界面窗口派发消息，使用其成员变量，调用成员函数（实现计算或文件存储等）都没什么问题；
2. 如果是按钮关联的事件函数，建议不直接调用，而是派发消息。线程和界面线程之间尽量以发消息为主。

特别是界面窗口函数与界面关联比较大，比如刚才说的事件函数，或者成员函数有Update函数调用

# 线程中向界面发送消息

```cpp:  
//类的头文件中
afx_msg LRESULT PickOutInfoTeam(WPARAM wparam,LPARAM lparam);  
```


# 