---
layout: post
title: "update SeaBIOS on Google Chromebook Pixel"
date: 2013-06-02 14:47
comments: true
categories: [chromebook_pixel, linux]
---

Google的新笔记本[chromebook pixel][]支持Corebook启动SeaBIOS,这样就可以启动安装Windows,Linux,甚至Mac OS X操作系统  
Pixel自带的SeaBIOS版本是`SeaBIOS Version: 20121017_154325-build69-m2`,[SeaBIOS]是开源软件,我们可以升级这笔记本自带的版本

1. 设置笔记本进入开发者模式

	关机,按住ESC键和Refresh键,再按电源键开机.
	按Ctrl+D键,确认,会清空所有文件和设置,并重启到dev-mode
	警告界面,按Ctrl+D进入Chrome OS, 按Ctrl+L进入SeaBIOS,这里按Ctrl+D进入Chrome OS
	进入后,按Ctrl+Alt+T打开Shell界面,输入以下命令
	
		shell
		sudo bash
		crossystem dev_boot_usb=1 dev_boot_legacy=1
		
	经过以上设置后,在开机界面即可按下Ctrl-L键进入SeaBIOS,并引导U盘等外部存储
		
2. 升级SeaBIOS
	下载SeaBIOS For Pixel的20130425版本文件[http://www.coreboot.org/~stepan/seabios.cbfs.bz2](),解压得到seabios.cbfs,放入U盘备用
	进入Chrome OS系统, 插入保存有升级固件的U盘,按Ctrl+Alt+T打开Shell界面,升级命令如下
	
		shell
		sudo bash
		cd /media/removable/USB_NAME   #USB_NAME是U盘上分区的名称
		flashrom -r image.rom.origin   #备份原来的固件
		cp image.rom.origin image.rom  #复制一份,准备操作
		dd if=seabios.cbfs of=image.rom seek=2 bs=2M conv=notrunc   #将下载的新SeaBIOS写入固件镜像文件
		flashrom -w image.rom -i RW_LEGACY  #将固件刷入设备
		
3. 重启,按Ctrl-L,就可以看到新版本的SeaBIOS.


参考资料:  
[Chromebook Pixel](http://www.chromium.org/chromium-os/developer-information-for-chrome-os-devices/chromebook-pixel)  
[Updating SeaBIOS on a Chromebook](http://www.seabios.org/pipermail/seabios/2013-April/006136.html) 


[chromebook pixel]:http://www.google.com/intl/zh-CN/chrome/devices/chromebook-pixel/
 
