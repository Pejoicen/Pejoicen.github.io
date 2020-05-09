---
title: "MFC学习"
date: 2019-08-14T10:10:10+08:00
description: ""
draft: false
tags: ["MFC","C/C++","Windows"]
categories: ["软件"]
---

VS2008 MFC学习

<!--more-->
2019年8月23日16:44:04 更新，精简内容；


参考：

<http://c.biancheng.net/cpp/mfc/> MFC入门教程：1天深入浅出MFC

<https://www.cnblogs.com/muouln/p/8036693.html> 深入浅出话VC++——MFC的本质

<http://www.jizhuomi.com/school/c/149.html> VS2010/MFC编程入门之前言

VS2008新建FMC工程步骤：
=======================

文件新建项目

![](/media-MFC学习/4891424f4ed65001e525ac0d10de9149.png)

选择MFC应用程序

![](/media-MFC学习/b1d79887a42e5fb1439782af1debfb53.png)

![](/media-MFC学习/1214643f95096472ebfd0e7d08e12ccb.png)

[应用程序类型](#应用程序类型说明)：选择单文档

![](/media-MFC学习/a78845e75555630f8f54ee4ea909ea4d.png)

出现MFC向导的第三个对话框，复合文档支持保持默认选择，然后在出现的对话框中一直点击下一步来完成一个单文档MFC应用程序的创建。

![](/media-MFC学习/9ad414bf7c395e80216639c3dc69ca30.png)

![](/media-MFC学习/031de815d6a5667e95c0498ff60ff0b3.png)

![](/media-MFC学习/c08da51c4b3eda6aeb606ce89ff4f9a1.png)

![](/media-MFC学习/c0453538bcf3741516207752c52ad2e2.png)

![](/media-MFC学习/c4e95aaa0c88efc67932eb3915204352.png)

下面，按下Ctrl+F5来运行MFC应用程序，之后将看到我们创建的MFC应用程序界面，具体如下图所示：

![](/media-MFC学习/e343d7a60f62c07609328125b0665360.png)

**应用程序类型说明**：
----------------------

**单文档：**记事本类程序的标准模式，有菜单栏、工具栏等，只能进行一份文档的操作，即不能同时在同一个应用程序中打开两个文件

**多文档：**WORD类或浏览器程序的标准模式，可多个窗口显示不同的信息，进行不同的任务，有多个视图环境，可同时操作多个文件。多个文件共享同一菜单栏、工具栏等

**对话框：**可方便的使用控件，所见即所得的编程，没有菜单栏、工具栏等

怎样看出一个MFC应用程序是基于单文档、多文档还是对话框？

1.运行应用程序观看，对话框一般没有菜单栏、工具栏等，单文档和多文档有，另多文档可产生子窗口，会有一个childframe
class

2.从源码中的类观看：

基于对话框—\>一般含以下3个类：CAboutDlg、程序名App、程序名Dlg

基于单文档—\>一般含以下4个类：CMainFrame、程序名App、程序名Doc、程序名View

基于多文档—\>一般含以下5个类：CMainFrame、CChildFrame、程序名App、程序名Doc、程序名View

应用程序工程中文件的组成结构
============================

<http://www.jizhuomi.com/school/c/152.html>

鸡啄米将所有文件分为6个部分**：解决方案相关文件、工程相关文件、应用程序头文件和源文件、资源文件、预编译头文件和编译链接生成文件。**

**1.解决方案相关文件**

解决方案相关文件包括解决方案文件夹下的.sdf文件、.sln文件、.suo文件和ipch文件夹。

**.sdf文件和ipch目录**一般占用空间比较大，几十兆甚至上百兆，与智能提示、错误提示、代码恢复和团队本地仓库等相关。如果你觉得不需要则可以设置不生成它们，方法是点击菜单栏Tools-\>Options，弹出Options对话框，选择左侧面板中Text
Editor-\>C/C++-\>Advanced，右侧列表中第一项Disable
Database由False改为True就可以了，最后关闭VS2010再删除.sdf文件和ipch目录以后就不会再产生了。但关闭此选项以后也会有很多不便，例如写程序时的智能提示没有了。

**.sln文件和.suo**文件为MFC自动生成的解决方案文件，它包含当前解决方案中的工程信息，存储解决方案的设置。

** 2.工程相关文件**

工程相关文件包括工程文件夹下的.vcxproj文件和.vcxproj.filters文件。

.vcxproj文件是MFC生成的工程文件，它包含当前工程的设置和工程所包含的文件等信息。.vcxproj.filters文件存放工程的虚拟目录信息，也就是在解决方案浏览器中的目录结构信息。

** 3.应用程序头文件和源文件**

应用程序向导会根据应用程序的类型（单文档、多文档或基于对话框的程序）自动生成一些头文件和源文件，这些文件是工程的主体部分，用于实现主框架、文档、视图等。鸡啄米下面分别简单介绍下各个文件：（HelloWorld为工程名）

HelloWorld.h：**应用程序的主头文件**。主要包含由CWinAppEx类派生的**CHelloWorldApp类**的声明，以及CHelloWorldApp类的**全局对象theApp**的声明。

HelloWorld.cpp：**应用程序的主源文件**。主要包含CHelloWorldApp类的实现，CHelloWorldApp类的全局对象theApp的定义等。

MainFrm.h和MainFrm.cpp：通过这两个文件从CFrameWndEx类派生出CMainFrame类，用于**创建主框架、菜单栏、工具栏和状态栏等**。

HelloWorldDoc.h和HelloWorldDoc.cpp：这两个文件从CDocument类派生出文档类CHelloWorldDoc，包含一些用来初始化文档、串行化（保存和装入）文档和调试的成员函数。

HelloWorldView.h和HelloWorldView.cpp：它们**从CView类派生出名为CHelloWorldView的视图类**，用来显示和打印文档数据，**包含了一些绘图和用于调试的成员函数**。

ClassView.h和ClassView.cpp：由CDockablePane类派生出CClassView类，用于实现应用程序界面左侧面板上的Class
View。

FileView.h和FileView.cpp：由CDockablePane类派生出CFileView类，用于实现应用程序界面左侧面板上的File
View。

OutputWnd.h和OutputWnd.cpp：由CDockablePane类派生出COutputWnd类，用于实现应用程序界面下侧面板Output。

PropertiesWnd.h和PropertiesWnd.cpp：由CDockablePane类派生出CPropertiesWnd类，用于实现应用程序界面右侧面板Properties。

ViewTree.h和ViewTree.cpp：由CTreeCtrl类派生出CViewTree类，用于实现出现在ClassView和FileView等中的树视图。

**4.资源文件**

一般我们使用MFC生成窗口程序都会有对话框、图标、菜单等资源，应用程序向导会生成资源相关文件：res目录、HelloWorld.rc文件和Resource.h文件。

res目录：工程文件夹下的res目录中含有应用程序默认图标、工具栏使用图标等图标文件。

HelloWorld.rc：包含默认菜单定义、字符串表和加速键表，指定了默认的About对话框和应用程序默认图标文件等。

Resource.h：含有各种资源的ID定义。

**5.预编译头文件**

几乎所有的MFC程序的文件都要包含afxwin.h等文件，如果每次都编译一次则会大大减慢编译速度。所以把常用的MFC头文件都放到了stdafx.h文件中，然后由stdafx.cpp包含stdafx.h文件，编译器对stdafx.cpp只编译一次，并生成编译之后的**预编译头HelloWorld.pch**，大大提高了编译效率。

**6.编译链接生成文件**

如果是Debug方式编译，则会在解决方案文件夹和工程文件夹下都生成Debug子文件夹，而如果是Release方式编译则生成Release子文件夹。

工程文件夹下的Debug或Release子文件夹中包含了编译链接时产生的中间文件，解决方案文件夹下的Debug或Release子文件夹中主要包含有应用程序的可执行文件。

MFC框架结构的分析
=================

框架代码文件的结构
------------------

基于MFC对话框程序的框架结构如图1-3-1所示：

![http://c.biancheng.net/cpp/uploads/allimg/151221/1-151221100045940.png](/media-MFC学习/a96b48f3e34547d050dc53f1ccfee35c.png)

图1-3-1 基于MFC对话框程序的框架代码结构示意图

主要有4个部分组成：

1) 资源文件Resource.h：主要是定义资源的ID。

2) 预编译文件：可以用来解决头文件包含冲突的问题，定义一些需要全局性包含的文件。

