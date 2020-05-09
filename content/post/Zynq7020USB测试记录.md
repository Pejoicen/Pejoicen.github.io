---
title: "USB测试记录"
date: 2018-09-28T20:28:10+08:00
description: ""
draft: false
tags: ["ZYNQ","嵌入式","USB"]
categories: ["软件"]
---


使用petalinux测试USB功能。在MicroZus板卡教程基础上修改测试。

<!--more-->





原理图
======

原理图设计如下：

![](/media-Zynq7020USB测试记录/f8ea09c765ea5479524f6b8c5febec68.png)

![](/media-Zynq7020USB测试记录/554c6a1647d033b62c0ceda8475f632f.png)

![](/media-Zynq7020USB测试记录/1ad9032e1a2e68537517391d7515baa3.png)

和MicroZus板卡的区别在于USB芯片型号，以及USB芯片RESET_N引脚连接到的是PL侧引脚，MicroZus板卡复位使用的是MIO46引脚。

硬件修改：
==========

硬件上需要将R516焊上。二极管D18、D19去掉。D21、D16二极管焊反，需要修改过来

问题整理：
==========

Vbus电压没有
------------

发现**硬件上R516没有焊接**，补焊后万用表能够测到Vbus的5V电压；

TUSB1210芯片的RESET_N引脚问题
-----------------------------

MicroZus开发板usb部分使用的复位时mio46，但是公司的板卡参考zedboard设计，复位使用的是PL侧引脚。PS侧使能EMIO
GPIO，同时在PL侧增加iobuf控制USB 复位引脚。

![](/media-Zynq7020USB测试记录/483d7c825f0734d7315bb72eaf0898bc.png)

![](/media-Zynq7020USB测试记录/e7af52321325114839961d6b8bf0e525.png)

修改设备树尤其是USB 复位部分，其余和MicroZus板卡一致。

![](/media-Zynq7020USB测试记录/3b9ddd7d42a103ebd0a5826b68da800c.png)

![](/media-Zynq7020USB测试记录/cae6f255f9184734894fb49ed7457338.png)

echo -n 960 \> /sys/class/gpio/export

echo out \> /sys/class/gpio/gpio960/direction

echo 0 \> /sys/class/gpio/gpio960/value

使用上述命令可以控制GPIO（EMIO）的状态，使用示波器测试发现确实能够改变USB
reset电平。因此可以确定gpio960960对应的为GPIO54（USB
reset），因此可以确定PL侧工程没有问题，GPIO的ID号也没有问题。

TPS2051B芯片OC引脚电平为低的问题
--------------------------------

测试发现vbus电压有时上电存在有时上电不存在，测试发现TPS2051B芯片的OC引脚为低电平。

![](/media-Zynq7020USB测试记录/b69244184662ccd4ce2407939564e773.png)

![](/media-Zynq7020USB测试记录/b378b374ccbb11515019adbde1d00d61.png)

检查发现D17异常，焊下后，vbus为5V。但是OC依旧为0.17V左右，发现OTG-VBUSOC连接到PL侧，将引脚改为输入后，发现OC输出电压变为正常，为3.3V。

Petalinux打印“usb 1-1: device descriptor read/64, error -71”问题
----------------------------------------------------------------

![](/media-Zynq7020USB测试记录/40c8176b1f789f8a40b1807a8c4cc40a.png)

![](/media-Zynq7020USB测试记录/5d37b90acbe1cf81c90d872bc7beee71.png)

把卡插U盘的时候显示：

usb 1-1: new **high-speed USB device** number 2 using ci_hdrc

**usb 1-1: device descriptor read/64, error -71**

usb 1-1: device descriptor read/64, error -71

usb 1-1: new high-speed USB device number 3 using ci_hdrc

usb 1-1: device descriptor read/64, error -71

usb 1-1: device descriptor read/64, error -71

usb 1-1: new high-speed USB device number 4 using ci_hdrc

