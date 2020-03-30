---
title: CSAPP 读书笔记
date: 2020-03-29 16:13:33
tags: 
 - books
---

CSAPP 读书笔记。 still in progress.

<!-- more -->

## 第一章

---
### 主线：

> 本书的目的是帮助你了解当你在系统上执行hello 程序时，系统发生了什么以及为什么会这样。

```
hello.c -(translate)-> a sequence of low-level machine-lanague instructions -(package)-> executable object file -(stored)-> binary disk file
```
=> `gcc -o hello hello.c`

----
### 名词：
* cpp -> c preprocessor
* GCC -> The GNU Compiler Collection (GCC) is a compiler system produced by the GNU Project supporting various programming languages.
* shell -> command-line interpreter
* 总线 = buses
* kernel -> os代码常驻main memory的部分。

---
### 细节：

#### 1.1 information = bits + context
* bit 0,1 => 8 bit = 1 byte => byte represents text character
* 所有东西都是bit，但是通过context，我们可以知道/区分 这串bit代表什么

#### 1.2 程序被其他程序翻译成不同格式
(以下有照片)
hello.c (source code, text)
cpp -> hello.i (replaced #, text)
cc1 -> hello.s (assembly, text)
as -> hello.o (relocatable object program, binary)
ld -> hello(execiutable object program, binary)
posix -> (用来消除各unix版本中的差异)一种涵盖了很多方面的unix系统的标准，比如unix系统调用的c语言接口，shell, tools, threads,网络编程等

#### 1.3 It pays to understand how compilation system work
* 优化程序性能
* 理解lind时出现的错误。 如 can't resolve import.
* 避免安全漏洞。如 缓冲区溢出错误(buffer overflow)。

#### 1.4 系统硬件组成
* Buses: carry information
4 bytes(32 bits)/8 byte(64 bits) => fixed chunk size -> word size => fundamental system parameter
* I/O devices
* Main Memory
temporary。由一系列DRAM组成。每个memory： index从0开始的线性字节数组
* processor
CPU: engine that interpres or executes instructions(stored in memory).
cpu核心是word-size storage device(or register) -> PC(program counter) -> point to an instruction


#### 1.7 OS manages hardware

> 可以把os看成是application 和hardware之间的一层software

os基本功能<- 通过process, vitual memory, files这些抽象概念来实现
1. 防止hardware被失控的app滥用
2. 向app提供简单一致的机制来控制复杂而又通常大不相同的low-level硬件设备

file: i/o的抽象表示
virtual memory: main memory & disk 的抽象
process: 对processor, main main, i/o的抽象


##### 1.7.1 process
* 并发是通过processor在process间切换来实现的。os 实现这种交错执行的机制叫做context switching。
* os 保持track process运行的所需的所有状态信息，就是context, e.g. pc, register file当前值, main memory内容等。
* 一个process到另一个的转换是由kernel管理的
* kernal不是一个独立的process，而是系统管理所有process所用的**代码和数据结构**的集合。

##### 1.7.2 threads
一个process可以由多个threads(执行单位)组成，每个thread都运行在process都上下文中，并共享同样的代码和global data。

##### 1.7.3 virtual memory
* 抽象概念 -> 好像每个process都在独占main memory (i.e. 每个process看到的memory都是一致的 -> virtual address space) <- 基本思想是把一个process的virtual memory的内容 存放在disk上，然后用main memory作为disk的cache。
* 每个process看到的virtual address space 由大量准确定义的区构成，每个区有自己的功能：
 * code, data. 固定大小。
 * heap. 动态大小。
 * shared libraries.
 * stack. compiler 用stack实现函数调用。动态大小。
 * kernel virtual memory. 为kernel保留。

##### 1.7.4 file
* uniform view to all applications
* 系统中的所有io都是通过一小组称为unix i/o的系统函数调用读写file来完成的。

#### 1.8 network
* network 可以视为一个i/o device

#### 1.9 重要主题
##### 1.9.1 Amdahl定律
当我们对系统中某个部分进行加速时，其对整体系统性能的影响取决于该部分的重要性和加速程度。

##### 1.9.2 concurrency 和 parallelism
concurrency -> 通用概念，一个具有多个活动的系统
parallelism -> 用concurrency来使一个系统运行的更快

* thread-level concurrency
* instruction-level parallelism
* single-instruction, multi-data(SIMD) parallelism

