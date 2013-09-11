---
layout: post
title: "cocos2d-x 2.1.4 启用C++11后使用 NDK r9 gcc 4.8无法编译"
date: 2013-08-08 23:47
comments: true
categories: [android,NDK]
---

下载android ndk r9版本后,发现cocos2d-x无法编译通过报一大堆错误和警告,仔细查看是这两个
		
		warning: invalid suffix on literal; C++11 requires a space between literal and identifier
		error: format not a string literal and no format arguments

第一个警告是由于头文件不符合C++11的标准引起的，第二个警告是因为格式化字串函数产生的,只需要给gcc参数让其忽略掉这两个错误即可。  
打开android项目的Application.mk文件，在APP_CPPFLAGS变量后面添加
		
		-Wno-format-security -Wno-literal-suffix
		
就可以了。