usb 1-1: device not accepting address 4, error -71

usb 1-1: new high-speed USB device number 5 using ci_hdrc

usb 1-1: device not accepting address 5, error -71

usb usb1-port1: unable to enumerate USB device

插Lenovo牌USB的鼠标，能够正常识别：

usb 1-1: new **low-speed USB device** number 6 using ci_hdrc

input: PixArt Lenovo USB Optical Mouse as
/devices/soc0/amba/e0002000.usb/ci_hdrc.0/usb1/1-1/1-1:1.0/0003:17EF:6019.0001/input/input0

hid-generic 0003:17EF:6019.0001: input: USB HID v1.11 Mouse [PixArt Lenovo USB
Optical Mouse] on usb-ci_hdrc.0-1/input0

### 可能原因1：

<https://blog.csdn.net/tiny_pi/article/details/20529219>：

![](/media-Zynq7020USB测试记录/90346bebe69549ba43ccac829085360e.png)

### 可能原因2：

<https://community.nxp.com/thread/294381>

![](/media-Zynq7020USB测试记录/aa405694f816e5305e6edbfea7ad83b9.png)

VDES项目：基带板MircoUSB的PHY芯片与FPGA之间连线的走线长度如截图所示，没有做等长设计，图中右侧为走线总长度，单位为mm

![PHY芯片与FPGA之间连线的走线.jpg](/media-Zynq7020USB测试记录/f4542cde8941c0a262059647e65b9e9d.jpg)

下图是Z7020开发板PCB中的约束，对于USB与FPGA的连线做的是正负300mil的等长，300mil约等于7.6mm

![Z7020开发板PCB中的约束](/media-Zynq7020USB测试记录/8dff107706845e8e2bdc7b826ee4dd31.jpg)

### 原因三

另外是否可能是稳压的二极管的问题，因为USB鼠标是低速设备，U盘是高速设备。**将稳压、防静电的二极管D18、D19去掉后，再测试，发现能够正常识别USB设备，并且挂载到系统中能在上面创建文件。**

Petalinux打印“ci_hdrc ci_hdrc.0: unable to init phy: -110”
----------------------------------------------------------

板卡上电的时候有概率出现上述问题，也有可能正常工作。

异常情况：

e0002000.usb supply vbus not found, using dummy regulator

**ci_hdrc ci_hdrc.0: unable to init phy: -110**

**ci_hdrc: probe of ci_hdrc.0 failed with error -110**

mousedev: PS/2 mouse device common for all mice

正常情况：

ehci_hcd: USB 2.0 'Enhanced' Host Controller (EHCI) Driver

ehci-pci: EHCI PCI platform driver

usbcore: registered new interface driver usb-storage

e0002000.usb supply vbus not found, using dummy regulator

**ULPI transceiver vendor/product ID 0x0451/0x1507**

**Found TI TUSB1210 ULPI transceiver.**

ULPI integrity check: passed.

ci_hdrc ci_hdrc.0: EHCI Host Controller

ci_hdrc ci_hdrc.0: new USB bus registered, assigned bus number 1

ci_hdrc ci_hdrc.0: USB 2.0 started, EHCI 1.00

hub 1-0:1.0: USB hub found

hub 1-0:1.0: 1 port detected

mousedev: PS/2 mouse device common for all mice

一旦正常初始化，在petalinux下往U盘中拷贝大文件，并不会出错。因此基本可以确定，PCB走线不会有问题，否则拷贝文件会出错。**说明还是初始化过程的问题。**

### 查看代码

从xilinx官网下载了petalinux源码，找到了打印信息的位置如下：

![](/media-Zynq7020USB测试记录/30d6d8f1a7ef2d438a6595e09db8545c.png)

/\*\*

\* ci_usb_phy_init: initialize phy according to different phy type

\* \@ci: the controller

\*

\* This function returns an error code if usb_phy_init has failed

\*/

static int ci_usb_phy_init(struct ci_hdrc \*ci)

