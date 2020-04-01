---
title: Linux 学习笔记
date: 2020-03-30 16:13:33
tags: 
 - books
---

鸟叔linux 笔记。still in progress。

reminder：
last step is to 整理成其他人能理解的东西 by integrating and adding context.

<!-- more -->


## 第5章 Linux 的档案权限与目录配置
多人多工环境 --> 权限问题
file 存取分为3种身份： owner/group/others
3种身份各有 read/write/execute 三种权限


### 5.1 owner + group
Linux里面，任何一个file都具有User, Group及Others 三种身份的权限设定

所有的系统上的帐号与一般身份user，还有那个root的相关info， 都是记录在/etc/passwd这个档案内的。
个人的密码记录在/etc/shadow这个档案下
Linux所有的群组名称都纪录在/etc/group内

### 5.2 Linux  file permission 概念
#### 5.2.1 Linux file属性
`ls -al` ==> `-rw-rw-r-- 1 xin xin 10  Sep 13 2015 dx`
##### (1) permission control: 
-rwxr--r-- 这种
10位(1+3+3+3)：(-)(---)(---)(---)  ==> (file type)(owner)(group)(others)
第一位：表示这个file是
d: dir  
-: file
l: link file
b: block device，随机读写block型设备，如disk
c: character device，一次性读取型设备，如键鼠

##### (2) 第二栏的数字 表示有多少 file name 连结到此节点(i-node)
每个file都会将他的permission 与 属性 记录到file system的i-node中，不过，我们使用的dir tree 却是使用file name来记录，因此每个file name 就会连结到一个i-node啰！这个属性记录的，就是有多少不同的file name连结到相同的一个i-node number.

##### (3) 第三栏表示这个file(或dir)的 owner
##### (4) 第四栏表示这个file的所属group
##### (5) file size, unit = bytes
##### (6) create date or last modify date
##### (7) file name `.ABC`这种前面有`.`的是隐藏file, 用`-a` 可以显示


#### 5.2.2 如何改变档案属性与权限
#####（1）chgrp ：改变file 的 group
要被改变的group name 必须要在/etc/group file 内存在
#####（2）chown ：改变file 的 owner
owner必须是已经存在系统中的account，也就是在/etc/passwd 这个file中有记录的owner才行
（什么时候需要改变Owner? 比如你把file copy 给别人的时候）
#####（3）chmod ：改变file的permission, SUID, SGID, SBIT等等的特性
* 数字类型改变file permission
r:4   w:2    x:1

* 符号类型改变file permission
`chmod` +
`u(user),g(group),o(others),a(all)`+
`+, -, =` +
`r, w, x`
e.g. `chmod u+x,g=rwx test.file`

#### 5.2.3 dir与file 之 permission意义：
1. 对file的重要性：
(1) 为什么要用`x`:
不像windows系统，Linux下的file是否能被执行是由这个`x`这个权限来决定的
(2) 有`w` 并没有删除file的权限，rwx都是针对于file 内容来说的，与file name 无关。

2. 对dir 的重要性：
(1) file是存放content的，dir是存放file name的。所以，针对于dir的`rwx`：
`r`: 可`ls`
`w`: dir里面的东西增删改移
`x`：能否进入该dir成为working dir.   PS:`cd=change dir`


#### 5.2.4 Linux file type 与 后缀名
##### file type 
* regular file `-`
1. ASCII。纯文字。如configuration等。
2. binary。可执行scripts。
3. data。有些程序在运作的过程当中会读取某些特定格式的file，那些特定格式的file可以被称为data file。
e.g. Linux在user log in时，都会将登录的data记录在/var/log/wtmp 这个file内，该file是一个data file，他能够透过last这个指令读出来！但是使用cat时，会读出乱码～因为他是属于一种特殊格式的file.

##### directory `d`
##### link `l`
类似于windows里的快捷方式？ 是一个`->` 这样的Map

##### device。通常集中在/dev下
* block device  `b`:  如disk,可以随机的在disk的不同block读写，这种device就是block device
* character device  `c`：如keyboard/mouse，一次性读取。

##### sockets `s`
主要用于Network。通过这个socket来进行data transfer。

##### FIFO, pipe `p`
特殊类型。主要用于解决多个程序同时存取一个档案所造成的错误问题。


