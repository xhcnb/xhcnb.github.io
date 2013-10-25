---
layout: post
title: "Set ubuntu as Mac AFP&amp;TimeMachine server with netatalk 3.05"
date: 2013-10-25 01:04
comments: true
categories: [Linux,Ubuntu,Mac,TimeMachine]
---

准备使用[netatalk][1]在Ubuntu Server上搭建支持TimeMachine备份的文件服务器,查了一下[netatalk][1]新的版本已经不需要avahi了,不过apt-get获取的netatalk还是老版本的,so,下载源码编译安装

####首先安装依赖

	sudo apt-get install libssl-dev libgcrypt11-dev libkrb5-dev \
		libpam0g-dev libwrap0-dev libdb-dev libavahi-client-dev \
		libacl1-dev libldap2-dev libcrack2-dev systemtap-sdt-dev \
		libdbus-1-dev libdbus-glib-1-dev libglib2.0-dev libevent-dev

####下载解压netatalk3.0.5源码

	wget http://softlayer-dal.dl.sourceforge.net/project/netatalk/netatalk/3.0.5/netatalk-3.0.5.tar.bz2
	tar xvf netatalk-3.0.5.tar.bz2 
	cd netatalk-3.0.5/
<!-- more -->
####配置&编译&安装netatalk
	./configure --with-init-style=debian --with-cracklib --enable-krbV-uam --without-libevent --with-pam-confdir=/etc/pam.d --with-dbus-sysconf-dir=/etc/dbus-1/system.d
	make
	sudo make install

####查看安装否正常
	afpd -V

输出应该类似于:
```bash
	afpd 3.0.5 - Apple Filing Protocol (AFP) daemon of Netatalk

	This program is free software; you can redistribute it and/or modify it under
	the terms of the GNU General Public License as published by the Free Software
	Foundation; either version 2 of the License, or (at your option) any later
	version. Please see the file COPYING for further information and details.

	afpd has been compiled with support for these features:

	          AFP versions:	2.2 3.0 3.1 3.2 3.3
	         CNID backends:	dbd last tdb
	      Zeroconf support:	Avahi
	  TCP wrappers support:	Yes
	         Quota support:	Yes
	   Admin group support:	Yes
	    Valid shell checks:	Yes
	      cracklib support:	Yes
	            EA support:	ad | sys
	           ACL support:	Yes
	          LDAP support:	Yes
	         D-Bus support:	Yes
	         DTrace probes:	Yes

	              afp.conf:	/usr/local/etc/afp.conf
	           extmap.conf:	/usr/local/etc/extmap.conf
	       state directory:	/usr/local/var/netatalk/
	    afp_signature.conf:	/usr/local/var/netatalk/afp_signature.conf
	      afp_voluuid.conf:	/usr/local/var/netatalk/afp_voluuid.conf
	       UAM search path:	/usr/local/lib/netatalk//
	  Server messages path:	/usr/local/var/netatalk/msg/
	
```

####配置TimeMachine文件夹
	sudo vim /usr/local/etc/afp.conf 
	
我准备使用/home/jason/WD目录做为共享文件夹, 所以我的afp.conf文件如下

	;
	; Netatalk 3.x configuration file
	;
	[Global]
	afpstats = yes
	;[Homes]
	;basedir regex = /home
	[WD]
	path = /home/jason/WD
	time machine = yes
	
afp.conf的配置说明可以查看官方文档[afp.conf][2].


####测试
回到mac, 到Finder里, 先Cmd+K连接服务器`afp://ip.ip.x.x`,输入用户名密码,连接成功  
再到TimeMachine设置里,选择共享的文件夹做为目标磁盘, 开始备份吧!

{% img /images/20131025_tm.png %} 

[1]: http://netatalk.sourceforge.net/
[2]: http://netatalk.sourceforge.net/3.0/htmldocs/afp.conf.5.html
