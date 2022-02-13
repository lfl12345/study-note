## shell是什么

Shell是一个命令行解释器，它为用户提供了一个向Linux内核发送请求以便运行程序的界面系统级程序，用户可以用shell来启动、挂起、甚至是编写一些程序。Linux中不至一种shell，但是最主要的是bash shell

![image-20211130163017490](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211130163017490.png)

##  Shell脚本的执行方式

### 脚本格式的要求

* 脚本要以#!/bin/bash开头，表示脚本以那种shell运行
* 脚本要有可执行权限

### 脚本的常用执行方式

直接输入脚本的相对路径或绝对路径，需要此时脚本需要有可执行权限

使用sh + 脚本，不用赋予脚本可执行权限

## Shell的变量

### Shell变量介绍

Linux Shell中的变量分为系统变量和用户自定义变量。

系统变量：$HOME、$PWD、$SHELL 、$USER等等，

显示当前shell中所有变量：set命令

### Shell变量的定义

## 运算符

![image-20211130171812685](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211130171812685.png)

## 条件判断

![image-20211130172429359](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211130172429359.png)

![image-20211130173732488](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211130173732488.png)

## 流程控制

![image-20211130174432006](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211130174432006.png)

![image-20211130174549816](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211130174549816.png)

![image-20211130174915855](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211130174915855.png)

![image-20211130180128353](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211130180128353.png)

## 系统函数

![image-20211130183005181](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211130183005181.png)

![image-20211130183027354](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211130183027354.png)

## 自定义函数

![image-20211130183256706](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211130183256706.png)

