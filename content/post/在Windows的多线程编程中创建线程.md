---
title: "Windows的多线程编程"
date: 2019-08-20T10:09:32+08:00
description: ""
draft: false
tags: ["C/C++","线程","Windows"]
categories: [软件]
---

在Windows的多线程编程中，创建线程的函数主要有CreateThread，_beginthead(_beginthreadex)和AfxBeginThread，那么它们之间有什么联系与区别呢？当我需要创建一个线程时该用哪个函数呢？



<!--more-->

需要的头文件支持
```cpp
 \#include \<process.h\>         // for \_beginthread()
```
需要的设置:ProjectàSetting--\>C/C++--\>User run-time library 选择Debug
Multithreaded 或者Multithreaded。即使用: MT或MTD。


# 下面先介绍各个函数的用法：

注：LPVOID 可以理解为long型指针，指向void。

## CreateThread：

函数原型：

```cpp
HANDLE WINAPI CreateThread(
   _in LPSECURITY_ATTRIBUTES lpThreadAttributes,
   _in SIZE_T dwStackSize,
   _in LPTHREAD_START_ROUTINE lpStartAddress,
   _in LPVOID lpParameter,
   _in DWORD dwCreationFlags,
   _out LPDWORD lpThreadId 
   );
```

参数：  

 * lpThreadAttributes: 指向一个LPSECURITY_ATTRIBUTES结构的指针决定返回的句柄能否被继承，如果lpThreadAttributes为空，这个句柄不能被继承。
 * sdStackSize：初始化的堆栈大小，以字节为单位。如果为0，使用默认的大小。
 * lpStartAddress：函数的入口地址，是一个函数指针。
 * lpParameter：一个指针，被传递到线程函数里。
 * dwCreationFlags：线程创建的标志。如果为CREATE_SUSPENDED这个标志，那么需要使用ResumeThread函数来激活线程函数，如果为NULL，线程函数立刻执行。
 * IpThreadId：一个指向线程id的指针，如果为空，线程id不被返回。  

返回值：  

 * 1：如果函数成功执行，**返回值将是这个新线程的句柄。如果失败，返回值是NULL**。
 * 2：当线程函数的起始地址无效（或者不可访问）时，CreateThread函数仍可能成功返回。如果该起始地址无效，则当线程运行时，异常将发生，线程终止，并返回一个错误代码，可以使用GetLastError获取。

说明：  

 * 1：如果线程函数return，返回值会隐式条用ExitThread函数，可以使用GetExitCodeThread函数获得该线程函数的返回值。
 * 2：使用CreateThread创建的线程具有THREAD_PRIORITY_NORMAL线程优先级。可以使用GetThreadPriority和SetThreadPriority函数获取和设置线程优先级值。
 * 3：当一个线程结束时，这个线程的对象将获得有信号状态，使得任何等待这个对象的线程都能够成功并继续执行下去。
 * 4：系统中的线程对象一直存活到线程结束，并且所有指向它的句柄都需要通过调用CloseHandle关闭。
 * 5：如果一个线程调用了CRT，应该使用_beginthreadex 和_endthreadex(需要使用多线程版的CRT)。



## \_beginthread与\_beginthreadex:

函数原型：

```cpp
uintptr_t _beginthread(
   void( *start_address )( void * ), 
   unsigned stack_size,
   void *arglist
   );
uintptr_t _beginthreadex(
 void *security,
 unsigned stack_size,
 unsigned ( *start_address )( void * ),
 void *arglist,
 unsigned initflag,
 unsigned *thrdaddr
 );  
```

参数：
start_address:线程函数的入口地址。对于_beginthread,线程函数的调用约定是_cdecl。对于_beginthreadex,线程函数的调用约定是_stdcall。
stack_size:线程堆栈大小，可以为0。
arglist:传递给线程函数的参数，可以为0。
security:线程安全属性。
initflag:线程创建的初始标志。为CREATE_SUSPENDED则挂起线程，使用ResumeThread激活线程，为NULL则立即执行。
thrdaddr:线程Id。   
 返回值：  

- 1：如果成功，将会返回一个新的线程句柄。然而，如果线程函数执行的很快，_beginthread可能得到一个非法的句柄。
- 2：如果失败，_beginthread返回-1，此时errno变量将被设置。_beginthreadex返回0，此时errno和_doserrno都被设。  

