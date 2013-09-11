---
layout: post
title: "查看android上程序的内存占用"
date: 2013-09-04 11:47
comments: true
categories: [android]
---

在android设置上开发, 要查看android程序的实际内存占用情况有两种方法  
####1. 使用linux命令`top`

android基于linux, 所以我们可以使用`top`命令来查看内存使用

打开终端:
		
		# 进入android的shell环境
		adb shell
		#执行top命令, 只显示内存占用最多的前4个程序
		top -m 4
可以看到输出

{% img /images/adb_top.png %} 

RSS一栏即为内存占用


####2. 使用eclipse中的ADT工具

eclipse中安装adt插件后,在DDMS界面可以查看android设备的很多信息, 其中就包括内存使用

把android设备连接电脑,打开eclipse, 从左上切换到DDMS界面

{% img /images/ddms.png %} 

然后再点System Information ,选 Memory usage, 把鼠标移动到想查看的进程上,就可以看到内存使用了

{% img /images/ddms_memory.png %} 
