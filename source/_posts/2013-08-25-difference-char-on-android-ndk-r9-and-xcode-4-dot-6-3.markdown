---
layout: post
title: "默认类型char 在 android NDK r9 和 xcode 4.6.3中的不同"
date: 2013-08-25 10:48
comments: true
categories: [NDK,xcode]
---
测试一些代码中,发现一个奇怪的bug.最好发现是如下原因:
考虑代码:
	
``` c
	
char c_num;
int i_num;

c_num = -1;
i_num = c_num;
if (i_num == c_num) {
    CCLOG("OK 1");
}

i_num = -1;
c_num = i_num;
if (i_num == c_num) {
    CCLOG("OK 2");
}

```
  xcode ios SDk 6.1 编译得到输出 
   
  		OK 1  
  		OK 2
  	
  android NDK r9, gcc 4.8编译得到输出  
  
  		OK 1

可见,在xcode中char被定义为有符号数,而在ndk中定义是无符号数
