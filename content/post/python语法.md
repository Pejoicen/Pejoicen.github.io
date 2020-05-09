---
title: "Python3 常用语法"
date: 2019-09-27T20:46:32+08:00
description: ""
draft: false
tags: ["Python","语法"]
categories: [Python]
---

整理Python3 常用语法

<!--more-->

|||
|:--------------|:-----|
|2019年8月20日| init |
|2019年9月27日| 更新文件相关和多线程部分内容|

# Python3 常用语法记录

注释：#  
**严格要求缩进！！！**  
大小写敏感  
变量不固定的动态语言   

# 基础语句

## 输入和输出（命令行）
**输出**  
print(),括号中可以是字符串也可以是变量。也可以是多个字符串，用逗号隔开即可。  

**输入**  
input(),在()中可以加入字符串打印到屏幕上，可以省略一个print()函数。  

## 循环语句

while 

```python
var = 1
while var == 1 :  # 表达式永远为 true
   num = int(input("输入一个数字  :"))
   print ("你输入的数字是: ", num)
 
count = 0
while count < 5:
   print (count, " 小于 5")
   count = count + 1
else:
   print (count, " 大于或等于 5")
```

for

```python
for <variable> in <sequence>:
    <statements>
else:
    <statements>
```

经常使用range()函数创建一个列表

range(start, stop[, step])

参数说明：

- start: 计数从 start 开始。默认是从 0 开始。例如range（5）等价于range（0， 5）;
- stop: 计数到 stop 结束，但不包括 stop。例如：range（0， 5） 是[0, 1, 2, 3, 4]没有5
- step：步长，默认为1。例如：range（0， 5） 等价于 range(0, 5, 1)



# 数据类型
str bytes Number

Python 3最重要的新特性之一是对**字符串**和**二进制数据流**做了明确的区分。  
文本总是Unicode，由**str**类型表示，二进制数据则由**bytes**类型表示  
Python 3不会以任意隐式的方式混用str和bytes  
字符串类str里有一个encode()方法，它是从字符串向比特流的编码过程。  
bytes类型恰好有个decode()方法，它是从比特流向字符串解码的过程。  

## Number
python中数字有四种类型：整数、布尔型、浮点数和复数。  

||||
|-------------|------|---------------------------------------|
|**int**      |整数  |如 1,只有一种整数类型 int,表示为长整型.|
|**bool**     |布尔  |如 True                                |
|**float**    |浮点数|如 1.23、3E-2                          |
|**complex**  |复数  |如 1 + 2j、 1.1 + 2.2j                 |

例：  
* a = 0x66  : 0b(2进制) 0o(8进制) 0x(16进制) 正常为10进制  
* b = 0.66  
* c = 6+6j  
* d = True  

算术运算  