3) 应用程序类对应文件：项目名称是TestOne，对应类名为CTestOneApp。

4) 对话框类：项目名称是TestOne，对应类名为CTestOneDlg。

### 文件介绍：

头文件：

-   App 应用类

-   Dlg 对话框类

-   Doc 文本类

-   View 绘图类

源文件：

-   App 应用类

-   Dlg 对话框类

-   Doc 文本类

-   View 绘图类

资源文件：

rc和rc2都是资源文件,包含了应用程序中用到的所有的资源。

两者不同在于：rc文件中的资源可以直接在VC集成环境中以可视化的方法进行编辑和修改;

而rc2中的资源不能在VC的集成环境下直接进行编辑和修改,
而是由根据需要手工地进行编辑。

Rc文件中共有如下内容：

![](/media-MFC学习/4f0361c137ae823707274d19bc6c92fb.png)

应用程序类
----------

**MFC定义了一个应用程序基类CWinApp**，所有基于MFC的应用程序都会继承这个类。TestOne项目也不例外，此时的应用程序类是CTestOneApp，定义如下：

1.  **class** CTestOneApp : **public** CWinApp

2.  {

3.  **public**:

4.  CTestOneApp();

5.  **public**:

6.  // 重载虚函数

7.  **virtual** BOOL InitInstance();

8.  // 实现

9.  DECLARE_MESSAGE_MAP()

10. };

这个类的定义很简单，**重点来看一下InitInstance函数**：

1.  BOOL CTestOneApp::InitInstance()

2.  {

3.  //初始化应用程序环境包控件等操作，省略部分代码.

4.  CWinApp::InitInstance();..

5.  //省略部分代码...

6.  //定义对话框对象

7.  CTestOneDlg dlg;

8.  //保存对话框到全局变量

9.  m_pMainWnd = \&dlg;

10. //显示对话框

11. INT_PTR nResponse = dlg.DoModal();

12. **if** (nResponse == IDOK)

13. {

14. //“确定”来关闭对话框的代码

15. }

16. **else if** (nResponse == IDCANCEL)

17. {

18. //“取消”来关闭对话框的代码

19. }

20. // 由于对话框已关闭，所以将返回 FALSE 以便退出应用程序，
    而不是启动应用程序的消息泵。

21. **return** FALSE;

22. }

这个函数可以看作MFC程序的入口函数，main函数隐藏在这个函数中。在实际的开发中，一般不需要对这个类进行操作，但如果需要在建立主对话框之前处理一些数据或者准备工作，那么就可以把代码添加到这个函数中，主对话框显示之前。

这里有两个比较个典型的应用。

1.  启动界面之前弹出登陆界面，需要正确输入用户名和密码才能弹出主界面；

2.  启动界面之前，弹出一个项目配置界面，例如操作员，设备编号，项目名称，批次等信息，同时这些信息需要传递到主界面中，主界面根据项目和批次决定某种操作方式。

3.  上面两个应用在实际的项目开发中很常见，需求比较普遍。

第一个需求不涉及数据传递，可以在代码中将用户名和密码固定，如果输入的结果与设定的结果一致，那么就开放此软件的使用权限；稍微复杂一点的，可以比较输入的结果与数据库中存储的用户名和密码是否匹配，如果匹配，才开放此软件的使用权限；再稍微复杂一点的，还可以将界面输入结果与服务器中的信息比较，查看当前用户是否有此软件的使用权限，当然这个还涉及到服务端的处理，与网络通信有关。

第二个需求涉及不同对话框之间的数据传递，应用也比较广泛。

篇幅有限，这里不再展示这两个需求的源码，源码统一在本教程的源码获取链接中获取。

框架类和视图类
--------------

