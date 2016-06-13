---
layout:     post
title:      "WindowLeaked产生原因和解决办法"
subtitle:   "Dialog、Activity"
date:       2016-06-13
author:     "Jerome"
header-img: "img/post-bg-06.jpg"
---


## 问题

- 在使用MaterialDialog的时候，横竖屏切换，造成WindowLeaked的问题，虽然不影响使用，但却是一个隐患，需要解决掉。Log如下：

	E/WindowManager: android.view.WindowLeaked: Activity tech.qiji.android.apps.xianyu.ui.activity.MainActivity has leaked window com.android.internal.policy.impl.PhoneWindow$DecorView{2a70ed6f V.E..... R....... 0,0-1025,1090} that was originally added here

## 分析

- Android的每一个Activity都有个WindowManager，因此，构建在Activity之上的dialog、PopupWindow也有相应的WindowManager窗体管理器。因为dialog、PopupWindown不能脱离Activity而单独存在着，所以当Dialog或者PopupWindow正在显示的时候去finish()了承载该Dialog(或PopupWindow)的Activity时，就会抛Window Leaked异常了，因为这个Dialog(或PopupWindow)的WindowManager已经没有谁可以附属了，所以它的窗体管理器已经泄漏了。

## 解决办法

- finish() Activity前，对附属在上面的dialog执行dismiss()操作




 