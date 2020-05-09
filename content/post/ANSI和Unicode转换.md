---
title: "VS中ANSI和Unicode转换"
date: 2020-04-27T10:09:32+08:00
description: " "
draft: false
tags: ["C/C++","VS"]
categories: [软件]
---
VS默认字符集为Unicode，之前的工程一直在用ANSI。在此整理ANSI和Unicode转换方法。

包括：
CString转ANSI char*
ANSI char*转CString

<!--more-->

ANSI和Unicode转换



# Unicode CString转ANSI char数组

```cpp
CString wStr;	// 待转换
wchar_t *p1;
p1=wStr.GetBuffer(wStr.GetLength());
wStr.ReleaseBuffer();

// Convert to a char*
size_t s1 = wcslen(p1) + 1;
const size_t newsize1 = 200;
size_t convertedChars1 = 0;
char c1[newsize1];	// 转换成的字符串
wcstombs_s(&convertedChars1, c1, s1, p1 , _TRUNCATE);
```



```cpp
void WChar2Char(CString wStr,char * Char)
{
	wchar_t *p1;
	p1=wStr.GetBuffer(wStr.GetLength());
	wStr.ReleaseBuffer();

	// Convert to a char*
	size_t s1 = wcslen(p1) + 1;
	const size_t newsize1 = 2000;
	size_t convertedChars1 = 0;
	//char c1[newsize1];
	//wcstombs_s(&convertedChars1, c1, s1, p1 , _TRUNCATE);
	wcstombs_s(&convertedChars1, Char, s1, p1 , _TRUNCATE);
}
```





# ANSI转Unicode

**VC下的格式转换**

**ANSI字符串转Unicode**(windows下对应UTF-16编码)
在vc工程选择unicode编译的情况下，如果是字符串常量可以直接使用L和_T or _TEXT宏，也可以使用wsprintf函数进行转换（需要引用Windows.h）。  

```cpp
 TCHAR szW[100];  
 //字符串常量时
 swprintf(szW,L"%s",L"Unicode Str中文");  
 CString name = _T("Report.txt");
```



```cpp
char HeaderPatt[50]
TCHAR wChar[100];  
wsprintfW(wChar,L"%S",HeaderPatt);  	// 大写的S
m_encodingDlg.m_HeaderPatt_Send.Format(_T("%s"),wChar);
```



# 字符串长度

```cpp
//unicode编码
CString FilePath;
FilePath = "C:\Users\Pejoicen\Desktop\HDR_jiemian_20200426_send_recv_tan\HDR_PcieDataTrans\Debug\sendConfig.dat"

int len = FilePath.GetLength(); // len = 99

```
//转成char数组后，长度为99+1.   
查看内存中转换好的ANSI字符串内容

|            |                                                              |                                                                     |
| ---------- | ------------------------------------------------------------ | ------------------------------------------------------------------- |
| 0x01B3CDE0 | 43 3a 5c 55 73 65 72 73 5c 50 65 6a 6f 69 63 65 6e 5c 44 65 73 6b 74 6f 70 5c 48 44 52 5f 6a 69 | C:\Users\Pejoicen\Desktop\HDR_ji |
| 0x01B3CE00 | 65 6d 69 61 6e 5f 32 30 32 30 30 34 32 36 5f 73 65 6e 64 5f 72 65 63 76 5f 74 61 6e 5c 48 44 52 | emian_20200426_send_recv_tan\HDR |
| 0x01B3CE20 | 5f 50 63 69 65 44 61 74 61 54 72 61 6e 73 5c 44 65 62 75 67 5c 73 65 6e 64 43 6f 6e 66 69 67 2e | _PcieDataTrans\Debug\sendConfig. |
| 0x01B3CE40 | 64 61 74 00                                                  | dat. |













# WideCharToMultiByte函数和MultiByteToWideChar函数