{

int ret;

switch (ci-\>platdata-\>phy_mode) {

case USBPHY_INTERFACE_MODE_UTMI:

case USBPHY_INTERFACE_MODE_UTMIW:

case USBPHY_INTERFACE_MODE_HSIC:

ret = \_ci_usb_phy_init(ci);

if (!ret)

hw_wait_phy_stable();

else

return ret;

hw_phymode_configure(ci);

break;

**case USBPHY_INTERFACE_MODE_ULPI:**

case USBPHY_INTERFACE_MODE_SERIAL:

hw_phymode_configure(ci);

ret = \_ci_usb_phy_init(ci);

if (ret)

return ret;

break;

default:

ret = \_ci_usb_phy_init(ci);

if (!ret)

hw_wait_phy_stable();

}

return ret;

}

并未找到什么有用的信息，看不懂。

### 噪声引起USB phy芯片异常

<https://github.com/parallella/parallella-issues/issues/3>

![](/media-Zynq7020USB测试记录/91f9a23171e425f6770c20aedb7a418d.png)

噪声引起USB phy芯片异常。

**找了USB转接线，发现对USB phy初始化错误现象没有影响。**

### 怀疑是复位问题

由于开发板使用的是MIO46来控制USB phy复位，而手里的板卡是PL侧控制USB
phy复位。因此**首先想到的是通过EMIO GPIO来控制USB
phy复位**，在前面问题2中已经整理了GPIO的对应关系。*但是修改设备树部分，自己并不敢保证修改正确。*

自己手动修改了pcw.dtsi，该文件其实是petalinux自动产生的。**直接修改不确定是否有影响。**

![](/media-Zynq7020USB测试记录/bb416c23309436bed8ae2b8790658e64.png)

![](/media-Zynq7020USB测试记录/4e4306a1e8c884d0e378c1717ac8ee5b.png)

参考网上的资料：<https://www.cnblogs.com/qiantuo1234/p/6665361.html>

还是怀疑复位的问题，使用示波器测试USB
phy的复位信号，发现上电后由低变为高，**并未出现电平变化**。

然后使用示波器测试MicroZus板卡，发现上电后由低变为高，**并未出现电平变化**。

查看TUSB1210手册

![](/media-Zynq7020USB测试记录/4521117b9cb8c2ff8d37b04891b24c33.png)

使用示波器观察，发现MicroZus板卡的R63电阻两端电平一直为高没有变化，所以复位问题应该不大。Xilinx官方也没有强调必须要有复位。将复位引脚去掉（fpga工程中和设备树中）后，发现USB有正常也有不正常的情况。因此得出结论，USB
phy的复位不是必须的，只需要保证USB芯片不被额外复位就可以。

将MicroZus板卡上的复位引脚去掉，设备树中也将复位相关信息删除。**发现MicroZus板卡也出现了USB
phy初始化错误的问题。也就是说，MIO46引脚电平并没有变化，但是有无USB
PHY复位引脚的设置，对USB
phy芯片初始化有影响。**因此判断，复位部分即使上电后引脚电平没有变化，但是应该是影响到了软件里面的部分内容。或者是在上电的最初始阶段有一个复位操作，无法使用示波器观察。

解决方法：
==========

由于板卡硬件上USB复位是接在PL侧，但是管脚的电平并没有变化。因此在PS侧选择MIO7作为USB
phy复位引脚，并且在设备树中也修改相应的设置。在PL侧人为增加一个大于200ns的复位，随后USB的复位信号保持高电平。修改如下：

![](/media-Zynq7020USB测试记录/ac93ee0cf388df296823d25273cb2438.png)

![](/media-Zynq7020USB测试记录/5e1762add2f058830948d8f56921a9d0.png)

![](/media-Zynq7020USB测试记录/75cf37172607f7416a83336c5b1fb291.png)

PL侧使用FCLK_CLK0和FCLK_RESET0，对USB phy芯片复位。

