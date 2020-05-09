---
title: "CString、char[]数组和'数'之间的转换"
date: 2019-08-20T09:50:32+08:00
description: ""
draft: false
tags: ["C/C++","字符串"]
categories: [软件]
---

整理CString、char[]和"数值"变量间的转换

<!--more-->

# CString、char[]数组和'数'之间的转换

> 涉及到字符串时需要注意编码方式：多字符集还是Unicode。字符串和CString之间的转换默认是在多字符集情况下

## char[]数组拷贝

char[]数组拷贝函数：string.h中的 **strncpy_s **函数  
使用strncpy_s 可以将CString拷贝到char[]

```cpp
#include "stdafx.h"
#include <string.h>
int _tmain(int argc, _TCHAR* argv[])
{   
	int i=0;
	char ar1[10] = {-1};
	for (;i<10;i++)
	{
		ar1[i]=-1;
	}
	const char * te="this is test strncpy_s";
	strncpy_s(ar1,10,te,5);
	return 0;
}
strncpy_s 函数原型：
#if __STDC_WANT_SECURE_LIB__
_CRTIMP_ALTERNATIVE __checkReturn_wat errno_t __cdecl strncpy_s(__out_ecount_z(_DstSize) char * _Dst, __in rsize_t _DstSize, __in_z_opt const char * _Src, __in rsize_t _MaxCount);

char * _Dst:目标buffer;  _DstSize：buffer长度;  char * _Src:源buffer;  _MaxCount:指定拷贝长度 
注意：strncpy_s自动比较_MaxCount和 strlen(_Src) 然后取两者中的较小值

```


**[CString](https://baike.baidu.com/item/CString)


## CString和 “数据”间的转换

### CString => int double float等

C标准库<stdlib.h>

**strtoul、strtol、strtod等**

 **stof()、atoi()、[atol()](http://c.biancheng.net/cpp/html/126.html)、[strtod()](http://c.biancheng.net/cpp/html/128.html)、[strtol()](http://c.biancheng.net/cpp/html/129.html)、[strtoul()](http://c.biancheng.net/cpp/html/130.html) 。**

**C99 / C++11 规范 atoll()、strtof()、strtold()、strtoll()、strtoull()**


strtol() 函数用来将字符串转换为长整型数(long)，

其原型为：long int strtol (const char* str, char** endptr, int base);

【参数说明】**str 为要转换的字符串，endstr 为第一个不能转换的字符的指针，base 为字符串 str 所采用的进制。**

【函数说明】strtol() 会将参数 str 字符串根据  **参数 base** 来转换成长整型数(long)。参数 base 范围从2 至36，或0。**参数base 代表 str 采用的进制方式**，如base 值为10 则采用10 进制，若base 值为16 则采用16 进制等。

两点注意：

- 当 base 的值为 0 时，默认采用 10 进制转换，但如果遇到 '0x' / '0X' 前置字符则会使用 16 进制转换，遇到 '0' 前置字符则会使用 8 进制转换。
- 若endptr 不为NULL，则会将遇到的不符合条件而终止的字符指针由 endptr 传回；**若 endptr 为 NULL，则表示该参数无效，或不使用该参数。**

温馨提示：

ANSI C 规范定义了 **[stof()](http://c.biancheng.net/cpp/html/124.html)、[atoi()](http://c.biancheng.net/cpp/html/125.html)、[atol()](http://c.biancheng.net/cpp/html/126.html)、[strtod()](http://c.biancheng.net/cpp/html/128.html)、[strtol()](http://c.biancheng.net/cpp/html/129.html)、[strtoul()](http://c.biancheng.net/cpp/html/130.html)** 共6个可以将字符串转换为数字的函数，大家可以对比学习。另外在 C99 / C++11 规范中又新增了5个函数，分别是 **atoll()、strtof()、strtold()、strtoll()、strtoull()**，在此不做介绍，请大家自行学习。

### **int double float char[] 等 => CString**

```cpp
CString data;
data.format(_T("%2d  %02f %s",)int , float ,char)
```