说明：  

 * 1：_beginthread函数的线程入口函数必须使用_cdecl调用约定。_beginthreadex函数的线程入口函数必须使用_stdcall调用约定
 * 2：使用_beginthreadex比使用_begingthread更加安全。因为_beginthread的线程函数可能执行很快，这时可能会返回一个非法的句柄。_
 * 3：_endthread将会自动的关闭线程句柄，然而_beginthreadex不会，需要使用CloseHandle现实的关闭句柄。所以_beginthreadex函数可以使用WaitForSingleObject函数来获取线程对象来进行同步。_
 * 4：一个连接Libcmt.lib的可执行文件，不要调用ExitThread函数，这个函数会阻止系统的运行时回收已分配的资源。使用\_endthread and \_endthreadex可以回收已分配的资源然后再调用ExitThread._
 * 5： 可以调用_endthread和_endthreadex显示式结束一个线程。然而，当线程函数返回时，_endthread和_endthreadex 被自动调用。endthread和_endthreadex的调用有助于确保分配给线程的资源的合理回收。_
 * 6：当\_beginthread和\_beginthreadex被调用时，操作系统自己处理线程栈的分配。如果在调用这些函数时，指定栈大小为0，则操作系统 为该线程创建和主线程大小一样的栈。如果任何一个线程调用了abort、exit或者ExitProcess，则所有线程都将被终止。
 * 7：对于使用C运行时库里的函数的线程应该使用_beginthread和_endthread这些C运行时函数来管理线程，而不是使用CreateThread和ExitThread。否则，当调用ExitThread后，可能引发内存泄露。
 * 8: 必须使用多线程版的 C run_time libraries.



### \_beginthreadex用法示例:

```cpp
// class Map;
Map MyMap;

unsigned int WINAPI GetDir(void* lpParameter)
{
    Map * pMap = (Map*)lpParameter;

    while (1)
    {
        char input;
        input = _getch();
        pMap->ChangeDir(input);

    }
}
_beginthreadex(NULL, 0, GetDir, &MyMap, 0, NULL);
```



## AfxBeginThread:

函数原型：

```cpp
//工作者线程
CWinThread* AfxBeginThread(
   AFX_THREADPROC pfnThreadProc,
   LPVOID pParam,
   int nPriority = THREAD_PRIORITY_NORMAL,
   UINT nStackSize = 0,
   DWORD dwCreateFlags = 0,
   LPSECURITY_ATTRIBUTES lpSecurityAttrs = NULL
   );
//用户界面线程
CWinThread* AfxBeginThread(
   CRuntimeClass* pThreadClass,
   int nPriority = THREAD_PRIORITY_NORMAL,
   UINT nStackSize = 0,
   DWORD dwCreateFlags = 0,
   LPSECURITY_ATTRIBUTES lpSecurityAttrs = NULL
   );
```

参数：

pfnThreadProc:线程函数的入口地址。



 函数原型：UINT __cdecl MyControllingFunction( LPVOID pParam );

pThreadClass:继承CWinThread类的RUNTIME_CLASS对象。

pParam: 传递给线程函数的参数，可以为0。

nPriority:线程优先级。

nStackSize:指明线程堆栈的大小，以字节为单位，可以为0。

dwCreateFlags:线程创建标志。

lpSecurityAttrs:线程安全属性。

返回值：

如果成功则返回一个指针指向线程对象，否则为NULL。

说明：

可以调用AfxEndThread来终止线程或者return。

# 这几个函数的联系与区别：

## CreateThread：

CreateThread是Windows的API函数，提供操作系统级别的创建线程的操作。_beginthread(及_beginthreadex)与AfxBeginThread的底层实现都调用了CreateThread函数。
CreateThread函数没有考虑到下面二点：
（1）C Runtime中需要对多线程进行记录和初始化，以保证C函数库工作正常（典型的例子就是strtok函数）
（2）MFC也需要知道新线程的创建，也需要做一些初始化工作。
所以，在不调用MFC和CRT的函数时，可以用CreateThread创建线程，其它情况不要使用。

## AfxBeginThread：

MFC中线程创建的函数，首先创建了相应的CWinThread对象，然后调用CWinThread：：CreateThread，在CWinThread：：CreateThread中完成了对线程对象的初始化工作，然后调用\_beginthreadex创建线程。注意不要在一个MFC程序中使用\_beginthreadex()或CreateThread()。

## \_beginthread和\_beginthreadex:

 (实现文件分别是thread.c和threadex.c)
是MS对C Runtime库的扩展SDK函数，首先对C Runtime库做了一些初始化的工作，以保证C Runtime库工作正常。然后，调用CreateThread真正创建线程。
若要使多线程C和C++程序能够正确地运行，必须创建一个数据结构，并将它与使用C/C++运行期库函数的每个线程关联起来。当你调用C/C++运行期库时，这些函数必须知道查看调用线程的数据块，这样就不会对别的线程产生不良影响。

1.每个线程均获得由C/C++运行期库的堆栈分配的自己的tiddata内存结构。  
2.传递给_beginthreadex的线程函数的地址保存在tiddata内存块中。传递给该函数的参数也保存在该数据块中。（指向tiddata结构的指针会作为一个TLS保存起来）   
3._beginthreadex确实从内部调用CreateThread，因为这是操作系统了解如何创建新线程的唯一方法。
4.当调用CreatetThread时，它被告知通过调用_threadstartex而不是pfnStartAddr来启动执行新线程。还有，传递给线程函数的参数是tiddata结构而不是pvParam的地址。
5.如果一切顺利，就会像CreateThread那样返回线程句柄。如果任何操作失败了，便返回 NULL。

# 总结：