修改后未插入U盘测试30次，发现phy芯片初始化出错问题不在出现。插上U盘测试20次，均能正常识别USB设备。

设备树
======

![](/media-Zynq7020USB测试记录/0bf00f36c66a93b8dbcd88fbfab1e2ed.png)

./subsystems/linux/configs/device-tree

PetaLinux device tree configuration is associated with following config files
that are located at
**\<plnx-proj-root\>/subsystems/linux/configs/device-tree**/.

• pcw.dtsi

• pl.dtsi

• skeleton.dtsi

• system-conf.dtsi

• system-top.dts

• zynq-7000.dtsi, for Zynq

• zynqmp-clk.dtsi, for Zynq Ultrascale+ MPSoC

• zynqmp.dtsi, for Zynq Ultrascale+ MPSoC

pcw.dtsi应该是根据导入的hdf文件生成的，因为修改USB
RESET后，发现在USB部分有改动，并且每次导入硬件描述文件petalinux-config
--get-hw-description=. 后发现该文件会更新。

修改记录
========

修改1
-----

在原始工程基础上的修改：

Zynq的MIO7连接LD_MIO/VCFG0信号，也通过接入带看USB的复位（目前电阻NC）。

![](/media-Zynq7020USB测试记录/ac93ee0cf388df296823d25273cb2438.png)

因为目前板卡USB复位连接的还是PL侧引脚，因此修改了petalinux的设备树

![](/media-Zynq7020USB测试记录/bb416c23309436bed8ae2b8790658e64.png)

修改2
-----

修改硬件将R521焊接，R520NC，修改设备树，将gpio0 54改为gpio0 7

修改3
-----

将USB reset引脚接在EMIO
GPIO0上，petalinux中修改pcw.dtsi和system-top.dts，将USB复位设置为GPIO54.

修改4
-----

e0002000.usb supply vbus not found

搜索上述问题，查找资料发现，官方给的device tree和MicroZus给的不一致。

usb_0: usb\@e0002000 {

compatible = "xlnx,zynq-usb-2.20.a", "chipidea,usb2";

clocks = \<&clkc 28\>

dr_mode = "otg";

interrupt-parent = \<\&intc\>;

interrupts = \<0 21 4\>;

reg = \<0xe0002000 0x1000\>;

usb-phy = \<\&usb_phy0\>;

};

usb_phy0: phy0 {

compatible = "ulpi-phy";

\#phy-cells = \<0\>;

reg = \<0xe0002000 0x1000\>;

view-port = \<0x170\>;

drv-vbus;

}

修改5
-----

重新焊接了新USB phy芯片，修改后发现USB reset引脚的电平恢复正常均为1.8V左右。

问题测试记录
============

在未修改时，测试发现VBUS电压输出为0，CPEN引脚电压也为0；D21一端为0.2\~0.4V，另一端为1.8V

修改1
-----

按照修改1修改后，测试发现VBUS电压为5V，但是D21两侧分别为1.8V和0.18V，同时插上USB设备后显示以下错误信息：

usb 1-1: new high-speed USB device number 2 using ci_hdrc

usb 1-1: device descriptor read/64, error -71

usb 1-1: device descriptor read/64, error -71

usb 1-1: new high-speed USB device number 3 using ci_hdrc

usb 1-1: device descriptor read/64, error -71

usb 1-1: device descriptor read/64, error -71

usb 1-1: new high-speed USB device number 4 using ci_hdrc

usb 1-1: device not accepting address 4, error -71

usb 1-1: new high-speed USB device number 5 using ci_hdrc

usb 1-1: device not accepting address 5, error -71

usb usb1-port1: unable to enumerate USB device

初步分析还是复位的问题。

修改2
-----

测试发现已经打印上述错误信息。使用万用表测试，发现MIO7输出为高电平3.3V。

对比测试MicroZus板卡，发现无论有USB设备还是没有USB设备R63两端均为1.8V（一端为1.8V上拉，一端为USB芯片的复位引脚）。

