---
layout: post
title: "解决 xcode 5 资源文件更新后不自动复制的问题"
date: 2013-11-06 18:37
comments: true
categories: [Mac,xcode]
---

升级到xcode5后一很奇怪的问题:资源文件以文件夹形式引入在xcode里, 但是如果更新资源文件夹下的图片, xcode不会自动复制到目标设备, 必须clean, 再build.  
猜测可能的原因是xcode根据文件夹的更新日期来判断此文件夹需不需要重新复制

#####解决方法: 
设置一个Build Phases在每次build的时候touch一下资源文件, 这样xcode就会重新复制资源文件了

#####步骤:
1.选中项目, 添加一个`Run Script Build Phase`

{% img /images/20131106_01.png %} 
<!--more-->
2.在新建的phase里编辑命令  

		find ${SRCROOT}/../Resources -exec touch {} \;

这个命令将循环touch Resource目录下的所有子目录和文件, 修改路径又适合自己的需求

{% img /images/20131106_02.png %} 

3.拖动这个Run Script到默认的`Copy Nundle Resources`上面, 这样新建的这个phase就会xcode复制文件之前运行

{% img /images/20131106_03.png %} 


######Enjoy!
