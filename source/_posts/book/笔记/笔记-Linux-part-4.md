---
title: Linux 学习笔记 - 第四部分
date: 2020-04-10 16:13:33
tags: 
 - books
---

鸟叔的Linux私房菜 之 第四部分 Linux 使用者管理

根据我过去的工作经验，第15， 16章需要用到的非常少，可以说几乎没有，甚至很多概念我从来没听说过，所以我看的也非常不认真，把大概的点写在这儿，等以后有需要的时候再回来仔细看吧。

第16， 17章(除了SELinux)非常有用。

[link](http://cn.linux.vbird.org/linux_basic/linux_basic.php#part4)

<!-- more --> 

## 第14章 ACL

### 一些points，没有仔细了解，等有需要的时候再看：
* ACL - Access Control List 
* su - 切换到root身份，需要root password
* sudo - (有root权限的user)以root身份run command,需要user password
* PAM  - Pluggable Authentication Modules -> AuthN API


## 第15章 Quota与 进阶FileSystem管理
### quota limitation：
1. 实际运行中是针对整个file system进行限制的
1. root不能配置quota
所以，不能针对dir来进行quota设计，只能针对file system来配置。

### quota的配置
* block(使用的disk容量), inode(可以创建的file数量)
* soft/hard
* grace time

### LVM(logical volume manager)
#### 作法：
将几个实体的 partitions (或 disk) 通过software组合成为一块看起来是独立的大disk(VG， volume group) ，然后将这块大disk再经过分割成为可使用分割槽 (LV, logical volume)， 最终就能够mount使用了


## 第16章 crontab
### run once: `at`
* 可以限制`at`的使用权限
* `bacth`: run once when cpu is not busy (本质也是`at`)

### periodically run: `crontab`
* 可以限制`cron`的使用权限
* 可以规定具体第几分钟才避免所有command一起执行(比如所有的程序都是每5分钟运行一次)

### anacron
对于不是24小时开机的host，`anacron`可以监测关机期间应该进行但是没有进行的crontab任务，任务运行一遍后就会自动停止。


## 第17章 process管理 与 SELinux
### Process
* 触发任何一个事件时，system都会定义为一个process，并且给这个proces一个ID，PID，同时根据run这个program的user和相关的属性关系，给这个PID一组有效的permission。
* program vs process
  * program - a binary prgram -> a file saved in disk
  * process - program被run后，user的permission和属性、program和所需的数据都被加载到memory中，is给这个memory内的unit一个PID。**所以，可以说process是一个运行中的program.**
* fork and exec: 每次在run一个command的时候，其实都是开始一个新的process，这里会把bash fork一下(作为新process的PPID)，然后运行，command运行结束后，这个process就自动被kill了。
* daemon: 启动后在background中一直持续不断的运行，常驻memory中

### 多人多工
* Linux可以支持多人多工的一个原因就是**每个人可以有自己的environment**
* 多人的environment就是每个人登录后shell的不同的PID
* Linux 几乎可以说绝对不会死机的

> 可以在任何时候，将某个被困住的程序杀掉，然后再重新运行该程序而不用重新启动

> 如果我在 Linux 下以文字界面登陆，在萤幕当中显示错误信息后就挂了～动都不能动，该如何是好！？ 这个时候那默认的七个窗口就帮上忙啦！你可以随意的再按 [Alt]+[F1].....[F7] 来切换到其他的终端机界面，然后以 `ps -aux` 找出刚刚的错误程序，然后给他 `kill` 一下，哈哈，回到刚刚的终端机界面

* 在backgroud中运行 `command &`

### job control
当我们登陆系统取得 bash shell 之后，在单一终端机介面(tty1, tty2...)下同时进行多个工作的行为管理

> 进行工作管理的行为中， 其实每个工作都是目前 bash的子程序，亦即彼此之间是有相关性的。 我们无法以 job control的方式由 tty1 的环境去管理 tty2 的 bash

* 将『目前』的工作丢到backgroud中『暂停』：[ctrl]-z
* `jobs` - 查看当前的jobs
* `fg %N` - 把background的job拿到foreground
* `bg %N` - 让background中的job在background run. example:
```
$ find / -perm +7000 > /tmp/text.txt  # start a new job
# ctrl z
$ jobs ; bg %1 ; jobs  # check jobs；让find在background中运行；查看jobs
```
* `kill` 来关掉job. **kill 后面默认是PID，想kill job，要用%N表示Job ID**
* case: 想要离线，但是又想让某个Job(比如command)继续run
  * 用 `at`
  * 用 `nohup`
    * 注意：`nohup`不支持bush built-in的command，所以要用absolute path

### process management

#### show ps 的 command
* `ps` - static
  * `ps -l`: 仅观察自己的 bash 相关程序
  * `ps aux`: 观察系统所有程序
* `top` - keep tracking
* `pstree` - 程序相关性 show ps as a treee

#### process management
* signal
  * `man 7 singal` 可以查询singal代号
  * 1 SIGNUP 类似重启
  * 9 SIGKILL 强行中断
  * 15 SIGTERM 正常结束
* `kill -signal PID`
* priority 和 nice - 调节ps manage的priority
* 系统资源观察
  * `free` 查看memory
  * `uname` 查看system和kernel相关Info
  * `uptime` 启动时间和load
  * `netstat` 常用在network方面，但是ps management方面也有用,e.g. 查port等
* SUID/SGID(可以粗略理解为runtime时user拥有owner权限的特殊权限) -> [第六章曾经在permission那儿提到过](/2020/04/03/book/笔记/笔记-Linux-part-2/#6-4-3-file-特殊权限：-SUID-SGID-SBIT)
  * SUID的permission其实和process非常有关，因为:运行了password后会取得一个新的process->PID，这个PID产生时通过SUID给了PID特殊的permission
* `fuser` 找出正在使用某file的process
* `lsof` (反向`fuser`)找出某个Process正在使用的file
* `pidof` 找出某个正在运行程序的PID **经常配合`ps aux`使用**

#### SELinux
这部分我是一点概念都没有，就先跳过了


##
















