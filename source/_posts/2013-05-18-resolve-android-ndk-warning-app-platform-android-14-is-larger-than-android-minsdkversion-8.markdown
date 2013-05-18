---
layout: post
title: "去除警告 Android NDK: WARNING: APP_PLATFORM android-14 is larger than android:minSdkVersion 8"
date: 2013-05-18 14:41
comments: true
categories: [android,NDK]
---

使用ndk-build编译项目的时候会看到一个警告“Android NDK: WARNING: APP_PLATFORM android-14 is larger than android:minSdkVersion 8”，虽然"不怎么"影响结果，看着碍眼

###解决方法

在项目里的`jni/Application.mk`文件里加入一行

	APP_PLATFORM := android-8

即可.

###为什么会有这个警告?
在android上项目里，可以在`AndroidManifest.xml`中写入
	
	<uses-sdk android:minSdkVersion="8" android:targetSdkVersion="17"/>
	
来表示程序可以运行的最低android设备是`android 2.2(API Version 8)`, 经过详细测试的目标android版本是`android 4.2.2(API Version 17)`.**这里定义的是Java API Version**

再来看一下ndk(版本r8e)目录下的platforms文件夹,可以看到
	
	android-3
	android-4
	android-5
	android-8
	android-9
	android-14
一共有6个文件夹,分别表示相应的**Native API Version**
____________
看到这里就明白了,那个警告的意思就是说,**使用的Native API Version比最低版本Java API要高**,可能导致的问题就是:  
在Native Code里使用了一个`platforms/android-14`下的API函数,然后程序在 `android-8` 的设备上运行,当然这个函数在`android-8`设备上是不存在的,就会崩溃了
____________

###为什么Native API的版本数量会少于Java API?
因为android在版本升级的时候,有时候只升级了Java层的API,而Native层的却没有变化