CMainFrame是视图CHelloWorldView的父窗口，视图CHelloWorldView就显示在CMainFrame的客户区中。视图类CHelloWorldView用来显示文档类CHelloWorldDoc中的数据，并根据对视图类的操作修改文档类的数据。一个视图类只能跟一个文档类相联系，而一个文档类可以跟多个视图类相联系

对话框类
--------

MFC主对话框类（主对话框是指与项目名称一致的那个类对应的对话框，下同）CTestOneDlg继承CDialogEx类，CDialogEx又继承CDialog类，这个可以通过类转到定义查看，对话框类负责与用户交互，处理用户消息，接受用户输入。类的定义如下：

1.  // CTestOneDlg 对话框

2.  **class** CTestOneDlg : **public** CDialogEx

3.  {

4.  **public**:

5.  // 标准构造函数

6.  CTestOneDlg(CWnd\* pParent = NULL);

7.  // 对话框数据

8.  **enum** { IDD = IDD_TESTONE_DIALOG };

9.  **protected**:

10. // 动态数据交换，负责控件与变量之间的关联

11. **virtual** void DoDataExchange(CDataExchange\* pDX);

12. **protected**:

13. //应用程序句柄

14. HICON m_hIcon;

15. //重载初始化对话框

16. **virtual** BOOL OnInitDialog();

17. //定义消息WM_SYSCOMMAND处理函数

18. afx_msg void OnSysCommand(UINT nID, LPARAM lParam);

19. //定义消息WM_PAINT处理函数

20. afx_msg void OnPaint();

21. //定义消息ON_WM_QUERYDRAGICON处理函数

22. afx_msg HCURSOR OnQueryDragIcon();

23. //消息映射

24. DECLARE_MESSAGE_MAP()

25. };

从这个类的定义可以看出以下几点：

1.  控件与数据关联，可以简单地交给框架来实现；

2.  在MFC框架上开发主要是针对消息处理机制。

添加消息响应
------------

MFC消息响应机制过程比较复杂，这里因为还只是基础部分，不做深入的探讨，只做一个简单的过程演示，给读者一个初步的印象，后面会在专门的章节中讨论这个问题。本节演示在界面中添加一个按钮的消息响应。

**首先把工作区间切换到“资源视图”**，如果工作区间没有显示资源视图，在菜单中的视图中查找“资源视图”并点击，这样工作区间就会显示出来。切换到资源视图后，找到Dialog文件夹**找到IDD_TESTONE_DIALOG**，如图1-3-2所示。

![](/media-MFC学习/49bb1146b75897f59c5457c18e58f362.png)

>   http://c.biancheng.net/cpp/uploads/allimg/151221/1-151221100642294.png

图1-3-2 MFC对话框程序资源视图示例

双击图1-3-2中选中的对话框ID就会显示这个对话框窗口，从**工具栏**中选择“Button”按钮后，可以**直接将按钮拖至显示的这个对话框窗口中**。说明一下，工具栏只有当主工作区间显示对话框时才会显示，初学者可能会碰到找不到工具栏的情况。用鼠标右键点击按钮，选择“属性”切换到属性页，修改按钮的名称（对应Caption）“Test”，同时修改按钮的ID为IDC_BTN_TEST。有两种方式可以完成添加按钮的消息处理函数。

1) 直接双击按钮；

2) 右键选中按钮，点击“添加事件处理程序”；

两种方式区别在于，第一种方式添加的消息响应函数名称是默认的，第二种方式添加的消息响应函数名称是可以编辑的。