### 5.3 Linux目录配置
#### 5.3.1 Linux目录配置的依据--FHS（Filesystem Hierarchy Stardard）

 。	|可分享的(shareable)|	不可分享的(unshareable)
-------|---------------------------|-----------------
不变的(static)|	/usr (软体放置处)|	/etc (设定档)
。|/opt (第三方协力软体)	|/boot (开机与核心档)
可变动的(variable)|	/var/mail (使用者邮件信箱)|	/var/run (程序相关)
。|/var/spool/news (新闻群组)|	/var/lock (程序相关)



可分享的：可以分享给其他系统mount使用的目录。
不可分享的：自己机器上面运作的device file 或者是与程序有关的socket file 等，由于仅与自身机器有关，所以不适合分享给其他主机了。

不变的：如函式库、文件说明file、admin所管理的主机服务configuration file 等
可变动的：经常变动的东西。

其实，FHS针对dir tree架构仅定义出三层dir底下应该放置什么:
* / (root, 根目录)：与开机系统有关；
* /usr (unix software resource)：与software安装/执行有关；
* /var (variable)：与系统运作过程有关。

##### `/` root(根目录)
`/`也与开机/还原/系统修复等动作有关。
FHS标准建议：根目录(/)所在partition(分割槽)应该越小越好，且应用程式所安装的software最好不要与根目录放在同一个分割槽内，保持根目录越小越好。如此不但效能较佳，根目录所在的档案系统也较不容易发生问题。***<== 联系mount的概念。***

`/`下应该有以下这些dir, 即使没有实体dir，FHS也希望至少有link file存在:


FHS要求必须存放：

dir| for what
--|--
/bin |单人维护模式下还能执行的command，可以被所有permission使用
/boot |存放开机所需的file，包括Linux kernel file 以及开机选单与开机所需config file。 Linux kernel常用的file name为：vmlinuz-*，如果使用的是grub这个开机管理程序，则还会存在/boot/grub/这个dir。
/dev |存放所有devices
/etc |存放config file. 一般是所有人`r`，但只有root `w`。 FHS建议不要放binary file在这里面。几个重要的dir建议放在这个下面：(1) `/etc/opt`: 放第三方software相关的config 
/lib |/lib放置的则是在开机时会用到的函式库，以及在/bin或/sbin底下的指令会呼叫的函式库。`/lib/modules/`必须存在：这个目录主要放置changable的kernel相关modules(驱动程式)
/media|存在可移除装置
/mnt|存放暂时mount的额外device
/opt|存放第三方software
/run|存放开机后产生的各种东西
/sbin|为开机过程中所需要的bin，里面包括了开机、修复、还原系统所需要的指令。
/tmp |暂时存放file的地方
/usr| 后续介绍
/var| 后续介绍


建议存放：

|dir| for what
|--|--
|/home |每增加一个user，都会增加一个相应的home dir。`~` 表示当前user的/home, ~AAA 表示AAA的home dir
|/root |root的home dir
/lib<qual>|与/lib格式不同之外的函数库，如/lib64

以上是FHS标准，以下的也重要：

|dir| for what
|--|--
|/lost_found |file system 发生错误时，把遗失的放在此
|/proc| virtual filesystem，此dir下放的东西都是存在memory中，存放如kernel, process, devices status and network
|/sys| 也是virtual file system 主要记录kernel和hardware相关的东西

mount 的时候 /etc, /bin, /dev , /lib, /sbin 必须跟 `/` 放在一起，用于救援模式。


##### /usr 的意义和内容
FHS建议所有developer，应该将他们的资料合理的分别放置到这个目录下的次目录，而不要自行建立该software自己独立的目录。

FHS要求必须：

|dir| for what
|--|--
|/usr/bin/ | 
|/usr/lib/ |
|/usr/local/ |自己安装的software安装在这个下面
|/usr/sbin/|非系统正常运作所需要的系统command，如server 中的damon
|/usr/share/| 


FHS建议：


|dir| for what
|--|--
|/usr/include/|c/c++等程序语言的header与include放置处
|/usr/src/|源代码


##### /var 的意义与内容：
如果/usr是安装时会占用较大disk容量的目录,/var就是在系统运作后才会渐渐占用disk容量的目录。
只要针对常态性变动型file，如cache,log等