petalinux-config --get-hw-description=.

该命令执行后会根据hdf文件，生成pcw.dtsi

![](/media-Zynq7020USB测试记录/41571e544ab5e7a66f5044cc624063bc.png)

从中可以看到没有USB reset信息。

usbcore: registered new interface driver usb-storage

e0002000.usb supply vbus not found, using dummy regulator

ci_hdrc ci_hdrc.0: unable to init phy: -110

ci_hdrc: probe of ci_hdrc.0 failed with error -110

修改3
-----

设置pcw.dtsi中USB复位为gpio0 54 0；设置system-top.dts

ehci_hcd: USB 2.0 'Enhanced' Host Controller (EHCI) Driver

ehci-pci: EHCI PCI platform driver

usbcore: registered new interface driver usb-storage

e0002000.usb supply vbus not found, using dummy regulator

ULPI transceiver vendor/product ID 0x0451/0x1507

Found TI TUSB1210 ULPI transceiver.

![](/media-Zynq7020USB测试记录/cae6f255f9184734894fb49ed7457338.png)

echo -n 960 \> /sys/class/gpio/export

echo out \> /sys/class/gpio/gpio960/direction

echo 0 \> /sys/class/gpio/gpio960/value

使用上述命令可以控制GPIO（EMIO）的状态，使用示波器测试发现确实能够改变USB
reset电平。因此可以确定gpio960960对应的为GPIO54（USB
reset），因此可以确定PL侧工程没有问题，GPIO号也没有问题。

**检查原理图发现D21和D16焊接方向是反的**，修改后petalinux打印的信息如下：

ehci_hcd: USB 2.0 'Enhanced' Host Controller (EHCI) Driver

ehci-pci: EHCI PCI platform driver

usbcore: registered new interface driver usb-storage

e0002000.usb supply vbus not found, using dummy regulator

ULPI transceiver vendor/product ID 0x0451/0x1507

Found TI TUSB1210 ULPI transceiver.

ULPI integrity check: passed.

ci_hdrc ci_hdrc.0: EHCI Host Controller

ci_hdrc ci_hdrc.0: new USB bus registered, assigned bus number 1

ci_hdrc ci_hdrc.0: USB 2.0 started, EHCI 1.00

hub 1-0:1.0: USB hub found

hub 1-0:1.0: 1 port detected

修改4
-----

**ehci_hcd: USB 2.0 'Enhanced' Host Controller (EHCI) Driver**

**ehci-pci: EHCI PCI platform driver**

**usbcore: registered new interface driver usb-storage**

**e0002000.usb supply vbus not found, using dummy regulator**

**ULPI transceiver vendor/product ID 0x0451/0x1507**

**Found TI TUSB1210 ULPI transceiver.**

**ULPI integrity check: passed.**

mousedev: PS/2 mouse device common for all mice

i2c /dev entries driver

Xilinx Zynq CpuIdle Driver started

Driver 'mmcblk' needs updating - please use bus_type methods

sdhci: Secure Digital Host Controller Interface driver

sdhci: Copyright(c) Pierre Ossman

sdhci-pltfm: SDHCI platform and OF driver helper

sdhci-arasan e0100000.sdhci: No vmmc regulator found

sdhci-arasan e0100000.sdhci: No vqmmc regulator found

mmc0: SDHCI controller on e0100000.sdhci [e0100000.sdhci] using ADMA

ledtrig-cpu: registered to indicate activity on CPUs

**usbcore: registered new interface driver usbhid**

**usbhid: USB HID core driver**

检查发现D21两侧电压不正常，“+”标志侧为0.5V左右，另一侧电压为0。怀疑是USB芯片有问题。

修改5
-----

重新焊接USB
phy芯片后，USB复位引脚的电平恢复正常，二极管D21两端电压分别为1.8V和1.6V左右。

![](/media-Zynq7020USB测试记录/40c8176b1f789f8a40b1807a8c4cc40a.png)