1：CreateThread是由操作系统提供的接口，而AfxBeginThread和\_BeginThread则是编译器对它的封装。

2：用\_beginthreadex()、\_endthreadex函数应该是最佳选择，且都是C Run-time Library中的函数，函数的参数和数据类型都是C Run-time Library中的类型，这样在启动线程时就不需要进行Windows数据类型和C Run-time Library中的数据类型之间的转化，从而减低了线程启动时的资源消耗和时间的消耗。

3：MFC也是C++类库（只不过是Microsoft的C++类库，不是标准的C++类库），在MFC中也封装了new和delete两中运算符，所以用到new和delete的地方不一定非要使用_beginthreadex() 函数，用其他两个函数都可以。


4：\_beginthreadex和\_beginthread在回调入口函数之前进行一些线程相关的CRT的初始化操作。CRT的函数库在线程出现之前就已经存在，所以原有的CRT不能真正支持线程，这也导致了许多CRT的函数在多线程的情况下必须有特殊的支持，不能简单的使CreateThread就可以。

5：如果要作多线程(非MFC)程序，在主线程以外的任何线程内
使用malloc(),free(),new
调用stdio.h或io.h,包括fopen(),open(),getchar(),write(),printf(),errno
使用浮点变量和浮点运算函数
调用那些使用静态缓冲区的函数如: asctime(),strtok(),rand()等。
应该使用多线程的CRT并配合\_beginthreadex(该函数只存在于多线程CRT), 其他情况，你可以使用单线程的CRT并配合CreateThread。因为对产生的线程而言，\_beginthreadex比CreateThread会为上述操作多做额外的工作，比如帮助strtok()为每个线程准备一份缓冲区。
然而多线程程序极少情况不使用上述那些函数(比如内存分配或者io)，所以与其每次都要思考是要使用_beginthreadex还是CreateThread,不如就一棍子敲定\_beginthreadex。_

6：你也许会借助win32来处理内存分配和io，这时候你确实可以以单线程crt配合CreateThread，因为io的重任已经从crt转交给了win32。这时通常你应该使用HeapAlloc，HeapFree来处理内存分配，用CreateFile或者GetStdHandle来处理io。

7：还有一点比较重要的是\_beginthreadex传回的虽然是个unsigned long,其实是个线程Handle(事实上\_beginthreadex在内部就是调用了CreateThread)，所以你应该用CloseHandle来结束他。千万不要使用ExitThread()来退出\_beginthreadex创建的线程，那样会丧失释放簿记数据的机会，应该使用_endthreadex.

## 下面对两个概念进行阐述

### CRT(C/C++ Runtime Library):

是一种函数库，由编译器的生产厂家提供头文件或接口，操作系统提供运行时库的实现。所以Windows和Linux系统的运行时库函数接口虽然一样，但具体实现不一样。
CRT是支持C/C++运行的一系列函数和代码的总称，虽然没有一个很精确的定义，但是可以知道，你的main函数就是它负责调用的，还有平时使用的strlen，strtok，time，atoi之类的函数也是它提供的。

### 线程局部存储（TLS，thread local storage）

一个多线程程序中，全局变量（及分配的内存）被所有线程所共享。函数的静态局部变量也被所有使用该函数的线程所共享。一个函数中的自动变量对每一个线程是唯一的，因为它们存储于堆栈上，而每个线程都有他们自己的堆栈。有时，我们需要对每一个线程唯一的持续性存储。例如，C函数strtok就需要这种存储。不幸的是，C语言不支持这种变量。但是Windows提供了四个API函数来实现这种机制。我们把这种存储称为线程局部存储（TLS，Thread Local Storage）。
首先，定义一个结构，把对每个线程唯一的数据包含在该结构中。
例如：
typedef struct
{
int one;
int two;   
} DATA, *PDATA;


然后，主线程调用TlsAlloc函数来为进程获得一个TLS索引：tlsIndex = TlsAlloc();该TLS索引可以存储于一个全局变量或者通过线程函数的参数传递给其它线程。每个需要使用该TLS索引的线程，先动态分配内存，然后调用TlsSetValue函数将该内存关联到该TLS索引（及该线程）： TlsSetValue(tlsIndex, GlobalAlloc(GPTR, sizeof(DATA));
此时，线程直接或间接调用的函数可以通过如下方式获得该线程的TLS存储区域：
PDATA pdata；
pdata = (PDATA) TlsGetValue(tlsIndex);
此时，就可以使用该线程的TLS存储区的变量了。


当线程函数终止时，它应该释放它所分配的动态空间： GlobalFree(TlsGetValue(tlsIndex));
当所有使用TLS的线程都终止后，主线程应当释放该TLS存储空间： TlsFree(tlsIndex);


TLS可以以一种更简单的方式使用，那就是通过Winodws对C所作的扩展关键字__declspec和扩展存储类型修饰符thread。例如：
__declspec(thread) int global_tls_i = 1;        // 在函数外部，声明一个TLS变量
__declspec(thread) static int local_tls_i = 2;  // 在函数内部声明一个静态TLS变量