#### 5.3.2 directory tree
<img src="https://upload-images.jianshu.io/upload_images/10023701-6c362df0911eb073.png"/>
dir tree

注意：图中的Link不一定适用于所有distribution。

#### 5.3.3 绝对路径（从`/`开始）与相对路径（当前路径开始）




## 第6章 Linux file与dir管理
### 6.1 目录与路径

`- 代表前一个工作目录`
`~ 代表『目前使用者身份』所在的家目录`
`~account 代表account 这个使用者的家目录(account是个帐号名称)`

`pwd` 和 `pwd -P`的差别在于“是link的file/dir"，前者显示当前路径，-P显示的真正的Path。

#### 6.1.3 关于执行档路径的变数： $PATH
1. `PATH="${PATH}:/root"` --> 将`/root` 加入到`$PATH`之后。$PATH中包含的所有dir用`:`隔开。

2. `$PATH`中两个目录下都有某个指令，最终执行哪一个，取决于哪个目录写的靠前。

3. 为了安全起见，不建议将`.`(当前路径)加入PATH的搜寻目录中。

### 6.2 档案与目录管理
#### 6.2.2  cp, rm, mv
1. 直接用`cp`的话cp生成的file会使用default permission。要想把权限也一直copy，用`cp -a`或`-p`。

2. `cp -s` `-s`是symbol link，可以理解为“快捷方式”, 就是把cp出的新的file 指向原始file
`cp -l` `-l`是hard link，与原文件所有都一模一样，link数+1.

查看file 指令 summary:
```
cat, nl, more, less, head, tail 
      n, N, g, G
```

#### 6.3.5 修改file时间或create a new file: touch
修改内容时间(mtime) --> 不改内容这个不变
修改状态时间(ctime) --> touch 可改


### 6.4 file与dir的预设权限与隐藏权限
#### 6.4.1 file 预设权限
`umask`: 指定 目前使用者在建立file或dir时的**权限预设值**
umask的分数指的是 该预设值需要 **减掉** 的权限 ==> 其实就相当于在创建某个file/dir之后自动拿掉一定的权限

#### 6.4.2 file 隐藏属性
chattr [+-=][ASacdistu]档案或目录名称
lsattr [-adR]档案或目录

#### 6.4.3 file 特殊权限： SUID, SGID, SBIT
1. SUID(set UID): 当s出现在owner的x权限上时(比如/usr/bin/passwd 的权限 -rw**s**r-xr-x)，被称为SUID。
限制与功能：SUID仅对binary program(不能Shell script) 有效。执行者需要有x权限。仅在run-time有效。执行者将（暂时）拥有Owner权限。

```
RECALL:
所有的系统上的帐号与一般身份user，还有那个root的相关info，都是记录在/etc/passwd这个档案内的。
user的密码记录和secure info在/etc/shadow这个档案下
/usr/bin/passwd: binary 用来改密码的binary program
```
以shadow + passwd 为例：
GIVEN： 
/etc/shadow 只有root可以读写
/usr/bin/passwd user($xindiao) 有r+x权限，但没有w

WHEN：
/passwd 的权限： `-rw**s**r-xr-x 1 root root ...` ==> user(xindiao)可以执行这个file，但这个file 的 owner 是root ==> owner上的x 变成了s ==> SUID

THEN：
user($xindiao) 在执行passwd的**过程中** 会暂时拥有root 权限 ==> 所以 user($xindiao) 就可以修改 /etc/shadow 


2. SGID(set GID): 当s出现在group的x权限上时。如-rwx--**s**--x
与SUID类似，也可以用于dir

3. SBIT(Sticky Bit)
只针对于dir
有SBIT的dir中，user只能针对自己建立的file或者dir进行delete/rename/move等，而无法删除他人file

#### 6.4.4 查看 file 类型 用 "file"

### 6.5 command 与 file 的 搜寻
#### 6.5.1 command对应 的完整file name 的搜寻
`which [-a] command` ==> `-a` all

**which预设是找PATH内所包括的目录**

