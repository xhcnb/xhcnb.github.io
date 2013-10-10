---
layout: post
title: "自定义xcode代码提示的风格"
date: 2013-10-10 16:24
comments: true
categories: [xcode]
---

网上有个图片:

{% img /images/twopeople.png %} 

我喜欢左边的风格, 很不幸xcode默认的代码提示自动完成都是右边风格的, 修改它一下  
用文本编辑工具打开文件:`/Applications/Xcode.app/Contents/PlugIns/IDECodeSnippetLibrary.ideplugin/Contents/Resources/SystemCodeSnippets.codesnippets`
可以看到里面是xcode预定义的自动完成代码, 找到要修改的`{`地方, 比如
	
	<string>class &lt;#class name#&gt; {
	  &lt;#instance variables#&gt;

	public:
	  &lt;#member functions#&gt;
	};</string>
	
很明显这是定义一个C++类, 在`{`后面添加一个回车修改成
	
	<string>class &lt;#class name#&gt; 
	{
	  &lt;#instance variables#&gt;

	public:
	  &lt;#member functions#&gt;
	};</string>
	
依次修改需要的地方,保存,重新启动xcode, 收工.