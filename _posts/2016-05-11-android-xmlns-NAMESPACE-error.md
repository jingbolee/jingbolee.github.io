---
layout:     post
title:      "Android Studio 自定义属性 xmlns 无法识别的解决办法"
subtitle:   "自定义属性xmlns"
date:       2016-05-11 
author:     "Jerome"
header-img: "img/post-bg-06.jpg"
---

##问题：在自定义控件的时候，自定义属性，在使用该控件的时候，使用自定义属性xmlns的时候提示错误
##错误：

	In Gradle projects, always use http://schemas.android.com/apk/res-auto for custom attributes less... (Ctrl+F1) 
	In Gradle projects, the actual package used in the final APK can vary; for example,you can add a .debug package suffix in one version and not the other. Therefore, you should not hardcode the application package in the resource; instead, use the special namespace http://schemas.android.com/apk/res-auto which will cause the tools to figure out the right namespace for the resource regardless of the actual package used during the build.

##解决办法：
- 安装提示信息把自定义属性改为 http://schemas.android.com/apk/res-auto