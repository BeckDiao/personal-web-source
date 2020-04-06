---
title: Linux 学习笔记 - 第一部分
date: 2020-03-30 16:13:33
tags: 
 - books
---

鸟叔的Linux私房菜

第一部分 Linux 的规划与安装

[link](http://cn.linux.vbird.org/linux_basic/linux_basic.php#part1)

<!-- more --> 


## 第0章 计算机概论
### 0.1 电脑：辅助人脑的好工具
#### 0.1.1 电脑硬体的五大单元
*所有data都要经过Memory，所以Memory的大小会对整体速度影响很大。*
#### 0.1.2 一切设计的起点： CPU 的架构
CPU 其实内部已经含有一些**instructions set**，我们所使用的software都要经过CPU 内部的instructions set来达成。
指令集的设计主要又被分为两种设计理念：
* 精简指令集(Reduced Instruction Set Computer, RISC)：
每个指令的执行时间都很短，完成的动作也很单纯
常见的RISC 微指令集CPU： Oracle的SPARC 系列（学术+金融领域）， IBM 公司的Power Architecture 和 PowerPC 系列（Sony）、与ARM的ARM CPU 系列（手机、路由器）
* 复杂指令集(Complex Instruction Set Computer, CISC)：
x86架构的CPU(用于Personal Computer)
*word size(32位，64位)：CPU一次资料读取的最多Bit数。CPU读取量 --> memory读取量 ==> 32位CPU --> memory 最大读取量4GB*

### 0.2 个人电脑架构与相关设备元件
#### 0.2.1 执行脑袋运算与判断的CPU
Intel主板架构：<br>
<img src="https://personal-bucket-prod.s3-us-west-2.amazonaws.com/books/linux/Intel%E4%B8%BB%E6%9D%BF%E6%9E%B6%E6%9E%84.png"/>

*SouthBridge(南桥) 被设计用来处理低速信号。NorthBridge(北桥)用来处理高速信号(CPU <--> Main memory)现在已经被整合到CPU内*

CPU:
 * 频率：CPU每秒钟可以进行的工作次数
 * 外频：CPU与外部组件进行数据传输时的速度
 * 32位与64位：word size -> CPU每次能够处理的数据量

Hyper-Threading, HT : CPU内把regiter 分成两部分，像是两个线程。

#### 0.2.2 Memory
#### 0.2.3 显卡 VGA(Video Graphics Array)
显卡内存：每个图像显示的颜色会占用掉内存， 因此显示卡上面会有一个内存的容量
GPU：3D影像需要大量计算，CPU不够用，所以用专门的GPU
#### 0.2.4 disk与储存设备
<img src="https://personal-bucket-prod.s3-us-west-2.amazonaws.com/books/linux/%E7%A3%81%E7%9B%98%E7%BB%93%E6%9E%84.png"/>

转圈的方式读写
组成：圆形磁碟盘、机械手臂、 磁碟读取头与主轴马达

传输接口： SATA接口，USB接口
### 0.3 data表示方式
### 0.4 软件运行
#### 0.4.2 OS
OS其实也是一组程序，这组程序的重点在于管理**电脑的所有活动**以及驱动系统中的所有**硬件**。

OS = system call + Kernel

OS的功能就是让CPU可以开始判断逻辑与运算数值、让main memory可以开始载入/读出data/functions、让disk可以开始被存取、让网卡可以开始传输data、让所有周边可以开始运转等等。 <=== **Kernel**

Kernel functions所放置到记忆体当中的区块是受保护的！并且开机后就一直常驻在memory当中。
主要用于管理硬件，提供合理的电脑系统资源分配。

Kernel是处于硬件和system call 之间的位置。
至少包括以下功能：
System call interface
Process control：安排多个process的执行先后顺序
Memory management(os提供virtual memory)
Filesystem management
Device drivers: OS提供interface给硬件开发商，硬件开发商在开发新产品的时候，依照这个interfact开发相应的drivers给customer，这样customer通过安装drivers就可以在对应的OS上用新开发的硬件了

---------

## 第1章  Linux是什么与如何学习
*POSIX: （Portable Operating System Interface）重点在规范kernel与application之间的interface*
***uname -r*** 查阅kernel版本
*Kernel + Softwares + Tools +可完整安装程序 = Linux distribution*

distributions主要分为两大系统:
* 一种是使用RPM方式安装软体的系统，包括Red Hat, Fedora, SuSE等都是这类
* 一种则是使用Debian的dpkg方式安装软件的系统，包括Debian, Ubuntu, B2D等等


-------

## 第2章  主机规划与disk分割
## 2.1 Linux与硬件的搭配
#### 2.1.3 各硬件装置在Linux中的文件夹名称
***在Linux系统中，每个 装置 都被当成一个 文件夹 来对待， 几乎所有的硬件装置文件夹都在/dev这个目录内***
### 2.2 disk分割
#### 2.2.2 MBR 与partition table
*主要开机记录区(Master Boot Record, MBR)：可以安装开机管理程序的地方，有446 bytes*
*分割表(partition table)：记录整个disk分割的状态，有64 bytes*


#### 2.2.3 开机流程中的BIOS
1. (早期)BIOS 搭配MBR/GPT 的开机流程
*CMOS是记录各项硬件参数且嵌入在主机板上面的储存器。*

  （1） BIOS(Basic Input/Output System)：**开机主动执行的firmware**，会认识第一个可开机的装置；写入到硬件上的一个软件程序，在开机的时候，电脑系统会主动执行的第一个程序。
  （2） MBR：第一个可开机装置的第一个磁区内的主要开机记录区块，内含**开机管理程序**；
  （3） 开机管理程序(boot loader)：一支可读取kernel file来执行的软体；写在MBR(446bytes)上；也可以安装在每个partition 的boot sector上
  （4） Kernel file：开始OS的功能...

loader的主要任务：
（1） 提供选择。
（2） load OS。
（3） 转交给loader：
<img src="https://personal-bucket-prod.s3-us-west-2.amazonaws.com/books/linux/MBR.png"/>


#### 2.2.4 Linux安装模式下，disk分割的选择

如何结合 directory tree 的架构与disk内的资料: mount
*mount: 利用一个目录当成进入点，将disk partition的资料放置在该目录下；也就是说，进入该目录就可以读取该partition*。如：
<img src="https://personal-bucket-prod.s3-us-west-2.amazonaws.com/books/linux/mount%2Bfilesystem.png"/>
目录树与partition之间的相关性


## 第4章 首次登入与线上求助
1. man + command 后出现的右上角的代号：

代号	代表内容
**1	使用者在shell环境中可以操作的指令或可执行档**
2	系统核心可呼叫的函数与工具等
3	一些常用的函数(function)与函式库(library)，大部分为C的函式库(libc)
4	装置档案的说明，通常在/dev下的档案
**5	设定档或者是某些档案的格式**
6	游戏(games)
7	惯例与协定等，例如Linux档案系统、网路协定、ASCII code等等的说明
**8	系统管理员可用的管理指令**
9	跟kernel有关的文件

2. 
*man -f XXX*： 所有名为XXX的说明文件，指令必须是XXX
e.g.
man -f man 得到很多man(1), man(7)等
然后用man 7 man 就可以得到代号为7的man的说明文件
<br>
*man -k XXX*： 只要description中有man这个关键字就将该说明列出来。
<br>
3. 
*info + command*

#### 查询方法Summary:
1. 知道command 忘了参数， --help
2. 查command 的说明， man, info
3. software文档，/usr/share/doc

#### 4.5 正确的关机方法
正常情况下，要关机时需要注意底下几件事：
* 观察系统的使用状态：
*who* :目前有谁在线
*ps -aux*: 背景执行的程序
*netstat -a*: 查看网络情况

* 正确的关机指令使用：
将data同步写入disk中的指令： sync ==> memory --> disk
惯用关机：shutdown
重新开机，reboot
关机：poweroff