![http://c.biancheng.net/cpp/uploads/allimg/151221/1-151221100R2152.png](/media-MFC学习/249adf742cbbf96a67776be507e969fb.png)

  
图1-3-3 MFC添加按钮消息响应事件处理向导

代码如下所示：

1.  //添加按钮IDC_BTN_TEST的单击消息处理函数

2.  void CTestOneDlg::OnBtnTest()

3.  {

4.  AfxMessageBox(_T("测试成功！"));

5.  }

添加一个MessageBox之后，编译并执行。在软件中添加我们添加的“Test”按钮之后，出现消息框，如图1-3-4所示。

![http://c.biancheng.net/cpp/uploads/allimg/151221/1-151221100933363.png](/media-MFC学习/05a0245c4c36fb97c5f83f4d0034f154.png)

图1-3-4显示处理按钮的消息

MFC消息映射机制概述
===================

<http://www.jizhuomi.com/school/c/154.html>

前面已经说过，Windows应用程序是消息驱动的。在MFC软件开发中，界面操作或者线程之间通信都会经常用到消息，通过对消息的处理实现相应的操作。比较典型的过程是，用户操作窗口，然后有消息产生，送给窗口的消息处理函数处理，对用户的操作做出响应。

什么是消息
----------

      
窗口消息一般由三个部分组成：(1)一个无符号整数，是消息值；(2)消息附带的WPARAM类型的参数；(3)消息附带的LPARAM类型的参数。其实我们一般所说的消息是狭义上的消息值，也就是一个无符号整数，经常被定义为宏。

什么是消息映射机制
------------------

      
MFC使用一种消息映射机制来处理消息，在应用程序框架中的表现就是一个消息与消息处理函数一一对应的消息映射表，以及消息处理函数的声明和实现等代码。当窗口接收到消息时，会到消息映射表中查找该消息对应的消息处理函数，然后由消息处理函数进行相应的处理。SDK编程时需要在窗口过程中一一判断消息值进行相应的处理，相比之下MFC的消息映射机制要方便好用的多。

Windows消息分类
---------------

      
先讲下Windows消息的分类。Windows消息分为系统消息和用户自定义消息。Windows系统消息有三种：

      
1.**标准Windows消息。**除WM_COMMAND外以WM_开头的消息是标准消息。例如，WM_CREATE、WM_CLOSE。

      
2.**命令消息。**消息名为WM_COMMAND，消息中附带了标识符ID来区分是来自哪个菜单、工具栏按钮或加速键的消息。

      
3.**通知消息。**通知消息一般由列表框等子窗口发送给父窗口，消息名也是WM_COMMAND，其中附带了控件通知码来区分控件。

       CWnd的派生类都可以接收到标准Windows消息、通知消息和命令消息。命令消息还可以由文档类等接收。

      
用户自定义消息是实际上就是用户定义一个宏作为消息，此宏的值应该大于等于WM_USER，然后此宏就可以跟系统消息一样使用，窗口类中可以定义它的处理函数。

消息映射表
----------

      
除了一些没有基类的类或CObject的直接派生类外，其他的类都可以自动生成消息映射表。下面的讲解都以前面例程HelloWorld的CMainFrame为例。消息映射表如下：

| 1 2 3 4 5 6 7 8 | BEGIN_MESSAGE_MAP(CMainFrame, CFrameWndEx)      ON_WM_CREATE()      ON_COMMAND(ID_VIEW_CUSTOMIZE, &CMainFrame::OnViewCustomize)      ON_REGISTERED_MESSAGE(AFX_WM_CREATETOOLBAR, &CMainFrame::OnToolbarCreateNew)      ON_COMMAND_RANGE(ID_VIEW_APPLOOK_WIN_2000, ID_VIEW_APPLOOK_WINDOWS_7, &CMainFrame::OnApplicationLook)      ON_UPDATE_COMMAND_UI_RANGE(ID_VIEW_APPLOOK_WIN_2000, ID_VIEW_APPLOOK_WINDOWS_7, &CMainFrame::OnUpdateApplicationLook)      ON_WM_SETTINGCHANGE()    END_MESSAGE_MAP() |
|-----------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|


     
在BEGIN_MESSAG_MAP和END_MESSAGE_MAP之间的内容成为消息映射入口项。消息映射除了在CMainFrame的实现文件中添加消息映射表外，在类的定义文件MainFrm.h中还会添加一个宏调用：

       DECLARE_MESSAGE_MAP()

       一般这个宏调用写在类定义的结尾处。

       **添加消息处理函数**

       如何添加消息处理函数呢？不管是自动还是手动添加都有三个步骤：

      
1.在类定义中加入消息处理函数的函数声明，注意要以afx_msg打头。例如MainFrm.h中WM_CREATE的消息处理函数的函数声明：afx_msg
int OnCreate(LPCREATESTRUCT lpCreateStruct);。

      
2.在类的消息映射表中添加该消息的消息映射入口项。例如WM_CREATE的消息映射入口项：ON_WM_CREATE()。

      
3.在类实现中添加消息处理函数的函数实现。例如，MainFrm.cpp中WM_CREATE的消息处理函数的实现：

| 1 2 3 4 | int CMainFrame::OnCreate(LPCREATESTRUCT lpCreateStruct) {            ...... } |
|---------|-------------------------------------------------------------------------------|


      
通过以上三个步骤以后，WM_CREATE等消息就可以在窗口类中被消息处理函数处理了。

       **各种Windows消息的消息处理函数**

       标准Windows消息的消息处理函数都与WM_CREATE消息类似。

       命令消息的消息映射入口项形式如：ON_COMMAND(ID_VIEW_CUSTOMIZE,
&CMainFrame::OnViewCustomize)，消息为ID_VIEW_CUSTOMIZE，消息处理函数为OnViewCustomize。

      
如果想要使用某个处理函数批量处理某些命令消息，则可以像CMainFrame消息映射表中的ON_COMMAND_RANGE(ID_VIEW_APPLOOK_WIN_2000,
ID_VIEW_APPLOOK_WINDOWS_7,
&CMainFrame::OnApplicationLook)一样添加消息映射入口项，这样值在ID_VIEW_APPLOOK_WIN_2000到ID_VIEW_APPLOOK_WINDOWS_7之间的菜单项等的命令消息都由CMainFrame的OnApplicationLook函数处理。函数原型为afx_msg
void OnApplicationLook(UINT id);，参数id为用户操作的菜单项等的ID。

      
在操作列表框等控件时往往会给父窗口发送WM_NOTIFY通知消息。WM_NOTIFY消息的wParam参数为发送通知消息的控件的ID，lParam参数指向一个结构体，可能是NMHDR结构体，也可能是第一个元素为NMHDR结构体变量的其他结构体。NMHDR结构体的定义如下（仅作了解）：

| 1 2 3 4 5 | Typedef sturct tagNMHDR{     HWND hwndFrom;     UINT idFrom;     UINT code; } NMHDR; |
|-----------|--------------------------------------------------------------------------------------|


       hwndFrom为发送通知消息控件的句柄，idFrom为控件ID，code为要处理的通知消息的通知码，例如NM_CLICK。

       通知消息的消息映射入口项形式如：

       ON_NOTIFY(wNotifyCode,id,memberFxn)

       wNotifyCode为要处理的通知消息通知码，例如：NM_CLICK。id为控件标识ID。MemberFxn为此消息的处理函数。

       通知消息的处理函数的原型为：

       afx_msg void memberFxn( NMHDR \* pNotifyStruct, LRESULT \* result);

       如果需要使用用户自定义消息，首先要定义消息宏，如：\#define WM_UPDATE_WND
(WM_USER+1)，再到消息映射表中添加消息映射入口项：ON_MESSAGE(WM_UPDATE_WND,
&CMainFrame::OnUpdateWnd)，然后在MainFrm.h中添加消息处理函数的函数声明：afx_msg
LRESULT OnUpdateWnd(WPARAM wParam, LPARAM
lParam);，最后在MainFrm.cpp中实现此函数。

鸡啄米本节对MFC消息映射机制只是做了比较简单的讲解，让大家对它有一定的认识，编程入门者不必强求完全掌握。在以后的教程中会经常涉及到消息的使用，大家会逐渐熟悉MFC的消息映射机制。

MFC中典型类和函数的介绍
=======================

在MFC中，典型的类有CString、CRect、CDialog等等。这里篇幅有限，以CString类的使用为例做一个详细说明。类的使用主要还是使用类的方法，可以查看类的定义，查看这个类提供了哪些方法，在使用的时候，只需要注意方法的返回值和参数即可。这些类的使用方法是通用的，大家在学习的时候要注意体会和总结。

MFC字符串操作类CString
----------------------

CString是用来表示可变长度的字符串的类。在MFC中，这个类的使用频率非常高。CString是MFC经过封装后特有的一种字符串类型，较string或iftream或oftream的字符串有更多明显的有点，如使用CString可不指明内存大小，CString会根据需要自行分配，除此之外还可以对字符串进行拼接操作。因为MFC已经对这个类进行了封装，我们可以在MFC程序任何地方调用它提供的一系列操作字符串的方法，示例代码如下：

```cpp
//CString 使用方法示例
CString strTemp=_T("MFC Tutorial");
//(1)获取字符串的长度
//结果： iLength=12,中间的空格也算一个字符
int iLength=strTemp.GetLength();

//(2)获取字符串strTemp的第三个字符
//结果：cFlag=‘C’
char cFlag=strTemp.GetAt(2);

//(3)数据类型转换 将char型字符转换为CString类型
数据类型的转换内容较多，后面会单独讨论
//结果：strS=_T("C");
CString strS=_T("");
strS.Format(_T("%c"),cFlag);

//(4)字符串比较 Compare方法，如果两个一致，返回0;不一致，返回-1
//结果：iResult0==0；iResult1==-1；iResult2=1
int iResult0=strTemp.Compare(_T("MFC Tutorial"));
int iResult1=strTemp.Compare(_T("MFC TutorialX"));
int iResult2=strTemp.Compare(_T("MFC TutoRiaL"));

//(5)字符串比较 CompareNoCase方法，如果两个不区分大小写一致，返回0;
//结果：iResult3==0；iResult4==0；iResult5==-120
int iResult3=strTemp.CompareNoCase(_T("MFC Tutorial"));
int iResult4=strTemp.CompareNoCase(_T("MFC TutoRiaL"));
int iResult5=strTemp.CompareNoCase(_T("MFC TutoRiaLX"));

//(5)改为小写
//结果mfc tutorial
CString strLower=strTemp.MakeLower();

//(6)改为大写
//结果MFC TUTORIAL

CString strUpper=strTemp.MakeUpper();

//(6)获取逆序的字符串
//结果LAIROTUT CFM

CString strReverse=strTemp.MakeReverse();
```
对代码的说明：

1) CString
是MFC提供的类，这个例子中列举了这个类的几个使用方法。这些方法是如何封装的对我们使用者来说是透明的，我们不可暂时不必深究。暂时只需要掌握如何使用这些方法即可。使用的时候，也只需关注方法的原型，参数和返回值即可。同样，对于使用
MFC提供的其他类封装的方法原理是一样的，希望大家仔细体会，融会贯通。

2)
CString在MFC中应用非常广泛，后面将会给出这个类的具体使用方法示例，可以本章的从源码库中获取。

