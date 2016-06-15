---
layout:     post
title:      "Error Installing APK歪打解决办法"
subtitle:   "Android Studio、EmptyThrowable"
date:       2016-06-13
author:     "Jerome"
header-img: "img/post-bg-06.jpg"
---


## 问题

- 在使用Android Studio运行app的时候，出现了Error Installing APK的提示，在build\outputs\apk\下有xianyu_v1.0_2016-06-13_internal.apk生成，但是却在查找一个xianyu_v1.0_2016-06-08_internal.apk。Log如下：

	EmptyThrowable: The APK file D:\qiji\android\app\build\outputs\apk\xianyu_v1.0_2016-06-08_internal.apk does not exist on disk.
	Session 'app': Error Installing APK

- 环境
	- Win7 64位旗舰版
	- Android Studio 2.1.1
	- gradle:2.1.0
## 分析

- 当前时间为2016-06-13，发现当前build的apk是正确的，但是呢，安装到手机的时候却找的是2016-06-08的apk,然后在stackoverflow上有个说删掉build文件夹，然后Rebuild Project。但是针对我的情况无效，依旧是这个错误提示。按说应该是缓存的问题造成的，但是Rebuild以后还是不可以。经过下面歪打的解决办法，估计是Win7的缓存造成的。

## 歪打的解决办法

- 重启Windows
- Sync Gradle Files
- Rebuild Project




 