---
layout:     post
title:      "Android 6.0 Permission"
subtitle:   "Android 6.0 运行时权限"
date:       2016-07-19 
author:     "Jerome"
header-img: "img/post-bg-06.jpg"
---

## Android Perimission

### 运行时权限分析

- Android 6.0 开始，引入了运行时权限，Requesting Permissions at Run Time。  
- 从 Android 6.0 开始，权限分为普通权限 normal 和危险权限 dangerous
- 普通权限 normal 和危险权限 dangerous 都需要在清单文件中注册。
- 普通权限的话，可以直接使用。
- 危险权限，在 Android 6.0 及以上设备，在使用危险权限时，需要检查是否有该权限，有权限可以直接使用，没有权限需要申请权限。假如使用到该权限的时候，不进行权限的检查，crash,然后 log 中提示 Permission Denied

### 权限分类

#### 普通权限 normal

- ACCESS_LOCATION_EXTRA_COMMANDS
- ACCESS_NETWORK_STATE
- ACCESS_NOTIFICATION_POLICY
- ACCESS_WIFI_STATE
- BLUETOOTH
- BLUETOOTH_ADMIN
- BROADCAST_STICKY
- CHANGE_NETWORK_STATE
- CHANGE_WIFI_MULTICAST_STATE
- CHANGE_WIFI_STATE
- DISABLE_KEYGUARD
- EXPAND_STATUS_BAR
- GET_PACKAGE_SIZE
- INSTALL_SHORTCUT
- INTERNET
- KILL_BACKGROUND_PROCESSES
- MODIFY_AUDIO_SETTINGS
- NFC
- READ_SYNC_SETTINGS
- READ_SYNC_STATS
- RECEIVE_BOOT_COMPLETED
- REORDER_TASKS
- REQUEST_IGNORE_BATTERY_OPTIMIZATIONS
- REQUEST_INSTALL_PACKAGES
- SET_ALARM
- SET_TIME_ZONE
- SET_WALLPAPER
- SET_WALLPAPER_HINTS
- TRANSMIT_IR
- UNINSTALL_SHORTCUT
- USE_FINGERPRINT
- VIBRATE
- WAKE_LOCK
- WRITE_SYNC_SETTINGS

#### 危险权限 dangerous


		| Permission Group       | Permissions    |
		| -------------|:------------:|
		| CALENDAR     | READ_CALENDAR     |
		|              | WRITE_CALENDAR    |
		| CAMERA       | CAMERA 			|
		| CONTACTS     | READ_CONTACTS     |
		| 		      | WRITE_CONTACTS      |
		| 		      | GET_ACCOUNTS      |
		|LOCATION     | ACCESS_FINE_LOCATION    |
		| 		      | ACCESS_COARSE_LOCATION  |
		|MICROPHONE   | RECORD_AUDIO      |
		|PHONE        | READ_PHONE_STATE  |
		| 		      | CALL_PHONE        |
		| 		      | READ_CALL_LOG      |
		| 		      | WRITE_CALL_LOG      |
		| 		      | ADD_VOICEMAIL      |
		| 		      | USE_SIP		      |
		| 		      | PROCESS_OUTGOING_CALLS   |
		| SENSORS     | BODY_SENSORS      |
		| SMS	      | SEND_SMS      |
		| 		      | RECEIVE_SMS      |
		| 		      | READ_SMS      |
		| 		      | RECEIVE_WAP_PUSH      |
		| 		      | RECEIVE_MMS      |
		| STORAGE     | READ_EXTERNAL_STORAGE      |
		| 		      | WRITE_EXTERNAL_STORAGE      |





### 权限检查示例

#### 检查权限：

需要 dangerous 权限，就需要在请求权限的时候，检查权限，调用方法：

	ContextCompat.checkSelfPermission()
	
	//  thisActivity 是当前的 activity
	int permissionCheck = ContextCompat.checkSelfPermission(thisActivity,
	        Manifest.permission.WRITE_CALENDAR);
	
假如 app 有权限的话，返回PackageManager.PERMISSION_GRANTED ，没有权限的话，返回 PERMISSION_DENIED 
	
#### 请求权限：
	
	// Here, thisActivity is the current activity
	if (ContextCompat.checkSelfPermission(thisActivity,
	                Manifest.permission.READ_CONTACTS)
	        != PackageManager.PERMISSION_GRANTED) {
	
	    // Should we show an explanation?
	    if (ActivityCompat.shouldShowRequestPermissionRationale(thisActivity,
	            Manifest.permission.READ_CONTACTS)) {
	
	        // Show an expanation to the user *asynchronously* -- don't block
	        // this thread waiting for the user's response! After the user
	        // sees the explanation, try again to request the permission.
	
	    } else {
	
	        // No explanation needed, we can request the permission.
	
	        ActivityCompat.requestPermissions(thisActivity,
	                new String[]{Manifest.permission.READ_CONTACTS},
	                MY_PERMISSIONS_REQUEST_READ_CONTACTS);
	
	        // MY_PERMISSIONS_REQUEST_READ_CONTACTS is an
	        // app-defined int constant. The callback method gets the
	        // result of the request.
	    }
	}

当你的 app 调用  requestPermissions() 方法的时候，系统会弹出一个标准对话框。

#### 权限请求回调方法 onRequestPermissionsResult()

	@Override
	public void onRequestPermissionsResult(int requestCode,
	        String permissions[], int[] grantResults) {
	    switch (requestCode) {
	        case MY_PERMISSIONS_REQUEST_READ_CONTACTS: {
	            // If request is cancelled, the result arrays are empty.
	            if (grantResults.length > 0
	                && grantResults[0] == PackageManager.PERMISSION_GRANTED) {
	
	                // permission was granted, yay! Do the
	                // contacts-related task you need to do.
	
	            } else {
	
	                // permission denied, boo! Disable the
	                // functionality that depends on this permission.
	            }
	            return;
	        }
	
	        // other 'case' lines to check for other
	        // permissions this app might request
	    }
	}  


### 备注

- 只有 requestPermissions() 方法调用以后，才会回调 onRequestPermissionsResult() 方法
- 检查权限的方法 ContextCompat.checkSelfPermission() 传入的第一个参数 可以是任何的 context ,而不必是 activity
- 请求权限的方法 ActivityCompat.requestPermissions() 传入的第一个参数，必须是 activity。因为需要在 Window 上显示权限对话框，所以必须传入 activity。




> 参考：  
Training :	 https://developer.android.com/training/permissions/requesting.html  
Permissions ： https://developer.android.com/guide/topics/security/permissions.html#normal-dangerous  
Normal Permissions ： https://developer.android.com/guide/topics/security/normal-permissions.html  
Manifest Permission :https://developer.android.com/reference/android/Manifest.permission.html