#### 6.5.2 file name 的搜寻
1. whereis 由一些特殊的dir中寻找file name，主要针对/bin, /sbin, /man
`whereis -l` list whereis 会去查询的目录
`-b`: 只找binary file
`-m`: 只找man下的
`-s`: 只找src
`-u`: 只找不在/bin, /man, 和 `/src`下的其他特殊file

2. `locate` 
只要完整file name(包含Path)中包含要搜索的词，就会显示出来。用于忘记某个file具体叫什么名字的时候很有用。
**限制**：Locate 只搜索 /var/lib/mlocate（已建立的资料库，大概每天更新一次，新creat的file可能会找不到） 里面的data找。
手动更新`mlocate.db`: updatedb(执行要等几分钟)

options: 
-i: ignore 大小写
-c: 只看count
-l: 仅输出几lines
-r: regex

3. `find [PATH] [option] [action]` 
与time有关的option:
有-atime, -ctime, -mtime
还有n, +n, -n, newer file等 (n是数字)

find的特殊功能就是能够进行**额外动作**
e.g.
<img src="https://upload-images.jianshu.io/upload_images/10023701-9c95bc6e89f1313e.png"/>
find的action sample.png

sample中特殊的地方有`{}` 以及`\;` 还有`-exec` 这个关键字，这些东西的意义为：
`{}` 代表的是『由find 找到的内容』，如上图所示，find 的结果会被放置到{} 位置中；
`-exec` 一直到`\;` 是关键字，代表find 额外动作的开始(-exec) 到结束(\;) ，在这中间的就是find 指令内的额外动作。
因为`; `在bash 环境下是有特殊意义的，因此利用`/`来标识。



## 第7章 Linux disk 与 file system管理
### 7.1 认识Linux file system
**From other websites:**
`File System是建立在Disk上面的`
`文件系统是对一个存储设备上的数据和元数据进行组织的机制。它的最终目的是把大量数据有组织的放入持久性(persistant)的存储设备中，比如硬盘和磁盘。文件系统(file system)是就是文件在逻辑上组织形式，它以一种更加清晰的方式来存放各个文件。`

#### 7.1.2 file system 特性
Format(格式化)：每种OS所设定的file attribute(e.g. owner, group, time 参数)/permission不完全相同，为了存放这些file所需的data，**需要将partition(分割槽)进行格式化，以成为OS系统能够利用的file system** 。

以前：一个partition --格式化--> 一个file system
现在：一个partition --格式化--> 多个file system
    and 多个partition --> 一个system
所以：现在我们称 **一个可被mount的data为一个file system**


FileSystem通常会将file和 file permission等attribute存放在不同block: 
- permission和attribute存放在inode中;
- 实际data放到data block中。
- 另外还有一个superblock会记录整个file system的整体Info，包括inode与block的总量、使用量、剩余量等。

每个inode与block都有编号，每个file都会占用一个inode，inode内有file data 存放的block号码。

两种存放方式：
1. indexed allocation(inode/block)
<img src="https://upload-images.jianshu.io/upload_images/10023701-209411f10a0eb303.png"/>
inode/block data存取示意图

2. FAT(用于如U盘)
<img src="https://upload-images.jianshu.io/upload_images/10023701-2d23275cf39896b1.png"/>
FAT file system 资料存取示意图

这种如果block太过分散，disk无法转一圈读取所有资料的话，会很慢，所以过一段时间就需要 disk重组。

#### 7.1.3 Linux的EXT2(Linux second extended file system) file system(inode)

因为：inode 与 block数量庞大，不易管理
所以：block group:
<img src="https://upload-images.jianshu.io/upload_images/10023701-c6ab07488f9de021.png"/>

**boot sector**: 安装开机管理程序。file system最前端。
- data block: 
基本限制：
-- block的大小和数量在format之后不能改变。
-- 每个Block内最多存放一个file的资料。
-- so, 若file小与block，则该block剩余容量就浪费了。

- inode table
inode所记录的file data至少有：
-- rwx
-- owner/group
-- size
-- ctime
-- atime
-- mtime
-- flag, e.g. SUID
**-- pointer**

特性：
-- 每个inode都是固定的128 bytes(随着File system可变)
-- 每个file只有一个inode
-- so, 一个File System上能够穿件的file 数量与inode的数量有关
-- system在读取file时，先找到inode，分析inode所记录的permission和user是否符合，若符合再开始读取block的内容

