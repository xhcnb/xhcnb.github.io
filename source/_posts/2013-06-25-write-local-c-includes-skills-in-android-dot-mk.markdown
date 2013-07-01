---
layout: post
title: "编写 android.mk 中 LOCAL_C_INCLUDES 的技巧"
date: 2013-06-25 14:30
comments: true
categories: [android,NDK]
---

在编写`android.mk`的过程中,免不了要修改`LOCAL_C_INCLUDES`来设置头文件的include目录, 一般写成这样

	LOCAL_C_INCLUDES := $(LOCAL_PATH)/../../Classes \
	 					$(LOCAL_PATH)/../../Classes/game \
	 					$(LOCAL_PATH)/../../Classes/logic \
	 					$(LOCAL_PATH)/../../Classes/view					
有一个目录就要写一行, 实在繁琐, 有没有写法可以把源码目录下的所有子目录都引入呢, 看下面

	LOCAL_C_INCLUDES := $(LOCAL_PATH)/../../Classes
	LOCAL_C_INCLUDES += $(shell ls -FR $(LOCAL_C_INCLUDES) | grep $(LOCAL_PATH)/$ )
	LOCAL_C_INCLUDES := $(LOCAL_C_INCLUDES:$(LOCAL_PATH)/%:=$(LOCAL_PATH)/%)
即可把`$(LOCAL_PATH)/../../Classes`目录和子目录全部包含进来

还有一种写法, 就是使用`sed`命令, 效果是一样的, 我对`sed`不是很熟悉, 简单写了一下

	LOCAL_C_INCLUDES := $(LOCAL_PATH)/../../Classes	
	LOCAL_C_INCLUDES += $(shell ls -FR $(LOCAL_C_INCLUDES) | grep $(LOCAL_PATH)/$ | sed "s/:/ /g" )

这两行和上面三行的结果是一样的

如果要方便的引入源文件到`android.mk`文件里, 可以参考我的这篇post:[编写Android.mk中的LOCAL_SRC_FILES的终极技巧](/blog/2013/05/20/write-local-src-files-in-android-dot-mk-ultimate-skills/)

以上代码在 mac + NDK r8e 下测试通过