典型函数
--------

在MFC中，经常会看到以Afx开头的一些函数，如下所示：

1.  CWindApp\* AfxGetApp();

2.  HINSTANCE AfxGetInstanceHandle( );

3.  HINSTANCE AfxGetResourceHandle( );

4.  int AfxMessageBox( LPCTSTR lpszText, UINT nType = MB_OK, UINT nIDHelp = 0 );

这里说明一下，Afx前缀的函数代表应用程序框架（Application
Framework）函数。应用程序框架实际上是一套辅助我们生成应用程序的框架模型。该模型把多个类进行了一个有机的集成，可以根据该模型提供的方案来设计自己的应用程序。在MFC中，以Afx为前缀的函数都是全局函数，可以在程序的任何地方调用他们。

MFC视图和窗口的基本概念
=======================

视（View）类窗口
----------------

**视类窗口是指程序运行后，显示信息的那一部分。对应的类是CXXXView**（XXX表示项目名称）类，CXXXView类是派生于CView类，而CView类又派生于CWnd类。他们的继承关系可以通过查找类的定义来查看继承关系。视图类CView，拥有窗口的客户区域，负责显示文档数据，接受用户的输入，提供文档与用户的通信。视类窗口只是主框架窗口中空白的部分。

主框架（MainFrame）窗口
-----------------------

主框架窗口是指程序运行后，程序的整个界面。对应的类是CMainFrame，CMainFrame类派生于CFrameWnd类，而CFrameWnd类又又派生于CWnd类。主框架窗口就是整个应用程序外框所包括的部分，包括了图3-1-1的全部内容。

框架窗口是视类窗口的一个父窗口（这里暂时记住结论，各个窗口之间的关系会在后面的章节中单独讨论，目前还只针对初学者），他们之间的关系如图3-1-1所示：