inode/block 与 file 大小关系：
inode 记录block 号码的区域定义为12个直接，一个间接, 一个双间接与一个三间接记录区：
<img src="https://upload-images.jianshu.io/upload_images/10023701-5a24a783db4ec6b6.png"/>
inode结构示意图

- superblock
记录整个filesystem 相关info的地方
主要有：
-- block 与 inode总量
-- 未使用/已使用 inode/block数量
-- block与inode的大小
-- File System 的 mount时间、最近一次写入data 时间、file system consistency check等file system的相关info
-- 一个valid bit数值，若此file system已被mount, valid bit == 0, otherwise valid bit == 1

- Filesystem description
描述每个block group 的开始与结束的block 号码，以及说明每个区段（？）分别介于哪个block号码之间

- block bitmap
1. ADD: 从这里可以知道哪个block是空的。
2. DELETE: 删除后相应的block号要被修改标识

- inode bitmap
与block bitmap类似

**`dumpe2fs`**: 查询EXT superblock info的 command

#### 7.1.4 与directory tree 的关系
General: **File 记录data内容， directory 记录file name**
Details:
* Directory: 
inode: 记录 此 directory 的permission + attribute & block number
block: 记录 file name & inode number of files

* File:
e.g. 1 block = 4K, file size = 100K, then: 
1 x inode: block numbers
25 x block: 100 / 4 = 25
1 more block: 一个inode只能指向12个block, 所以需要多一个block来存block numbers

#### 7.1.5 EXT2/EXT3/EXT4 File的存取与Journaling filesystem的功能
##### create 一个新的file的system workflow:
1. check permission: 是否对directory有w权限
2. 根据 inode bitmap找到available inode number，写入file的permission + attribute
3. 根据block bitmap 找到available blcok number，写入实际的data，且更新inode中的block 指向
4. 更新inode bitmap + block bitmap， 并更新superblock

##### 如果发生inconsistence ==> Journaling filesystem
早期如果inconsistence: 在开机时借助一些Metadata和flag去全面check整个系统，太慢。
现在：Journaling filesystem：专门用一个block来记录写入和修改file的步骤
预备(记录 预备开始) --> 实际写入(更新metadata资料) --> 结束(记录 完成)


#### 7.1.6 Linux File System的运作
```
RECALL: 所有的data 都得要载入到memory后CPU才能够对该data进行处理
```
==> Problem: 如果经常修改超大的file，那么会很慢（因为disk写入速度慢）

==> 解决办法：Asynchronously
利用一个flag: clean/dirty --> 系统会定期把Memory中dirty的file与disk同步

==> 即使有Asynchronously，我们还希望效率更高，于是linux把常用的file直接存到memory中的cache区。所以Linux系统的Memory被用尽是很正常的。
PS：可以用`sync`来强制同步disk。关机等action时sync也会被自动调用。所以强行断电的时候可能会损毁file。

#### 7.1.7 Mount point
```
每个filesystem都有独立的inode / block / superblock等info，
这个file system要能够connect到directory tree才能被我们使用。
```
***将File System 和 Directory Tree 连接的action ==> Mount***
`重点：Mount point 一定是目录，该目录为进入该File System的入口`

所有Mount point 的inode number应该都是一样的 --> 最顶层目录的inode number(一般为2, depends on 预设的File System)

**From Other websites:**
```
不是所有逻辑上的父子关系都必须要是同一个设备，
决定不同路径对应到哪个设备的机制就叫做mount（挂载）。
通过mount，可以设置当前的路径与设备的对应关系。

每个设备会设置一个挂载点，挂载点是一个空目录。
一般来说必须有一个设备挂载在/这个根路径下面，叫做rootfs。
其他挂载点可以是/tmp，/boot，/dev等等，
通过在rootfs上面创建一个空目录然后用mount命令就可以将设备挂载到这个目录上。
挂载之后，这个目录下的子路径，就会映射到被挂载的设备里面。
当访问一个路径时，会选择一个能最大匹配当前路径前缀的挂载点。
比如说，有/var的挂载点，也有/var/run的挂载点的情况下，
访问/var/run/test.pid，就会匹配到/var/run挂载点设备下面的/test.pid。
同一个设备可以有多个挂载点，同一个挂载点同时只能加载一个设备。
访问非挂载点的路径的时候，按照前面所说，其实是访问最接近的一个挂载点，
如果没有其他挂载点那么就是rootfs上的目录或者文件了。
```