![](/media-Zynq7020USB测试记录/5d37b90acbe1cf81c90d872bc7beee71.png)

把卡插U盘的时候显示：

usb 1-1: new **high-speed USB device** number 2 using ci_hdrc

usb 1-1: device descriptor read/64, error -71

usb 1-1: device descriptor read/64, error -71

usb 1-1: new high-speed USB device number 3 using ci_hdrc

usb 1-1: device descriptor read/64, error -71

usb 1-1: device descriptor read/64, error -71

usb 1-1: new high-speed USB device number 4 using ci_hdrc

usb 1-1: device not accepting address 4, error -71

usb 1-1: new high-speed USB device number 5 using ci_hdrc

usb 1-1: device not accepting address 5, error -71

usb usb1-port1: unable to enumerate USB device

插Lenovo牌USB的鼠标，能够正常识别：

usb 1-1: new **low-speed USB device** number 6 using ci_hdrc

input: PixArt Lenovo USB Optical Mouse as
/devices/soc0/amba/e0002000.usb/ci_hdrc.0/usb1/1-1/1-1:1.0/0003:17EF:6019.0001/input/input0

hid-generic 0003:17EF:6019.0001: input: USB HID v1.11 Mouse [PixArt Lenovo USB
Optical Mouse] on usb-ci_hdrc.0-1/input0

![](/media-Zynq7020USB测试记录/b786e473f8017d6b815dc58aa6f9d957.png)

上图为vdes板卡（左）和MicroZus板卡（右）打印信息比较，除了USB
phy芯片型号不一致外都相同。文件如下：

可能原因1：

<https://blog.csdn.net/tiny_pi/article/details/20529219>：

![](/media-Zynq7020USB测试记录/90346bebe69549ba43ccac829085360e.png)

可能原因2：

<https://community.nxp.com/thread/294381>

![](/media-Zynq7020USB测试记录/aa405694f816e5305e6edbfea7ad83b9.png)

VDES项目：基带板MircoUSB的PHY芯片与FPGA之间连线的走线长度如截图所示，没有做等长设计，图中右侧为走线总长度，单位为mm

![C:\\Users\\Pejoicen\\AppData\\Roaming\\Foxmail7\\Temp-11032-20180925085922\\Attach\\Catch(09-25-16-47-56).jpg](/media-Zynq7020USB测试记录/f4542cde8941c0a262059647e65b9e9d.jpg)

下图是Z7020开发板PCB中的约束，对于USB与FPGA的连线做的是正负300mil的等长，300mil约等于7.6mm

![C:\\Users\\Pejoicen\\AppData\\Roaming\\Foxmail7\\Temp-11032-20180925085922\\Attach\\Catch5687(09-25-16-47-56).jpg](/media-Zynq7020USB测试记录/8dff107706845e8e2bdc7b826ee4dd31.jpg)

另外是否可能是稳压的二极管的问题。

可能原因3：

<https://blog.csdn.net/kenny_wju/article/details/9091395>

软件配置的问题。

使用另一块板卡测试

发现，vbus电压并不是时刻存在，有时候会出现电压为0的情况。并且D21连接PL侧的电压为1.65V左右，而该bank供电电压为2.5V。

测试十次，观察Vbus几次有电压几次没电压

第一次没有电压，第二次，第三次。发现U47的OC引脚为低，说明电流过载。检查是否有短路的地方，随后准备去掉D17。去掉D17后，vbus正常。但是依旧显示原来的那个错误：“usb
1-1: device descriptor read/64, error -71”

继续把D18、D19、D20二极管去掉。

![](/media-Zynq7020USB测试记录/b959c3a1293cf41638c851a89b451b53.png)

可以看到已经能够正常识别到USB；

![](/media-Zynq7020USB测试记录/fae05124f0d3cc5c99d711e2c7b88c8d.png)

用vi 在U盘中创建txt文件，名称为666.txt