||||
|--------|-------------------------------------------------|-------------------|
|**\+**  |加 - 两个对象相加                               |a \+ b 输出结果 31  |
|**\-**  |减 - 得到负数或是一个数减去另一个数             |a \- b 输出结果 -11 |
|**\***  |乘 - 两个数相乘或是返回一个被重复若干次的字符串 |a \* b 输出结果 210 |
|**/**   |除 - x 除以 y                                   |b /  a 输出结果 2.1 |
|**%**   |取模 - 返回除法的余数                           |b %  a 输出结果 1   |
|**\*\***|幂 - 返回x的y次幂                               |a\*\*b 为10的21次方 |
|**//**  |取整除 - 向下取接近除数的整数                   |9//2=4  -9//2=-5    |



### bytes

字节流

```python
a = b'\x12\x34\x56'	# 16进制赋值
print(a[0:2])	# 打印为bytes,最后一个不显示（a[2]）
print(a[0])		# 打印为int
#获取长度
len(a)
```

结果：

> b'\x12\x34'
>
> 18
>
> 3







### **str => bytes**

```python
a = 'abcdef'  # 字符串赋值
a_bytes = bytes.fromhex(a)  # 将str 转为 bytes  
print(a_bytes)
b = a_bytes.hex()   # 将bytes 转 str
print(b)  
```

显示结果：

> b'\xab\xcd\xef ‘  
>
> abcdef

bytes.hex(self: bytes)

B.hex() -> string

Example: b'\xb9\x01\xef'.hex() -> 'b901ef'.



### **int<=>bytes**

```python
to_bytes(length, byteorder)和from_bytes(type, bytes, byteorder)

TC1 = 0
TC_value = 0

TC_bytes = TC_value.to_bytes(4,byteorder='little') # 整型转字节流
TC_value = int.from_bytes(TC_bytes,byteorder='little') # 字节流转整型
```

### 


## 字符串和编码

''或""，中间的内容为字符串内容。''' ''' 表示多行字符串  
**转义** 字符"\",\'=>',\\=>\.  \r\n  
Python3中使用Unicode编码  
str.encode('ascii')或('utf-8')可以指定编码
len()是计算长度的函数，可以len(str.encode('utf-8'))  

### 字符串操作

[ : ]  截取字符串中的一部分，遵循**左闭右开**原则，str[0,2] 是不包含第 3 个字符的。

截取字符串使用 变量[头下标:尾下标]，就可以截取相应的字符串，其中下标是从0开始算起，可以是正数或负数，下标可以为空表示取到头或尾。

```python
str = '12345678'
print str[0:1]
>> 1			# 输出str位置0开始到位置1以前的字符
print str[1:6]		
>> 23456			# 输出str位置1开始到位置6以前的字符
num = 18
str = '0000' + str(num)	# 合并字符串
print str[-5:]		# 输出字符串右5位
>> 00018

```



### 数字格式化

str.format() 

```python
>>> print("{:.2f}".format(3.1415926));
3.14
```

| 数字       | 格式                                                         | 输出                   | 描述                         |
| :--------- | :----------------------------------------------------------- | :--------------------- | :--------------------------- |
| 3.1415926  | {:.2f}                                                       | 3.14                   | 保留小数点后两位             |
| 3.1415926  | {:+.2f}                                                      | +3.14                  | 带符号保留小数点后两位       |
| -1         | {:+.2f}                                                      | -1.00                  | 带符号保留小数点后两位       |
| 2.71828    | {:.0f}                                                       | 3                      | 不带小数                     |
| 5          | {:0>2d}                                                      | 05                     | 数字补零 (填充左边, 宽度为2) |
| 5          | {:x<4d}                                                      | 5xxx                   | 数字补x (填充右边, 宽度为4)  |
| 10         | {:x<4d}                                                      | 10xx                   | 数字补x (填充右边, 宽度为4)  |
| 1000000    | {:,}                                                         | 1,000,000              | 以逗号分隔的数字格式         |
| 0.25       | {:.2%}                                                       | 25.00%                 | 百分比格式                   |
| 1000000000 | {:.2e}                                                       | 1.00e+09               | 指数记法                     |
| 13         | {:10d}                                                       | 13                     | 右对齐 (默认, 宽度为10)      |
| 13         | {:<10d}                                                      | 13                     | 左对齐 (宽度为10)            |
| 13         | {:^10d}                                                      | 13                     | 中间对齐 (宽度为10)          |
| 11         | `'{:b}'.format(11) '{:d}'.format(11) '{:o}'.format(11) '{:x}'.format(11) '{:#x}'.format(11) '{:#X}'.format(11)` | `1011 11 13 b 0xb 0XB` | 进制                         |

### 字符串格式化

和C里面的用法一样，甚至也可以指定长度  

|占位符 |替换内容|
|-------|:-------|
|%d     |整数    |
|%f     |浮点数  |
|%s     |字符串  |
|%x     |十六进制整数|

举例

```python
workdir = os.getcwd()               #获得当前路径 
print ( '当前工作路径为:%s'%(workdir) )
```

结果：
当前工作路径为:E:\python_prj



# 全局变量

全局变量能够在所有的函数中进行访问，但是不能修改。如果在函数中修改全局变量，那么就需要使用global进行声明，否则出错。如果全局变量的名字和局部变量的名字相同，那么使用的是局部变量的

python增加了global关键字， 可以指定变量的作用域。global关键字的作用：声明变量var是全局的。

使用方法：

> global 关键字，后面为变量名，多个变量名使用逗号隔开
>
> 函数中使用时，也是先使用global关键字声明全局变量，再使用

```python
# 播放相关的全局变量
global Play_Flag,VideoAddr   
Play_Flag = False   # 播放控制标志
VideoAddr = ''      # 视频地址

# 函数中调用（线程其实也是执行函数）
def Button_Play_Click():
    print('点击播放按钮')
    global VideoAddr,Play_Flag
    VideoAddr = Entry_ADDR.get()
    Play_Flag = True
    NewThread.start()
```

# 文件操作

file = open(filename,encoding='UTF-8') #打开文件  
file.close() #关闭文件 **忘记关闭文件会导致资源泄露**  
免不了出现打开失败的情况，所以需要让系统自动关闭文件，方法：  
```python
with open(filename,encoding='UTF-8') as file: #注意此处的缩进
    print('打开文件失败')  
```



## open() 方法

Python open() 方法用于打开一个文件，并返回文件对象，在对文件进行处理过程都需要使用到这个函数，如果该文件无法被打开，会抛出 OSError。

**注意：**使用 open() 方法一定要保证关闭文件对象，即调用 close() 方法。

open() 函数常用形式是接收两个参数：文件名(file)和模式(mode)。

```python
open(file, mode='r')
```

完整的语法格式为：

```python
open(file, mode='r', buffering=-1, encoding=None, errors=None, newline=None, closefd=True, opener=None)
```

参数说明:

- file: 必需，文件路径（相对或者绝对路径）。
- mode: 可选，文件打开模式
- buffering: 设置缓冲
- encoding: 一般使用utf8
- errors: 报错级别
- newline: 区分换行符
- closefd: 传入的file参数类型
- opener:

mode 参数有：

| 模式 | 描述                                                         |
| :--- | :----------------------------------------------------------- |
| t    | 文本模式 (默认)。                                            |
| x    | 写模式，新建一个文件，如果该文件已存在则会报错。             |
| b    | 二进制模式。                                                 |
| +    | 打开一个文件进行更新(可读可写)。                             |
| U    | 通用换行模式（**Python 3 不支持**）。                        |
| r    | 以只读方式打开文件。文件的指针将会放在文件的开头。这是默认模式。 |
| rb   | 以二进制格式打开一个文件用于只读。文件指针将会放在文件的开头。这是默认模式。一般用于非文本文件如图片等。 |
| r+   | 打开一个文件用于读写。文件指针将会放在文件的开头。           |
| rb+  | 以二进制格式打开一个文件用于读写。文件指针将会放在文件的开头。一般用于非文本文件如图片等。 |
| w    | 打开一个文件只用于写入。如果该文件已存在则打开文件，并从开头开始编辑，即原有内容会被删除。如果该文件不存在，创建新文件。 |
| wb   | 以二进制格式打开一个文件只用于写入。如果该文件已存在则打开文件，并从开头开始编辑，即原有内容会被删除。如果该文件不存在，创建新文件。一般用于非文本文件如图片等。 |
| w+   | 打开一个文件用于读写。如果该文件已存在则打开文件，并从开头开始编辑，即原有内容会被删除。如果该文件不存在，创建新文件。 |
| wb+  | 以二进制格式打开一个文件用于读写。如果该文件已存在则打开文件，并从开头开始编辑，即原有内容会被删除。如果该文件不存在，创建新文件。一般用于非文本文件如图片等。 |
| a    | 打开一个文件用于追加。如果该文件已存在，文件指针将会放在文件的结尾。也就是说，新的内容将会被写入到已有内容之后。如果该文件不存在，创建新文件进行写入。 |
| ab   | 以二进制格式打开一个文件用于追加。如果该文件已存在，文件指针将会放在文件的结尾。也就是说，新的内容将会被写入到已有内容之后。如果该文件不存在，创建新文件进行写入。 |
| a+   | 打开一个文件用于读写。如果该文件已存在，文件指针将会放在文件的结尾。文件打开时会是追加模式。如果该文件不存在，创建新文件用于读写。 |
| ab+  | 以二进制格式打开一个文件用于追加。如果该文件已存在，文件指针将会放在文件的结尾。如果该文件不存在，创建新文件用于读写。 |

默认为文本模式，如果要以二进制模式打开，加上 **b** 。



## file 对象

file 对象使用 open 函数来创建，下表列出了 file 对象常用的函数：

| 序号 | 方法及描述                                                   |
| :--- | :----------------------------------------------------------- |
| 1    | [file.close()](https://www.runoob.com/python3/python3-file-close.html)关闭文件。关闭后文件不能再进行读写操作。 |
| 2    | [file.flush()](https://www.runoob.com/python3/python3-file-flush.html)刷新文件内部缓冲，直接把内部缓冲区的数据立刻写入文件, 而不是被动的等待输出缓冲区写入。 |
| 3    | [file.fileno()](https://www.runoob.com/python3/python3-file-fileno.html)返回一个整型的文件描述符(file descriptor FD 整型), 可以用在如os模块的read方法等一些底层操作上。 |
| 4    | [file.isatty()](https://www.runoob.com/python3/python3-file-isatty.html)如果文件连接到一个终端设备返回 True，否则返回 False。 |
| 5    | [file.next()](https://www.runoob.com/python3/python3-file-next.html)**Python 3 中的 File 对象不支持 next() 方法。**返回文件下一行。 |
| 6    | [file.read([size\])](https://www.runoob.com/python3/python3-file-read.html)从文件读取指定的字节数，如果未给定或为负则读取所有。 |
| 7    | [file.readline([size\])](https://www.runoob.com/python3/python3-file-readline.html)读取整行，包括 "\n" 字符。 |
| 8    | [file.readlines([sizeint\])](https://www.runoob.com/python3/python3-file-readlines.html)读取所有行并返回列表，若给定sizeint>0，返回总和大约为sizeint字节的行, 实际读取值可能比 sizeint 较大, 因为需要填充缓冲区。 |
| 9    | [file.seek(offset[, whence\])](https://www.runoob.com/python3/python3-file-seek.html)设置文件当前位置 |
| 10   | [file.tell()](https://www.runoob.com/python3/python3-file-tell.html)返回文件当前位置。 |
| 11   | [file.truncate([size\])](https://www.runoob.com/python3/python3-file-truncate.html)从文件的首行首字符开始截断，截断文件为 size 个字符，无 size 表示从当前位置截断；截断之后后面的所有字符被删除，其中 Widnows 系统下的换行代表2个字符大小。 |
| 12   | [file.write(str)](https://www.runoob.com/python3/python3-file-write.html)将字符串写入文件，返回的是写入的字符长度。 |
| 13   | [file.writelines(sequence)](https://www.runoob.com/python3/python3-file-writelines.html)向文件写入一个序列字符串列表，如果需要换行则要自己加入每行的换行符。 |



## 读文件  

**read()**  
调用read()会一次性读取文件的全部内容，如果文件有10G，内存就爆了，所以，可以反复调用read(size)方法，每次最多读取size个字节的内容。
**readline()**  
调用readline()可以每次读取一行内容
**readlines()**    
调用readlines()一次读取所有内容并按行返回list

## 写文件

```python
file = open(filename,mode = 'ab')   # 追加操作
file.write(data[64:64+framelen]  )
file.close()
```



# 文件路径

tkFileDialog有两种形式: 
askopenfilename(option=value, ...) 这个是"打开"对话框
asksaveasfilename(option=value, ...) 这个是另存为对话框

option参数如下:

defaultextension = s  默认文件的扩展名

filetypes = [(label1, pattern1), (label2, pattern2), ...]  设置文件类型下拉菜单里的的选项

initialdir = D  对话框中默认的路径

initialfile = F  对话框中初始化显示的文件名

parent = W  父对话框(由哪个窗口弹出就在哪个上端)

title = T  弹出对话框的标题

如果选中文件的话,确认后会显示文件的完整路径,否则单击取消的话会返回空字符串

```python
import os
os.path


# 保存文件
title = 'Save project as'
ftypes = [('Project file', '.prj'), ('All files', '*')]
filename = asksaveasfilename(filetypes=ftypes, title=title,defaultextension='.prj')
```



# 类

## 类和实体

类是抽象模板，实体是用模板创建的具体对象。

```python
class Student（object）：
	def __init__ (self,param1,param2):
		self.param1 = param1
		self.param2 = param2

#所有的类都继承于：object 类，Student后括号中为继承的父类
#特殊方法__init__（两个下划线），类似于构造函数，__init__`方法的第一个参数永远是`self，表示创建的实例本身
```





# 多线程

## threading模块创建线程

```python
import threading
import time

exitFlag = 0

class myThread (threading.Thread):	# 继承自 threading.Thread
    def __init__(self, threadID, name, counter):	# 构造
        threading.Thread.__init__(self)
        self.threadID = threadID
        self.name = name
        self.counter = counter
    def run(self):
        print ("开始线程：" + self.name)
        print_time(self.name, self.counter, 5)
        print ("退出线程：" + self.name)

def print_time(threadName, delay, counter):
    while counter:
        if exitFlag:
            threadName.exit()
        time.sleep(delay)
        print ("%s: %s" % (threadName, time.ctime(time.time())))
        counter -= 1

# 创建新线程
thread1 = myThread(1, "Thread-1", 1)
thread2 = myThread(2, "Thread-2", 2)

# 开启新线程
thread1.start()
thread2.start()
thread1.join()#让主线程等待子线结束后再做其他事情
thread2.join()
print ("退出主线程")
```

## 线程停止

在 Python 的多线程编程中，在实例代码中经常有 thread1.join()这样的代码。join的原理就是依次检验线程池中的线程是否结束，没有结束就阻塞直到线程结束，如果结束则跳转执行下一个线程的join函数。

先看看这个：

1. 阻塞主进程，专注于执行多线程中的程序。

2. 多线程多join的情况下，依次执行各线程的join方法，前头一个结束了才能执行后面一个。

3. 无参数，则等待到该线程结束，才开始执行下一个线程的join。

4. 参数timeout为线程的阻塞时间，如 timeout=2 就是罩着这个线程2s 以后，就不管他了，继续执行下面的代码。

```

```

当线程结束后，如果想再次启动线程，不能只调用thread.start(),需要重新创建线程，再启动线程。

## 线程同步

略

# 时间、日期

获取时间、日期

函数time.time()用于获取当前时间戳, 如下实例:

```python
import time;  # 引入time模块
ticks = time.time()
print ("当前时间戳为:", ticks)
```

以上实例输出结果：

```
当前时间戳为: 1459996086.7115328
```

## 获取当前时间

从返回浮点数的时间戳方式向时间元组转换，只要将浮点数传递给如localtime之类的函数。

```python
import time

localtime = time.localtime(time.time())
print ("本地时间为 :", localtime)
```

以上实例输出结果：

```
本地时间为 : time.struct_time(tm_year=2016, tm_mon=4, tm_mday=7, tm_hour=10, tm_min=28, tm_sec=49, tm_wday=3, tm_yday=98, tm_isdst=0)
```



## 获取格式化的时间

你可以根据需求选取各种格式，但是最简单的获取可读的时间模式的函数是asctime():

```python
import time

localtime = time.asctime( time.localtime(time.time()) )
print ("本地时间为 :", localtime)
```

以上实例输出结果：

```
本地时间为 : Thu Apr  7 10:29:13 2016
```

------

## 格式化日期

我们可以使用 time 模块的 strftime 方法来格式化日期，：

```python
time.strftime(format[, t])
import time

# 格式化成2016-03-20 11:45:39形式
print (time.strftime("%Y-%m-%d %H:%M:%S", time.localtime()))

# 将格式字符串转换为时间戳
a = "Sat Mar 28 22:24:24 2016"
print (time.mktime(time.strptime(a,"%a %b %d %H:%M:%S %Y")))
```

以上实例输出结果：

```
2016-04-07 10:29:46
1459175064.0
```

python中时间日期格式化符号：

- %y 两位数的年份表示（00-99）
- %Y 四位数的年份表示（000-9999）
- %m 月份（01-12）
- %d 月内中的一天（0-31）
- %H 24小时制小时数（0-23）
- %I 12小时制小时数（01-12）
- %M 分钟数（00=59）
- %S 秒（00-59）
- %a 本地简化星期名称
- %A 本地完整星期名称
- %b 本地简化的月份名称
- %B 本地完整的月份名称
- %c 本地相应的日期表示和时间表示
- %j 年内的一天（001-366）
- %p 本地A.M.或P.M.的等价符
- %U 一年中的星期数（00-53）星期天为星期的开始
- %w 星期（0-6），星期天为星期的开始
- %W 一年中的星期数（00-53）星期一为星期的开始
- %x 本地相应的日期表示
- %X 本地相应的时间表示
- %Z 当前时区的名称
- %% %号本身