#### 7.1.8 其他Linux support的File System 与VFS
Linux 标准 File System是ext2 和 ext3, ext4(增加了Journaling system)。但支持很多种FS.

Linux 的kernel 如何管理这些认识的FS?
通过Virtual Filesystem Switch 的核心功能去读取filesystem 


### 7.2 File System的简单操作
#### 7.2.1 disk 和 directory的usage
* `df`: 把系统中所有的file system 列出来
由于`df` 主要读取的资料几乎都是针对一整个File system，因此读取的范围主要是在Superblock 内的info， 所以这个指令显示结果的速度非常的快速。

* `du`：列出当前目录下的所有file usage
会直接到file system内去搜寻所有file info，所以慢


#### 7.2.2 实体连结与符号连结： ln

* hard link: 只是在某个目录下新增一条file name 连结到某inode号码的关连记录
多个file name 对应一个inode number
<img src="https://upload-images.jianshu.io/upload_images/10023701-0881d0456901f1a6.png"/>
hard Link file读取示意图

限制：
  不能跨Filesystem.
  不能link 目录. --> 不建议做，可能会因此产生dead loop

* Symbolic Link (快捷方式)
两个file指向不同的inode number.

<img src="https://upload-images.jianshu.io/upload_images/10023701-1a97ad94f74e6044.png"/>
symbolic link 连接的file读取示意图

* `ln` command


### 7.3 Disk的partition, format, check 和 mount

若想新增一个disk，需要：
1. 对disk进行partition，以建立可用的partition ；
2. 对该partition 进行格式化(format)，以建立系统可用的filesystem；
3. 若想要仔细一点，则可对刚刚建立好的filesystem 进行check；
4. 在Linux 系统上，需要建立mount point(亦即是目录)，并将他mount上来；


#### 7.3.1 观察disk的partition状态
`lsblk` 列出系统上的所有disk列表
`blkid` 列出装置的UUID 等参数
`parted` 列出disk的partition类型与partition info



#### 7.3.2 disk分割： gdisk/fdisk

#### 7.3.3 disk format(create and set up File System)


#### 7.3.4 File System Repair(check?)

#### 7.3.5 File System 的mount 与umount
进行mount前，先确定几件事：
单一File System不应该被重复mount在不同的mount point(目录)中；
单一目录不应该重复mount多个File System；
要作为Mount point的目录，理论上应该都是空目录才是。

注意：如果你要用来mount的目录里面并不是空的，那么mount了File System之后，原目录下的东西就会暂时的消失。

`mount` : 将device(file system) mount到某个directory
`remount`: 
`umount`: 

### 7.4 设定开机mount
####  7.4.1 开机mount  /etc/fstab 及/etc/mtab

系统mount的一些限制：
根目录`/ `是必须mount的﹐而且一定要先于其它mount point 被mount进来。
其它mount point 必须是已建立的directory﹐可任意指定﹐但一定要遵守必须的系统目录架构原则(FHS)
所有mount point 在同一时间之内﹐只能mount一次。
所有partition 在同一时间之内﹐只能mount一次。
如若进行umount﹐您必须先将current directory移到mount point(及其子目录) 之外。

开机系统mount: 在`/etc/fstab` 定义和修改
实际filesystem的mount是记录到/etc/mtab与/proc/mounts这两个file当中

每当无法顺利开机成功，而进入单人维护模式当中，那时候的`/`是read only的状态，当然你就无法修改/etc/fstab ，也无法更新/etc/mtab ==> 
`$ mount -n -o remount,rw /`


### 7.5 memory 置换空间(swap) -->如今一般用不到
```
RECALL(from 第三章): 
swap：就是disk模拟成为memory，
由于swap并不会使用到目录树的mount，所以用swap就不需要指定mount point。
```
CPU所读取的资料都来自于memory，那当memory不足的时候，为了让后续的program可以顺利的运作，因此在memory中暂不使用的program与data就会被挪到swap中了。

（因为如今一般用不太到，所以Details 跳过）




## 第8章 File与File System的compress,package与backup

















