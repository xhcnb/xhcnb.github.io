---
layout: post
title: "[原创]使用eclipse调试cocos2d-x Native C++ 代码"
date: 2013-05-17 16:34
comments: true
categories: 
---

前提条件:

* 你已经下载coccos2d-x和[NDK](http://developer.android.com/tools/sdk/ndk/index.html),并且会使用`build_native.sh`和[eclipse](http://www.eclipse.org/downloads/)编译cocos2d-x项目


##安装NDK Plugin
英文好的同学可以直接查看官方说明[Using the NDK plugin](http://tools.android.com/recent/usingthendkplugin),这里我简单翻译一下

1. 打开eclipse,在菜单里选择Help->Install New Software...
2. 弹出的窗口里第一个框"Work with:"后面输入`https://dl-ssl.google.com/android/eclipse/`
3. 稍等一会,下面会出来"Developer Tools"和"NDK Plugins",全部选中,Next,同意协议,等安装完成后,重启eclipse
4. eclipse重启后,到Eclipse->Preferences->Android->NDK,在这里设置你的NDK目录,比如我的,我设置到`/android/android-ndk-r8e`
5. 安装完成


##导入cocos2d-x目录下的示例游戏
这里我以`cocos2d-2.1rc0-x-2.1.3/samples/Cpp/SimpleGame`这个自带的小游戏为例子来说明一下.
<!-- more -->
假设你的eclipse是干净的,也就是左边的Package Explorer栏是空空的


1. 导入libcocos2d-x到eclipse,右击Package Explorer空白区域,Import->Existing Android Code Into Workspace,然后在下一个窗口的Root Directory那里定位到`cocos2d-2.1rc0-x-2.1.3/cocos2dx/platform/android/java`这个目录,选中libcocos2dx,导入
2. 按照上面的方法导入`cocos2d-2.1rc0-x-2.1.3/samples/Cpp/SimpleGame/proj.android`
3. 现在eclipse里已经有了两个项目:libcocos2dx,SimpleGame

##设置使用ndk-build来编译

__________________________________________
因为我们不使用build-native.sh来编译,所以要先把资源文件得到在android项目的assets下,具体就是把`cocos2d-2.1rc0-x-2.1.3/samples/Cpp/SimpleGame/Resources`下面的所有文件复制到`cocos2d-2.1rc0-x-2.1.3/samples/Cpp/SimpleGame/proj.android/assets`下面去

__________________________________________
准备工作完毕

1. 在eclipse的Package Explorer里右击SimpleGame项目,选"Properties",打开项目属性框
2. 按下面设置Tool Chain Editor
{% img /images/ndk_debug/set_1.png %}
3. 再设置ndk-build的命令,为 `ndk-build NDK_DEBUG=1`,确定,关闭对话框
{% img /images/ndk_debug/set_2.png %}
4. 这个时候,你选择Project->Build All,会出现错误,意思是NDK_MODULE_PATH设置不对
{% img /images/ndk_debug/error_1.png %}
5. 这里我们不设置NDK_MODULE_PATH,因为设置了它会让我们无法调试C++代码,解决上面问题的办法是:把编译过程中需要的库拷贝到系统默认的NDK_MODULE_PATH里,跟我来做
6. 复制cocos2d-2.1rc0-x-2.1.3目录下的`cocos2dx,CocosDenshion,extensions,external`这4个目录到你的NDK的sources目录下,以我的电脑为例,就是/android/android-ndk-r8e/sources
7. 复制cocos2d-2.1rc0-x-2.1.3/cocos2dx/platform/third_party/android/prebuilt目录的所有到NDK的sources目录下
8. 这样我们复制到NDK的sources目录下的目录一共有`cocos2dx,CocosDenshion,extensions,external, libcurl,libjpeg,libpng,libtiff,libwebp`这几个
9. 回到eclipse,再次Project->Build All,应该会编译成功

##修改项目文件结构,以使cdt可以识别我们的C++文件
为了可以给C++下断点,我们必须修改一下当前的项目文件结构

1. 移动cocos2d-2.1rc0-x-2.1.3/samples/Cpp/SimpleGame/Classes目录到cocos2d-2.1rc0-x-2.1.3/samples/Cpp/SimpleGame/proj.android/jni下面
2. 修改cocos2d-2.1rc0-x-2.1.3/samples/Cpp/SimpleGame/proj.android/jni下面的Android.mk,把原来的

Android.mk中:

	LOCAL_SRC_FILES := hellocpp/main.cpp \
				../../Classes/AppDelegate.cpp \
               ../../Classes/HelloWorldScene.cpp \
	   			../../Classes/GameOverScene.cpp	                      
	LOCAL_C_INCLUDES := $(LOCAL_PATH)/../../Classes	

修改为

	LOCAL_SRC_FILES := hellocpp/main.cpp \
                   Classes/AppDelegate.cpp \
                   Classes/HelloWorldScene.cpp \
		   			Classes/GameOverScene.cpp
                   
	LOCAL_C_INCLUDES := $(LOCAL_PATH)/Classes    
	
也就是使文件指向正确的位置

Project->Build ALL 编译项目,应该可以成功编译


##调试

1. 我们给Classes目录下的HelloWorldScene.cpp文件里的ccTouchesEnd方法下一个断点,这样游戏运行后,点击屏幕应该可以触发我们的断点
{% img /images/ndk_debug/addbreakpoint.png %}
2. 把手机连接到电脑上,右击SimpleGame, 选 Debug As->Android Native Application
3. 如果需要选择手机,选你想调试的
4. 游戏运行起来后,触摸屏幕,可以看到

{% img /images/ndk_debug/breakpoint.png %}

eclipse自动切换到了Debug界面,而且成功的断点下来了,并且变量可以在右边窗口查看
Debug界面上的按钮就不再多说了,就是继续执行,单步执行什么的几个,摸索一下便知

##Enjoy!

[原创文章,转载请注明]
<br/><br/><br/>

PS:
上述操作中我们复制了cocos2dx的好多文件到NDK的目录下面去,显的有点繁琐,这样做的目录是为了避开去设置NDK_MODULE_PATH,如果一旦设置了NDK_MODULE_PATH,就会使的调试不可行,可能有更好的方法,如果你知道,或者有什么问题都可以直接在文章下面留言