![http://c.biancheng.net/cpp/uploads/allimg/151227/1-15122GZ441919.png](/media-MFC学习/3d31e82a5939e35f7fd1798181c0c7f5.png)

图3-1-1  主框架窗口和视窗口之间的关系

文档（Doc）
-----------

文档类CDcoument负责维护应用程序所需要的数据，提供一系列可对这些数据进行操作的方法，并且能够为视图提供所需的数据。

>   新建一个MFC单文档应用程序，项目名称为SDITest。项目结构示意图如图3-1-2所示：

![http://c.biancheng.net/cpp/uploads/allimg/151227/1-15122GZ55c13.png](/media-MFC学习/8fcfa24f76b0dfb7c09c1b7d862dc627.png)

图3-1-2 MFC单文档程序结构示意图

上图中可以看到SDITest程序中还有一个CSDITestDoc类，它派生于CDocument类，CDocument类的基类是CCmdTarget，而CCmdTarget又派生于CObject类，从而可知这个CSDITestDoc类不是一个窗口类，实际上是一个文档类。

**MFC提供的文档/视（Document/View）结构**，其中文档是指CDocument类，视类是指CView类。Microsoft在设计基础类库时，考虑到要**把数据本身与它的显示分离开**，于是就采用文档类和视类结构来实现这一想法。数据的存储和加载由文档类来完成，数据的显示和修改则由视类完成，从而把数据的管理和显示方法分离开来。文档/视类结构是MFC程序的一个重点，在后面的中级部分会详细地介绍这部分内容。这里希望大家能够有一个初步的印象。

下面再介绍一下MFC单文档程序是如何将文档类对象、框架对象、视类对象组织在一起的。还是以SDITest项目为例，在源文件SDITest.cpp中找到CSDITestApp类的InitInstance函数定义，有下面这一段代码：

1.  // 注册应用程序的文档模板。文档模板将用作文档、框架窗口和视图之间的连接

2.  CSingleDocTemplate\* pDocTemplate;

3.  pDocTemplate = **new** CSingleDocTemplate(

4.  IDR_MAINFRAME,

5.  RUNTIME_CLASS(CSDITestDoc),

6.  RUNTIME_CLASS(CMainFrame), // 主 SDI 框架窗口

7.  RUNTIME_CLASS(CSDITestView)

8.  );

9.  **if** (!pDocTemplate)

10. **return** FALSE;

11. AddDocTemplate(pDocTemplate);

从上面的代码段中可以看到其中定义了一个单文档模板对象指针pDocTemplate，这个对象把文档对象、框架对象、视类对象有机地组织在一起，形成了一个有机的整体，程序接着利用AddDocTemplate函数把这个单文档模板添加到文档模板中，从而把这三个类组织为一个整体。

MFC 文档、视图和框架窗口间的关系和消息传送
==========================================

在上一节中对文档、视图和框架窗口做了简单地介绍，总结一下：**文档相当于数据容器**，**视（View）相当于查看数据的窗口或是和数据发生交互的窗口**。（这一结构在MFC中的OLE，ODBC开发时又得到更多的拓展）因此**一个完整的应用一般由四个类组成：CWinApp应用类，CFrameWnd窗口框架类，CDocument文档类，CView视类**。

在程序运行时CWinApp将创建一个CFrameWnd框架窗口实例，而框架窗口将创建文档模板，然后有文档模板创建文档实例和视实例，并将两者关联。一般来讲我们只需对文档和视进行操作，框架的各种行为已经被MFC封装好了，不需人为干预，这也是Microsoft设计文档-视结构的主旨，使界面和逻辑分离，让开发人员将主要的精力放在程序的算法和功能实现上。

在应用中一个视对应一个文档，但一个文档可以包含多个视。一个应用中只用一个框架窗口，对多文档界面来讲可能有多个MDI子窗口。每一个视都是一个子窗口，在单文档界面中父窗口即是框架窗口，在多文档界面中父窗口为MDI子窗口。一个多文档应用中可以包含多个文档模板，一个模板定义了一个文档和一个或多个视之间的对应关系。同一个文档可以属于多个模板，但一个模板中只允许定义一个文档。同样一个视也可以属于多个文档模板。

接下来看看如何在程序中得到各种对象的指针：

全局函数AfxGetApp可以得到CWinApp应用类指针。

AfxGetApp()-\>m_pMainWnd为框架窗口指针。

在框架窗口中：CFrameWnd::GetActiveDocument得到当前活动文档指针。

在框架窗口中：CFrameWnd::GetActiveView得到当前活动视指针。

在视中：CView::GetDocument得到对应的文档指针。

在文档中：CDocument::GetFirstViewPosition，CDocument::GetNextView用来遍历所有和文档关联的视。

在文档中：CDocument::GetDocTemplate得到文档模板指针。

在多文档界面中：CMDIFrameWnd::MDIGetActive得到当前活动的MDI子窗口。

一般来讲用户输入消息（如菜单选择，鼠标，键盘等）会首先发送视，如果视未处理则会发往框架窗口。所以定义消息映射时定义在视中就可以了，如果一个应用同时拥有多个视而当前活动视没有对消息进行处理则消息会发往框架窗口。

其他内容（略）
==============

[MFC中所提供的各种视类介绍](http://c.biancheng.net/cpp/html/3085.html)

[MFC文档与视图结构](http://c.biancheng.net/cpp/html/3086.html)

[MFC单文档与多文档](http://c.biancheng.net/cpp/html/3087.html)

[MFC图形设备接口](http://c.biancheng.net/cpp/html/3088.html)

[MFC画笔CPen类](http://c.biancheng.net/cpp/html/3089.html)

[MFC画刷CBrush类](http://c.biancheng.net/cpp/html/3090.html)

[MFC使用GDI对象CBitmap示例](http://c.biancheng.net/cpp/html/3091.html)

添加资源（窗口、菜单等）
========================

![](/media-MFC学习/8017771d16aa137bc4489bb6c3f21b55.png)

在资源视图中，右击选择添加资源，可以添加如图所示类型的资源。

对话框
======

MFC常用控件
========

List Control 表格
-----------------

![](/media-MFC学习/dc502b99e9850f45b11911c4dda25f90.png)

右键“添加变量”，

![](/media-MFC学习/75be3b6051f2d14191e5a9249b3044a3.png)

需要改的只有变量名，其余不需要更改。

添加完毕后，会在对应的类的头文件和源文件中自动添加内容。

插入数据：

InserColumn插入列

SetItemText设置内容

![](/media-MFC学习/53a76ffc55e801761a08b72732498a56.png)

红绿灯
------

对话框上新建一个Static Text，在对应的头文件，类成员中新加一个CLed对象；

![](/media-MFC学习/4914e6cbfd5f6fc48c5bc49778b7bd54.png)

在对应的源文件中，将Static Text对应到CLed。

![](/media-MFC学习/dd9969debd1703ed074372c44f153955.png)

更改LED颜色

![](/media-MFC学习/ecaef5942435700cec16e945ac0d33a4.png)

Combox
------

<https://blog.csdn.net/yunqi415/article/details/6963720>

添加Combox控件

在对应对话框类中添加Combox成员，CCombox xxx（自定义名称）

![](/media-MFC学习/606f8623cad28d832a3d7c6423997636.png)

在窗口对应的源文件中绑定控件ID和控件成员名称

![](/media-MFC学习/0dfd680ae28f50935db998d5ffeb87c8.png)

m_droplist即对应刚添加的控件，控件ID可在属性中看到。

Checkbox
--------

添加checkbox

在消息映射函数中增加消息映射，在VS208中双击控件会添加默认的处理函数。

![](/media-MFC学习/dd7faae146440475ec4ae9d2894b983e.png)

并且自动添加消息映射。

![](/media-MFC学习/f14855dfd7fc32d824f8e20c0ce6201d.png)

其实可以手动指定，右击控件。

![](/media-MFC学习/aa889f9f1fe22b44db9c1bdb643e1800.png)

可指定消息处理函数。

![](/media-MFC学习/626919df2f8d720bc6b02c1790fc14e1.png)

并且会自动在Resource.h中定义ID值

![](/media-MFC学习/7d2892601f4aa58cc248685b462e7b9b.png)

# MFC常用函数


有两类函数，一类是消息对应的响应函数，还有一类是窗口类（或其他父类）中定义好的函数。对前者来说，需要操作时添加消息映射，对后者来说需要重载函数。

定时器就是一个消息映射的例子。

![](/media-MFC学习/15ffc516bc8d998890f3dcae892b3123.png)

![](/media-MFC学习/a620b9c39ccde2b631c336410675220d.png)

重载函数

![](/media-MFC学习/923b73514f2d5853c339d8510af45834.png)

BOOL PreCreateWindow(CREATESTRUCT& cs)
--------------------------------------

PreCreateWindow由Framework在窗口创建前被调用，所以当你需要修改窗口的大小、风格、窗口所属的窗口类等cs成员变量时，要改写PrecreateWindow函数。

void OnInitialUpdate()
----------------------

视图窗口完全建立后第一个被框架调用的函数。框架在第一次调用OnDraw前会调用OnInitialUpdate，因此OnInitialUpdate是设置滚动视图的逻辑尺寸和映射模式的最合适的地方.

也就是程序界面上所有内容都会在这里设置。

void AssertValid() const
------------------------

AssertValid函数通过检查其内部状态来对此对象执行有效性检查。
在库的Debug版本中，AssertValid可以断言并因此终止程序，并显示一条消息，列出断言失败的行号和文件名。

CUSBTransmissionView::AssertValid 成员函数提供对对象内部状态的运行时检查。虽然从
CUSBTransmissionView派生类时不须要重写
AssertValid，但能够通过重写使您的类更安全可靠。AssertValid
应在对象的全部成员变量上运行断言，以验证它们包括有效值。比如，它应检查指针成员变量不为
NULL。

void Dump(CDumpContext& dc) const
---------------------------------

Dump函数在程序调试时候使用,主要用于记录调试过程的中间量,及一些相关信息

1.  CUSBTransmissionDoc\* CUSBTransmissionView::GetDocument() const

GetDocument视图对象是用来显示文档对象的内容，函数GetDocument()用于获取当前文档对象的指针m_pDocument.而函数OnDraw()是一个虚函数，负责文档对象的数据在用户视图区的显示输出。在向导生成的成员函数OnDraw()中调用了函数GetDocument().通过获取的文档类指针可以在视图中显示文档内容。

最后那个const，表示不能在函数内修改类的成员变量

HBRUSH OnCtlColor(CDC\* pDC, CWnd\* pWnd, UINT nCtlColor)
---------------------------------------------------------

从OnCtlColor函数名易看出，OnCtlColor函数的作用是**改变（或响应）控件的颜色**，**多用在对话框应用程序（或基于CFormView的应用程序）中**，希望在对话框显示时，里面的控件的颜色可以设置为我们想要的颜色在OnInitDialog()
函数中，我们一般设置好控件的字体在OnPaint()函数中，用来画图，或者是加载一幅位图我们往控件里拖入了很多控件，那么控件的颜色可以在哪里更改呢？就在OnCtlColor函数里（要注意的是，该控件始终要返回一个画刷（句柄），因为有了画刷，才能真正的改变控件的颜色嘛）。

1.  LRESULT CSpreadTransmissionView::OnUpdateUI(WPARAM wparam,LPARAM lparam)

>   OnUpdateUI函数起到更新界面的作用（从控件变量向界面更新和从界面更新到变量），即时将函数1，关联的两者相互更新。

MFC几个常用函数：OnCreate和OnInitialUpDate，GetActiveFrame和MDIGetActive，Invalidate、SetModifiedFlage、UpdateAll

[https://blog.csdn.net/xfortius/article/details/8546394\#](https://blog.csdn.net/xfortius/article/details/8546394)

DoDataExchange
--------------

MSDN是这样描述的：Called by the framework to exchange and validate dialog
data.大意是**框架调用此函数来改写与确认对话框数据**。DoDataExchange的作用是
将界面上的控件与一个变量或者一个类对象相关联

当程序需要交换数据时，不要直接调用DoDataExchange函数，而应该调用CWnd::UpdateData。UpdataData函数内部调用了DoDataExchange。该函数只有一个布尔型参数，它决定了数据传送的方向。调用UpdateData(TRUE)将数据从对话框的控件中传送到对应的数据成员中，调用UpdateData(FALSE)则将数据从数据成员中传送给对应的控件。

需要注意是否是能够双向传输数据的控件才能使用UpdateData函数更新。

定时器
------

在这个窗口对应的类中，增加WM_TIMER消息对应的函数。

![](/media-MFC学习/a620b9c39ccde2b631c336410675220d.png)

添加完毕后，在对应的头文件以及源码中，工具自动添加了类成员函数
OnTimer（），源码中OnTimer（）函数内容需要自己添加。对应的消息ON_WM_TIMER()，已经自动增加到BEGIN_MESSAGE_MAP中。

根据实际情况，决定设置定时器的位置，比如初始化时OnInitialUpdate()。

调用SetTimer(1,2,3);函数指明①一个事件id和②一个时间和③回调函数。当③为NULL时，使用默认的OnTimer();

可以多次调用settimer函数，指明不同定时时间，每个时间约定一个不同的id.

在ontimer函数里根据id判断是什么定时器触发的

# 窗口销毁


一般是OnDestroy函数里调用KillTimer（K、T大写）函数关闭每一个id。几个就调用几次，和前面SetTimer对应起来。防止资源泄露。可以多调用几次KillTimer，不会有影响。

>   OnClose-\>DestroyWindow-\>OnDestroy-\> PostNcDestroy()

>   OnCancle-\>DestroyWindow-\>OnDestroy-\> PostNcDestroy()

## OnOK、OnCancel、OnDestroy的区别:
总结OnOK、OnCancel、EndDialog、OnClose、OnDestroy函数之间的区别:

第一，OnOk()和OnCancel()是CDialog基类的成员函数。  

>   即ID为ID_OK和ID_CANCEL的按钮的通知消息的响应函数，**而OnClose()和OnDestroy()是CWnd基类的成员函数，即WM消息响应函数**。  

第二，OnClose()和OnDestroy()

>   在对话框程序中，根据《深入浅出MFC》所讲，程序退出执行时执行的操作顺序为（从点X按钮开始）

>   * (1) 用户点击X关闭按钮，发送了**WM_CLOSE消息–\>响应OnClose()**
          
>   * (2) 在WM_CLOSE消息的处理函数中，调用**DestroyWindow()**-\>销毁与指定CWnd对象关联的窗口，但没有销毁CWnd对象
          
>   * (3) 在DestroyWindow()中发送了**WM_DESTROY消息–\>窗口销毁后响应OnDestroy();**
          
>   * (4) 在WM_DESTROY消息中调用**PostQuitMessage()**，发送WM_QUITT消息，结束消息循环

>   可以看到，程序的退出过程，是先响应OnClose()，然后响应OnDestroy()。OnDestroy中调用PostQuitMessage()发送WM_QUIT消息结束消息循环。

>   如果没有重定义OnClose函数的话，在对话框程序中执行顺序如下：

>   * (1) 用户点击X关闭按钮，调用的是**CDialog::OnCancel函数**
          
>   * (2) 在**CDialog::OnCancel**的处理函数中，调用**CDialog::EndDialog**关闭对话框；销毁与指定CDialog对象关联的对话框，但**没有销毁CDialog对象**
          
>   * (3) 在CDialog::EndDialog中发**送了WM_DESTROY消息**–\>窗口销毁后响应OnDestroy();
          
>   * (4) 在WM_DESTROY消息中调用PostQuitMessage()，发送WM_QUITT消息，结束消息循环

>   可以看到，程序的退出过程，是先响应OnCancel()，然后响应OnDestroy()。OnDestroy中调用PostQuitMessage()发送WM_QUIT消息结束消息循环。


第三，OnOk(),OnCancel(),OnClose(),OnDestroy()

>   CDialog::OnOk首先调用UpdateData(TRUE)将数据传给对话框成员变量，然后调用CDialog::EndDialog()关闭对话框；CDialog::OnCancel只调用CDialog::EndDialog关闭对话框；

>   OnClose()是WM_CLOSE的响应函数。在一定程度上可以说CDialog::EndDialog()和OnClose()完成类似的工作，但处理的机制不一样，**前者是CDialog的对象机制，后者是WM的消息映射机制**。

>   CDialog::EndDialog()-\>OnDestroy()

>   OnClose()-\>OnDestroy()

>   所以我们在按下cancel按钮的时候，程序不会执行OnClose()，而是调用了直接调用了CDialog::EndDialog(），然后调用了OnDestroy()

第四， DestroyWindow(),OnDestroy(),OnNcDestroy(), PostNcDestroy()

1  virtual void PostNcDestroy( );

参数： 在窗口被销毁以后，缺省的OnNcDestroy成员函数调用这个函数。派生类可以利用这个函数来执行自定义的清除工作，比如删除指针。 

2   afx_msg void OnNcDestroy( );

说明：  
当非客户区即将被销毁时，框架调用这个函数，这是Windows的窗口被销毁时调用的最后一个成员函数。缺省的实现执行一些清除工作，然后调用虚成员函数PostNcDestroy。  
如果你希望执行自己的清除操作，例如删除，则应重载PostNcDestroy。如果你重载了OnNcDestroy，则必须调用基类的OnNcDestroy以确保内部为窗口分配的内存都被释放。

3  virtual BOOL DestroyWindow( );

返回值：如果销毁了窗口，则返回非零值；否则返回0。

4   afx_msg void OnDestroy( );

框架调用这个成员函数以通知CWnd对象它将被销毁。 OnDestroy 是在CWnd对象已经从屏幕上清除以后被调用的。

首先为被销毁的CWnd调用OnDestroy，然后当CWnd的子窗口被销毁时为它们调用OnDestroy。可以假定当OnDestroy运行的时候，所有的子窗口依然存在。

>   综合以上：**先调用DestroyWindow，在此间会有OnDestroy消息，接着窗口被销毁，于是DestroyWindow返回TRUE，然后是OnNcDestroy消息，之后再调用PostNcDestroy。**

>   也不知道这个结论对不对，我的想法是，OnNcDestroy是非客户区销毁时调用，而DestroyWindow销毁的是窗口，非客户区是没有窗口句柄的。


对话框类中添加消息对应的函数
============================

在类视图中找到窗口对应的类，然后再属性中找到消息，如下图红框

![](/media-MFC学习/adfe5c5d659e0a01d6a27ff4a13c12b4.png)

找到对应的消息，可以在右面添加对应函数。

![](/media-MFC学习/995e6e80ca4e95ef7c955be611fd3fc2.png)

# 附


## This指针


this表示当前类的实例的指针，也就是说你 this 在哪个类里，这里 this
就指向哪个类。例如当 this 写在 CTreeView 类的函数里， this 指针就指向 CTreeView
的实例；

CSpreadTransmissionView \*g_pMainView;

![](/media-MFC学习/0ebbe971c78fd16c85a2b95e532f37b4.png)

## 虚函数：


实现多态功能。更新基类，支持不同类型。而不是更新基类实现新功能。

在无需了解具体子类类型的情况下，通过**基类指针**调用子类的实现。

把基类函数声明为虚，才能在运行时，通过调用基类的函数做出多态的分派。如果没有声明为虚，那么调用哪个函数是在编译期静态决定的。

## MESSAGE_MAP


BEGIN_MESSAGE_MAP是[宏定义](https://baike.baidu.com/item/%E5%AE%8F%E5%AE%9A%E4%B9%89/8711494)的一种，在BEGIN_MESSAGE_MAP()和END_MESSAGE_MAP()之间添加你的消息响应函数,为每个**消息处理函数**加入一个入口。


 


