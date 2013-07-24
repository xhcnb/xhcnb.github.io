---
layout: post
title: "Ubuntu 13.04 不能保存屏幕亮度的补丁"
date: 2013-07-24 15:07
comments: true
categories: [python,ubuntu]
---

安装Ubuntu后,再安装[屏幕亮度指示器(Indicator Brightness)](https://launchpad.net/~indicator-brightness/+archive/ppa), 发现每次重启后亮度都会恢复到最大,无法记忆上次设置的屏幕亮度.经查看,这个指示器是python写的,脚本路径是`/opt/extras.ubuntu.com/indicator-brightness/indicator-brightness`, 决定在修改脚本,增加自动保存亮度的代码.

使用任何文件编辑器打开它, 在开头添加如下两个方法来保存和加载亮度数据

``` python
import os
import os.path
filepath = os.path.expanduser('~')+"/.brightness"
def save_to_file(rec):	
	try:
		savefile = open(filepath, "w")
		savefile.write("%s" % rec)
		savefile.close()
	except:
		savefile = ""

def read_from_file():
	lastBrightness = 0
	try:
		savefile = open(filepath, "r")
		lastBrightness = int(savefile.read())
		savefile.close()
	except:
		lastBrightness = 0
	return lastBrightness			
```
这里,我把当前亮度保存`$HOME/.brightness`文件里.
再添加对这两个方法的调用
在`if __name__ == "__main__":`后面添加
``` python
rec = read_from_file()
if rec != 0:
    set_brightness(rec)			
```
读取保存的亮度记录,并设置

同时修改`set_brightness`方法为:
``` python
def set_brightness(brightness):
  subprocess.call(['pkexec','/usr/lib/gnome-settings-daemon/gsd-backlight-helper','--set-brightness',"%s" % brightness])
  save_to_file(brightness)
  create_menu(ind)	  
```

保存`indicator-brightness`文件,并修复权限

	sudo chown root:root indicator-brightness
	sudo chmod 755 indicator-brightness

注销,重新登录,生效

附件为我修改的`indicator-brightness`文件, 这里[下载Download](/images/indicator-brightness)
