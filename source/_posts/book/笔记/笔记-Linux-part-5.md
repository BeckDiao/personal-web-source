---
title: Linux 学习笔记 - 第五部分
date: 2020-04-11 16:13:33
tags: 
 - books
---

鸟叔的Linux私房菜 之 第五部分 Linux 系统管理员

最后一部分涵盖的内容很多，但是对我而言，我觉得用处不大。尤其在现在的公司，Linux本身配置、setup、deploy什么的已经不需要每个人弄了，大家用的都是现场弄好的，不需要也不被允许去随意更改，所以也就不需要去研究这些了。所以这部分只是记录一些点和常见的名词。没有仔细看，等以后用到了再回来看吧。

[link](http://cn.linux.vbird.org/linux_basic/linux_basic.php#part5)

<!-- more --> 


## 第18章 Daemon

### daemon
* daemon 分类：
  * stand_alone 
  * super daemon 管理的daemon，e.g. xinetd
    * triggered by request
    * 优点：
      * super daemon具有security control and management机制(可以理解为firewall)，所以：
        * 对client开放较多权限的service(e.g. telnet)
        * 本身不具有管理机制或firewall的service
      * service 可以在request结束后就关闭，不占用资源
    * 缺点：latency(load to memory)
    * firewall也可以配置
    
## 第22章 source code和tarball

### Make
* make & configure
  * make: program, to find makefile (record how to compile source code)
  * configure侦测程序(software provider提供，侦测user的environment，如是否有软件必需的功能等) -> create makefile


### Tarball(POSIX标准)
Tarball 文件: 将软件的所有source code先以 tar 打(然后再以压缩技术来压缩，通常最常见的就是以 gzip 来压缩)

* .tar是未压缩的
* .tar.gz(=.tgz)等是经过gzip压缩过的

tarball里面一般包括：
* source code
* configure/config file(侦测程序文件)
* README

### library
#### static library
* `*.a`
* integrate on compile time
* 最大优点：compiled executable 可以独立运行，不再有external call
* library升级了，executable必需重新compile

#### dynamic library(prefered)
* `*.so`
* used on runtime
* compiled executable不能独立运行，`*.so`的path甚至都不能变
* library升级 executable不需要重新compile
* always under `usr/lib`
* cache dynamic library: `ldconfig`
* 判断binary file中含有哪些`*.so`: `ldd`

### checksum
一般用MD5和SHA1



