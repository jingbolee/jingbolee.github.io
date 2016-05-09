---
layout:     post
title:      "Android 中的那些 Manager 们"
subtitle:   "各种各样的Manager"
date:       2016-05-09 
author:     "Jerome"
header-img: "img/post-bg-06.jpg"
---

* **AssetManager** 提供一个对app原始资源的访问  
获取实例：`getAssets()`

* **PackageManager** 用于提供设备上已安装应用程序相关的各种信息，该对象主要用于获取设备上已安装应用的所有信息，比如包名，清单文件，路径等信息。  
获取实例：`getPackageManager()`

* **AlarmManager** 全局定时器，可用于设置闹钟，重复任务等  
获取实例：`(AlarmManager) getSystemService(ALARM_SERVICE)`  
设置一个10秒以后的定时任务 
 
        long triggerAtTime = SystemClock.elapsedRealtime()+10*1000;
        manager.set(AlarmManager.ELAPSED_REALTIME_WAKEUP,triggerAtTime,pendingIntent);

* **LocationManager**，可以对手机进行定位，获取到经纬度，海拔等参数  
获取实例：`(LocationManager)getSystemService(Content.LOCATION_SERVICE);`  
需要一个位置提供器来确定设备当前的位置  
Android提供三个可选：
  - GPS_PROVIDER
  - NETWORK_PROVIDER
  - PASSIVE_PROVIDER
`Location location=locationManager.getLastKnowLocation(LocationManager.GPS_PROVIDER)`  

* **TelephonyManager**，使用获取到的对象，可以得到手机的各种信息，包括sim信息  
获取实例：`(TelephonyManager) getSystemService(TELEPHONY_SERVICE)`


* **DevicePolicyManager**获取到该对象以后，可以对手机进行wipedata, lockscreen, 设置密码， 限制密码长度等，请查看API guide中的Administration介绍  
获取实例：`(DevicePolicyManager)getSystemService(DEVICE_POLICY_SERVICE)`

* **Vibrator**获取到该对象后，可以对手机震动进行设置。可以设置时长，也可以设置震动规律；  
获取实例：`(Vibrator) getSystemService(VIBRATOR_SERVICE)`


* **ActivityManager**该对象主要用于提供系统中所有活动的状态，比如进程信息，当前操作的应用，后台运行的进程等。  
获取实例：`(ActivityManager)context.getSystemService(Context.ACTIVITY_SERVICE)`



* **PowerManager**电源管理的对象,该对象主要用于提供设备电源状态的控制
获取实例：`(PowerManager) context.getSystemService(Context.POWER_SERVICE)`  
**注意：使用中，尽可能用最小的权限，同时使用完成以后尽快释放**

* **ConnectivityManager**网络连接的对象,该对象主要用于监控网络连接状态（WIFI，GPRS，3G等信息）    
获取实例：`(ConnectivityManager) context.getSystemService(Context.CONNECTIVITY_SERVICE)`

* **LoaderManager**  
[官网](http://developer.android.com/intl/zh-cn/reference/android/app/LoaderManager.html)