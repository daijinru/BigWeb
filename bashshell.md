# bash shell

## 什么是bash？
bash是Bourne Again Shell的简称，是从unix系统中的sh发展而来，是用户和linux内核交互的工具，用户通过bash操作内核完成系统的使用和管理。

## shell 的种类
* /bin/sh （已经被/bin/bash替代）
* /bin/bash （默认的shell）
* /bin/ksh （源自贝尔实验室，兼容bash）
* /bin/tcsh（整合了 C shell，功能更为强大）
* /bin/csh （已经被tcsh取代）
* /bin/zsh （源自ksh功能更为强大）
* 每种shell的功能比较类似，但是语法稍有不同

## bash shell的变量概述
### 变量是代表一些值得符号：
* 可以通过变量来设置shell或者其他程序。
* 变量存在于内存中。
* Linux有自定义变量local和环境变量environment
* 自定义变量和环境变量作用范围不同
### 可以通过以下指令来查看变量
* set可以查看所有变量
* env可以查看所有的环境变量

## bash shell有以下几种设置方式：
* 通过自定义变量来设置
    * VARIABLE = value 定义变量
    * echo $VARIABLE 获取变量值
    * 不能已特殊字符开头定义变量名
    * 使用双引号才能把变量名传入到变量值
    * unset VARIABLE 删除变量
    * HISTFILESIZE:记录历史指令保存
    * COLUMNS:终端窗口显示的宽度
    * LINES：终端窗口显示的高度
    * PS1：设置提示符号前面的内容样式
* 通过别名（Aliases）来设置
    * $alias lss='ls-la'
    * 可以用alias自身查看所有的别名
    * 使用alias和alias查看值
    * type -a alias名称
    * unalias删除定义
* 通过set指令设置
* 通过环境变量设置

