---
layout: post
title: "Mac下的命令行小程序调用词典查单词"
date: 2013-06-18 19:29
comments: true
categories: mac
---
查个单词还得打开词典app或才打开浏览器到google翻译, 这对于大多时间在终端中的我们来说来回切换窗口实在是不方便, 提供一个小程序可以在命令行下查单词

使用你喜欢的编辑器创建源文件`dict.m`, 比如,我这里是  
	
	mate dict.m

在`main.m`文件里输出我们的程序代码, 看我的写的简单注释就能懂了  
``` objective-c

	#import <Foundation/Foundation.h>

	int main(int argc, const char * argv[])
	{
    	@autoreleasepool { 
    		//判断参数, 如果参数不对, 打印使用方法      
        	if(argc < 2)
        	{
            	printf("Usage: %s [word to translate]\n", argv[0]);
            	printf("示例: \n%s apple\n", argv[0]);
            	return -1;
        	}
            //获取需要查找的单词      
        	NSString * search = [NSString stringWithCString: argv[1] encoding: NSUTF8StringEncoding]; 
        	//通过CoreServices查找单词的翻译                 
        	CFStringRef def =  DCSCopyTextDefinition(NULL,(CFStringRef)search,CFRangeMake(0, [search length]));  
        	//输出结果                
        	NSString * output = [NSString stringWithFormat: @"Definition of <%@>:%@\n", search, (NSString *)def];                   
        	printf("%s", [output UTF8String]);       
    	}
    	return 0;
	}
```
所有的代码就是这些了, 其中使用了[DCSCopyTextDefinition](https://developer.apple.com/library/mac/#documentation/userexperience/Reference/DictionaryServicesRef/Reference/reference.html),可到苹果开发网站上查看详细介绍.

编译源文件`dict.m`, 我这里使用clang, 你也可以使用gcc  

	clang dict.m -o dict -framework CoreServices -framework Foundation -O2
<!-- more -->	

===========
####源码和编译好的程序打包下载, 点[这里dict.zip](/images/dict.zip)
===========

编译后,得到可执行文件`dict`, 试试效果怎么样

	./dict apple
	
可以看到输出

	Jasons-Mac:~ jason$ ./dict apple
	Definition of <apple>:apple
	/ˈæpl; `æpl/
	n 
	1 (a) round fruit with firm juicy flesh and green, red or yellow skin when ripe 苹果
	*[attrib 作定语] an apple pie 苹果馅饼
	* apple sauce 苹果酱. =>illus at fruit 见fruit之插图.
	(b) (also `apple tree) tree bearing this fruit 苹果树. 
	2 (idm 习语) an/the ,apple of `discord (fml 文) cause of an argument or a quarrel 争端; 祸根. the ,apple of sb's `eye person or 	thing that is loved more than any other 心爱的人或物; 掌上明珠
	*She is the apple of her father's eye. 她是父亲的掌上明珠. in ,apple-pie `order very neatly arranged 井然有序.
再试试可以查中文吗
	
	./dict 苹果
	
得到输出
	
	Definition of <苹果>:苹果
	píngguǒ
	名
	落叶乔木，叶子椭圆形。花白色带有红晕。果实也叫苹果，圆形，味甜，是常见水果。
	
Very good!, 为了方便把工具复制到系统bin目录, 这样就可以在任意终端窗口,任意目录下查单词了

	#复制dict到系统/usr/bin目录下
	sudo mv ./dict /usr/bin/
	
好了,现在可以在终端里使用dict命令查单词了.