---
layout: post
title: "Update: Android.mk 中的 LOCAL_SRC_FILES, LOCAL_C_INCLUDES"
date: 2013-10-12 17:00
comments: true
categories: [android,NDK]
---

我在先前的两篇post  
[编写Android.mk中的LOCAL_SRC_FILES的终极技巧](/blog/2013/05/20/write-local-src-files-in-android-dot-mk-ultimate-skills/)  

[编写 android.mk 中 LOCAL_C_INCLUDES 的技巧](/blog/2013/06/25/write-local-c-includes-skills-in-android-dot-mk/)  

中提到了一些编译android.mk文件的技巧, 由于都涉及到了shell命令, 导致不能完全在windows下工作, 下面我使用纯净的makefile语法重新编写了脚本

	# 配置自己的源文件目录和源文件后缀名
	MY_FILES_PATH  :=  $(LOCAL_PATH) \
	                   $(LOCAL_PATH)/../../Classes
					   
	MY_FILES_SUFFIX := %.cpp %.c
					   
	# 递归遍历目录下的所有的文件
	rwildcard=$(wildcard $1$2) $(foreach d,$(wildcard $1*),$(call rwildcard,$d/,$2))
	
	# 获取相应的源文件
	MY_ALL_FILES := $(foreach src_path,$(MY_FILES_PATH), $(call rwildcard,$(src_path),*.*) ) 
	MY_ALL_FILES := $(MY_ALL_FILES:$(MY_CPP_PATH)/./%=$(MY_CPP_PATH)%)
	MY_SRC_LIST  := $(filter $(MY_FILES_SUFFIX),$(MY_ALL_FILES)) 
	MY_SRC_LIST  := $(MY_SRC_LIST:$(LOCAL_PATH)/%=%)

	# 去除字串的重复单词
	define uniq =
	  $(eval seen :=)
	  $(foreach _,$1,$(if $(filter $_,${seen}),,$(eval seen += $_)))
	  ${seen}
	endef
	
	# 递归遍历获取所有目录
	MY_ALL_DIRS := $(dir $(foreach src_path,$(MY_FILES_PATH), $(call rwildcard,$(src_path),*/) ) )
	MY_ALL_DIRS := $(call uniq,$(MY_ALL_DIRS))

	# 赋值给NDK编译系统
	LOCAL_SRC_FILES  := $(MY_SRC_LIST)
	LOCAL_C_INCLUDES := $(MY_ALL_DIRS)
	
完全使用makefile语法编写, 可以工作在所有平台上

我已经在cocos2d-x中提交了一个pull request [https://github.com/cocos2d/cocos2d-x/pull/3921](), 希望能被集成到cocos2d-x的代码库中, 以后使用就不需要自己修改了
