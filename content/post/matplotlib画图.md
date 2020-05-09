---
title: "matplotlib画图"
date: 2020-05-09T11:48:32+08:00
description: ""
draft: false
tags: ["Python","语法"]
categories: [Python]
---

matplotlib画图 常用语法

<!--more-->

|||
|:-----------|:-----|
|2020年5月9日| init |

# plot图像

```py
import matplotlib.pyplot as plt

#添加后支持显示中文↓
from pylab import *
mpl.rcParams['font.sans-serif'] = ['SimHei'] 
plt.rcParams['axes.unicode_minus'] = False  # 解决负号问题
#添加后支持显示中文↑

#声明第一张图片
plt.figure()      

#本振频率
fig_data_x = [2     ,2.5   ,3     ,3.5   ,4      ,4.2    ,4.3    ,4.4    ,4.5    ,4.6    ,4.7    ,4.8    ,4.9    ,5]
#输出本振+61.44MHz信号功率
fig_data_y = [-22.6 ,-25.4 ,-24.8 ,-25.5 ,-29.2  ,-33.9  ,-36.5  ,-38.5  ,-41    ,-44    ,-46.1  ,-48.6  ,-54.2  ,-59.6]

#plot图像方法说明
# @ x
# @ y
# @ ''
# line color  ：g | green；b | blue；c | cyan 蓝绿色；m - magenta 品红色 ...
# point shape ：. | 点儿；v | 实心倒三角；o | 实心圆；* | 实心五角星；+ | 加号 ...
# line shape  ：- | 实线；-- | 虚线；-. 点划线
# @ others
# color = 'red' # green\blue # 颜色 
# linestyle = '-'
# label = 'income' #
# linewidth =1
plt.plot(fig_data_x,fig_data_y,'c*-',linewidth =1)


# 增加显示文字信息等内容
for x,y in zip(fig_data_x,fig_data_y):
    plt.text(x-0.01 , y - 1, '%.2fGhz' % x, ha='center', va='bottom')

for x,y in zip(fig_data_x,fig_data_y):
    plt.text(x+0.05 , y + 1, '%.2fdBm' % y, ha='center', va='top')


# 设置x轴的文本，用于描述x轴代表的是什么
plt.xlabel("本振频率")
# 设置y轴的文本，用于描述y轴代表的是什么
plt.ylabel("输出本振+61.44MHz信号功率")


# legend将要显示的信息来自于上面代码中的 label.
# @ 参数 loc='upper right' 表示图例将添加在图中的右上角
#'best' : 0,'upper right'  : 1, 'upper left'   : 2, 'lower left'   : 3, 'lower right'  : 4,'right'    : 5, 
#'center left'  : 6, 'center right' : 7, 'lower center' : 8,'upper center' : 9, 'center'    : 10,
#
plt.legend(loc='best')
#plt.legend(loc='lower right', fontsize=40) # 标签位置
#plt.plot(fig_data_x,fig_data_y3, label="余额变化")

plt.show()



```


# bar图像
```py

#bar图像
# matplotlib.pyplot.bar(left, height, alpha=1, width=0.8, color=, edgecolor=, label=, lw=3)
# @color或facecolor：柱形图填充的颜色；
# @width：为柱形图的宽度，一般这是为0.8即可；
plt.bar(fig_data_x, fig_data_y0, facecolor='#00FF00', width = 2 , edgecolor='white', label="income")
plt.bar(fig_data_x, -fig_data_y1, facecolor='#ff0000', width = 2 , edgecolor='white', label="Spending")
```