---
layout: post
title: "使用Clover 修正被黑苹果识别为 HD 6xxx 的 HD 6450显卡"
date: 2013-05-19 21:27
comments: true
categories: [hackintosh, Clover]
---

AMD HD6450 显卡在黑苹果（OS X 10.7 和 10.8）下是免驱动,不需要任何设置直接就可以使用的，唯一的不足就是系统没有正确识别显卡型号，显卡为 "`AMD Radeon HD 6xxx`"，平时是不影响使用的  
造成这个问题的原因是苹果的显卡驱动`ATI6000Controller.kext`中的FrameBuffer配置和显卡不合适，以往的解决方法是对`ATI6000Controller.kext`打补丁，但是往往系统升级后就会失败，还得重新打补丁，这里我用[Clover]作系统的引导器，它可以在系统启动的过程中动态的修改`ATI6000Controller.kext`，这样只要把对FrameBuffer的修改写到[Clover]的配置文件里,再也不怕系统升级更新驱动文件了
<!-- more -->
详细操作过程可以参考:  
<http://www.applelife.ru/threads/%D0%97%D0%B0%D0%B2%D0%BE%D0%B4-ati-hd-6xxx-5xxx-4xxx.28890/> <br/>
<http://rampagedev.wordpress.com/kext-editing/editing-atiamd-framebuffer-personality/>

 
经测试,我的HD6450可以使用两个FrameBuffer正常工作,实际使用中我使用了配置1

1.FrameBuffer: **Pithecia**  VideoPorts: **2**

	原始数据: 
	
		00040000040300000001000021030204
		04000000140200000001000000000403
		
	修正数据: 
	
		00080000040300000001000021030202
		04000000140200000001000000000404
	
2.FrameBuffer: **Ipomoea**  VideoPorts: **3**
	
	原始数据: 
	
		00040000040300000001000012040105
		00080000040200000001000011020403
		10000000100000000001000000000002
		
	修正数据: 
	
		00080000040300000001000012040102
		04000000040200000001000011020404
		10000000100000000001000000000001
		

[Clover]: http://sourceforge.net/projects/cloverefiboot/