![](/media-Zynq7020USB测试记录/9a4a058391677ce6efa574bda3b6d0ed.png)

修改内容，保存，解挂。将U盘插在电脑上

![](/media-Zynq7020USB测试记录/903fe77cd8e2cf42d90158b3e725021a.png)

![](/media-Zynq7020USB测试记录/eec450fc82424454499ffdda44189d7a.png)

问题6
-----

无论有无U盘在板卡上，每次上电都有概率出现异常，基本在10次以内必出现。有时候很频繁。

![](/media-Zynq7020USB测试记录/069da73fecd6a3692ba6eedaab8f9070.png)

异常情况：

e0002000.usb supply vbus not found, using dummy regulator

**ci_hdrc ci_hdrc.0: unable to init phy: -110**

**ci_hdrc: probe of ci_hdrc.0 failed with error -110**

mousedev: PS/2 mouse device common for all mice

正常情况：

ehci_hcd: USB 2.0 'Enhanced' Host Controller (EHCI) Driver

ehci-pci: EHCI PCI platform driver

usbcore: registered new interface driver usb-storage

e0002000.usb supply vbus not found, using dummy regulator

ULPI transceiver vendor/product ID 0x0451/0x1507

Found TI TUSB1210 ULPI transceiver.

ULPI integrity check: passed.

ci_hdrc ci_hdrc.0: EHCI Host Controller

ci_hdrc ci_hdrc.0: new USB bus registered, assigned bus number 1

ci_hdrc ci_hdrc.0: USB 2.0 started, EHCI 1.00

hub 1-0:1.0: USB hub found

hub 1-0:1.0: 1 port detected

mousedev: PS/2 mouse device common for all mice

<https://www.cnblogs.com/qiantuo1234/p/6665361.html>

![](/media-Zynq7020USB测试记录/4521117b9cb8c2ff8d37b04891b24c33.png)

使用示波器观察，发现MicroZus板卡的R63电阻两端电平一直为高没有变化，所以复位问题应该不大。

Xilinx官方也没有强调必须要有复位。

将复位引脚去掉（fpga工程中和设备树中）后，发现USB有正常也有不正常的情况。因此得出结论，USB
phy的复位不是必须的，只需要保证USB芯片不被额外复位就可以。

测试7
-----

在USB工作正常的情况下，使用petalinux将U盘中的一个400+MB的文件复制到同一目录下，看是否能够正常复制，以及复制的文件内容是否正确。

查看代码
--------

从xilinx官网下载了petalinux源码，找到了打印信息的位置如下：

![](/media-Zynq7020USB测试记录/30d6d8f1a7ef2d438a6595e09db8545c.png)

/\*\*

\* ci_usb_phy_init: initialize phy according to different phy type

\* \@ci: the controller

\*

\* This function returns an error code if usb_phy_init has failed

\*/

static int ci_usb_phy_init(struct ci_hdrc \*ci)

{

int ret;

switch (ci-\>platdata-\>phy_mode) {

case USBPHY_INTERFACE_MODE_UTMI:

case USBPHY_INTERFACE_MODE_UTMIW:

case USBPHY_INTERFACE_MODE_HSIC:

ret = \_ci_usb_phy_init(ci);

if (!ret)

hw_wait_phy_stable();

else

return ret;

hw_phymode_configure(ci);

break;

**case USBPHY_INTERFACE_MODE_ULPI:**

case USBPHY_INTERFACE_MODE_SERIAL:

hw_phymode_configure(ci);

ret = \_ci_usb_phy_init(ci);

if (ret)

return ret;

break;

default:

ret = \_ci_usb_phy_init(ci);

if (!ret)

hw_wait_phy_stable();

}

return ret;

}

问题9
-----

<https://github.com/parallella/parallella-issues/issues/3>

![](/media-Zynq7020USB测试记录/91f9a23171e425f6770c20aedb7a418d.png)

噪声引起USB phy芯片异常。
