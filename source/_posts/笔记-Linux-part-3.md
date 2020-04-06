---
title: Linux 学习笔记 - 第三部分
date: 2020-04-04 16:13:33
tags: 
 - books
---

鸟叔的Linux私房菜

第三部分 学习 Shell 与 Shell scripts

TBD: 整理成更加分类的模式 或 直接成cheat sheet

[link](http://cn.linux.vbird.org/linux_basic/linux_basic.php#part1)

简书： https://www.jianshu.com/p/b2952d00efe4

<!-- more --> 

## 第11章 认识与学习 BASH

### 一些零散的points：TBD: 把command 分类
* （i.e. 单引号变量不置换、双引号置换）变量内容若有空格符可使用双引号『"』或单引号『'』将变量内容结合起来，
  但双引号内的特殊字符如 $ 等，可以保有原本的特性，如：『var="lang is $LANG"』则『echo $var』可得『lang is en_US』
  单引号内的特殊字符则仅为string，如：『var='lang is $LANG'』则『echo $var』可得『lang is $LANG』
*  \`command\` 或者 $(command) e.g. 『version=$(uname -r)』再『echo $version』可得『2.6.18-128.el5』
* 若该变量需要在其他子程序运行，则需要以 export 来使变量变成环境变量：『export PATH』
* ENV variables: 通常大写字符为系统默认变量，自行配置变量可以使用小写字符，方便判断 
* 取消 variable 的方法为使用 unset ：『unset 变量名称』
* export： 自定义变量(local)转成环境变量(global)
* `read`
* `array`
* `declare` `-a -i -x -r` 
* `ulimit` 
* `path/a/b`, `path#a`, `path%a` 替换、从前删除、从后删除
* `-`, `=`, `?` 判断并赋值
* <img src="https://personal-bucket-prod.s3-us-west-2.amazonaws.com/books/linux/bash_shell_load_procedure.png"/>
* bash 中常用符号：
	* all wildcard
	* `\` escape
	* `|`  pipe 前一个command 的stdout作为后面一个command的stdin
		* 仅能接受 stdout(not stderr)
	* `$` 表示后面跟variable
	* `{}` 中间为command block
	* `>`,`>>`,`2>`,`2>>`, `<`, `<<`
		* codes：
			* stdin: 0
			* stdout: 1
			* stderr: 2
		* 特殊需求：
			* 不想显示，也不想输出到某个file：利用“黑洞文件”: /dev/null
			* stdout和stderr输出到同一file：`command > list 2>&1`
* `cut -d "xxx" -f 2,3` OR `cut -c 12-`
* `sort`
* `uniq`
* `wc`
* `tee`
* `tr`
* `join`
* `split` 
* `xargs` 对于不支持pipe的command会有用
* `sed` stream editor
* `printf` 按自己想要的格式print
* `awk` linux中的excel


### bash shell

* `#/bin/bash` 宣告这个script用的shell名称，这样`~/.bashrc`（bash相关的env file）才能被load
* declare 主要ENV e.g. `PATH`(这样用外部command的时候就不用写绝对路径了), `LANG`等
* 最后用`exit N`结尾
* `$((计算式))` 来进行数值运算
* 用`source X.sh`来run shell script会把`expor`的变量放到global ENV里，但是用`sh X.sh`运行则不会
* `test` + `&&` or `||` 来做判断
* `[]`
	* 在中括号 [] 内的每个component都需要有space来分隔
	* 在中括号内的变量、常数，最好都以双引号括号起来 -> 表示是一个整体
* `$0`(`$number`)、`$#`、`$@`
* if..elif..fi
```
if []; then
	XXXX
elif []; then
    YYYY
fi
```
* case $ in..)..;;..ecac
```
case  $变量名称 in   <==关键字为 case ，还有变量前有钱字号
  "第一个变量内容")   <==每个变量内容建议用双引号括起来，关键字则为小括号 )
	程序段
	;;            <==每个类别结尾使用两个连续的分号来处理！
  "第二个变量内容")
	程序段
	;;
  *)                  <==最后一个变量内容都会用 * 来代表所有其他值
	不包含第一个变量内容与第二个变量内容的其他程序运行段
	exit 1
	;;
esac
```
* function. **注意：shell script的参数传递是通过$number的**
```
function fname() {
	XXXX
}
```
* while loop
```
whil []
do
	XXXX
done
```
* until loop
```
until []
do
	XXXX
done
```
* for loop
```
for var in con1 con2 con3 ... OR for (( i=1; i<=$num; i=i+1 ))
do
	XXXX
done
```

* `sh -x` debug -> print出每一步


































