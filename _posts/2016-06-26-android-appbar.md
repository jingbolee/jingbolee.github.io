---
layout:     post
title:      "AppBar 的学习和使用"
subtitle:   "Annotation"
date:       2016-05-11 
author:     "Jerome"
header-img: "img/post-bg-06.jpg"
---


## Android 5.0 及以上版本不同控件的认识

![](http://i.imgur.com/5bWz3sN.jpg)


## 设置 StatusBar ， AppBar ， NavigationBar 颜色统一

1. 主题设置
	- 因为使用 Toolbar ，所以需要把 ActionBar 等隐藏，设置使用 style 的属性

			<item name="windowActionBar">false</item>
	 		<item name="android:windowNoTitle">true</item>

1. 在 style 中设置 StatusBar 的颜色
	
		<item name="colorPrimaryDark">@color/colorPrimaryDark</item>
	
2. 在 style 中设置 AppBar 的颜色

		<item name="colorPrimary">@color/colorPrimary</item>
3. 在 style 中设置 NavigationBar 的颜色
	- 只能在 Android 5.0 以后的版本上设置，需要到 value-v21 中的 style 设置

			<item name="android:navigationBarColor">@color/colorPrimary</item>

4. 设置 ToolBar 的 background

		  <android.support.v7.widget.Toolbar
		      android:id="@+id/toolbar"
		      android:layout_width="match_parent"
		      android:layout_height="wrap_content"
		      android:background="?attr/colorPrimary"
		      android:minHeight="?attr/actionBarSize"
		      android:theme="@style/ThemeOverlay.AppCompat.ActionBar"
		      app:popupTheme="@style/ThemeOverlay.AppCompat.Light"
	      />

通过以上步骤就可以设置颜色保持统一。

## 设置 ToolBar 的属性
- 首先在代码中获取的 ToolBar 的对象。

  		Toolbar toolbar = (Toolbar) findViewById(R.id.toolbar); 
- 设置 Logo

		toolbar.setLogo(R.drawable.ic_launcher);
- 设置 Title

		toolbar.setTitle("AppBarDemo");
- 设置 SubTitle

		toolbar.setSubtitle("副标题");
- 设置 NavigationIcon

		toolbar.setNavigationIcon(R.drawable.desktop_mac_black_24dp);

## 设置 Appbar 的 menu 及动作

1. menu，需要先在 res/menu/menu_main.xml 定义：

		<?xml version="1.0" encoding="utf-8"?>
		<menu xmlns:android="http://schemas.android.com/apk/res/android"
		    xmlns:app="http://schemas.android.com/apk/res-auto">
		  <item android:id="@+id/action_add"
		    android:title="Add"
		    android:orderInCategory="80"
		    android:icon="@drawable/add_box_white_24dp"
		      app:showAsAction="ifRoom"></item>
		
		  <item android:id="@+id/action_archive"
		      android:title="Archive"
		      android:orderInCategory="90"
		      android:icon="@drawable/archive_white_24dp"
		      app:showAsAction="ifRoom"></item>
		
		  <item android:id="@+id/action_sort"
		      android:title="Sort"
		      android:orderInCategory="100"
		      android:icon="@drawable/sort_white_24dp"
		      app:showAsAction="never"></item>
		</menu>

2. 重写 activity 的 onCreateOptionsMenu() 方法，加载 menu 布局并返回为 true。想要显示 menu 必须返回 true , 返回 false 的话，menu 不会显示出来。

		  @Override public boolean onCreateOptionsMenu(Menu menu) {
		    getMenuInflater().inflate(R.menu.menu_main,menu);
		    return true;
		  }

3. 重写 activity 的 onOptionsItemSelected() 方法，对 itemId 做判断，并返回为 true 表示消耗掉这些事件。

		  @Override public boolean onOptionsItemSelected(MenuItem item) {
		    switch (item.getItemId()) {
		      case R.id.action_add:
		        Toast.makeText(MainActivity.this, "Add", Toast.LENGTH_SHORT).show();
		        return true;
		      case R.id.action_archive:
		        Toast.makeText(MainActivity.this, "Archive", Toast.LENGTH_SHORT).show();
		        return true;
		      case R.id.action_sort:
		        Toast.makeText(MainActivity.this, "Sort", Toast.LENGTH_SHORT).show();
		        return true;
		      default:
		        return  super.onOptionsItemSelected(item);
		    }
		  }



以上步骤可以设置 AppBar 的 menu 和 menu 的动作。

## 增加返回上一级 Activity 的动作

1. 需要在 AndroidManifest 中当前 activity 的节点中设置 parentActivityName 属性

	    <activity
	        android:name=".SecondActivity"
	        android:parentActivityName=".MainActivity">
	      <!--兼容4.0版本及更低版本-->
	      <meta-data
	          android:name="android.support.PARENT_ACTIVITY"
	          android:value=".MainActivity"/>
	    </activity>

2. 在代码中设置 setDisplayHomeAsUpEnabled() 为 true
	
		Toolbar toolbar = (Toolbar) findViewById(R.id.toolbar);
	    setSupportActionBar(toolbar);
	    ActionBar actionBar = getSupportActionBar();
	    actionBar.setDisplayHomeAsUpEnabled(true);

## 兼容性

- Android 5.0 提出来了 AppBar ,同时由 ToolBar 来替换 Android 3.0 提出来的 ActionBar ，所以涉及到兼容 Android 5.0 以下版本的问题，不同的版本采用不同的库，如下：
	- 兼容 Android 5.0 以下版本：android.support.v7.widget.Toolbar 
	- Android 5.0 版本及以上：android.widget.Toolbar


## 透明化系统状态栏的设置

### 设置 StatusBar 透明，然后给 ToolBar 设置一个 padding，就可以让 StatusBar 与 ToolBar 保持颜色一致。

1. 设置 StatusBar 为透明
	1. StatusBar 在不同版本上的状态
	    - 在 4.4 版本之前 Android 是不可以自定义状态栏的。
		- 在 4.4 版本 Android 推出了一个透明状态栏的概念，使手机最顶部的状态栏的颜色全透明，并且颜色可以定义。
		- 5.0 推出了 Material Design，这个时候的状态栏就变成了半透明的颜色。

	- 主题设置，在 value 目录下的 style 中设置：
	
			<style name="AppTheme.Base" parent="Theme.AppCompat">
			    <item name="android:windowTranslucentStatus" tools:targetApi="kitkat">true</item>		
			</style>

		或者是在 value-19 下的 style 中设置  

			<?xml version="1.0" encoding="utf-8"?>
			<resources>
			  <style name="Apptheme" parent="AppTheme.Base">
			    <item name="android:windowTranslucentStatus">true</item>
			  </style>
			</resources>
	
	- 代码设置：
	
			if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.KITKAT) {
	            WindowManager.LayoutParams localLayoutParams = getWindow().getAttributes();
	            localLayoutParams.flags = (WindowManager.LayoutParams.FLAG_TRANSLUCENT_STATUS | localLayoutParams.flags);
	        }

	> 注意：通过 XML 设置 StatusBar 透明在某些国产手机的 ROM 上不起作用，所以建议在代码中设置。

2. 给 ToolBar 设置 padding

		  <android.support.v7.widget.Toolbar
		      android:id="@+id/toolBar"
		      android:layout_width="match_parent"
		      android:layout_height="wrap_content"
		      android:background="?attr/colorPrimary"
		      android:paddingTop="@dimen/toolbar_padding_top"
		      android:minHeight="?attr/actionBarSize"
		      app:popupTheme="@style/ThemeOverlay.AppCompat.Light"
		      app:theme="@style/ThemeOverlay.AppCompat.Dark.ActionBar"
	      />
	- android:paddingTop 的大小的确定：
	    - 在 4.4 版本之前 Android 是不可以自定义状态栏的。
		- 在 4.4 版本 Android 推出了一个透明状态栏的概念，使手机最顶部的状态栏的颜色全透明，并且颜色可以定义。所以 **4.4 版本：android:paddingTop 为 0dp**
		- 5.0 推出了 Material Design，这个时候的状态栏就变成了半透明的颜色。通过看源码得知 **5.0 以上版本：android:paddingTop 为 25dp**
	- android:paddingTop 的设置：
		- values-> dimens，toolbar_padding_top 变量值为 0dp
		- values-v19-> dimens，toolbar_padding_top 变量值为 25dp


###  设置 StatusBar 透明，然后给 ToolBar 添加一个属性 android:fitsSystemWindows 为 true 

1. 设置 StatusBar 为透明，请查看上面的设置方法
2. ToolBar 设置 android:fitsSystemWindows 为 true，切记是放在 ToolBar 的节点中设置的。否则 StatusBar 会变成与 content 颜色一致。

		  <android.support.v7.widget.Toolbar
		      android:id="@+id/toolbar"
		      android:layout_width="match_parent"
		      android:layout_height="wrap_content"
		      android:minHeight="?attr/actionBarSize"
		      android:background="@color/primaryOrange"
		      android:fitsSystemWindows="true"
		      android:theme="@style/ThemeOverlay.AppCompat.ActionBar"
		      app:popupTheme="@style/ThemeOverlay.AppCompat.Light"
	      />
	> 注意：当 android:layout_height 直接设置为 ?attr/actionBarSize ， 会造成 StatusBar 覆盖了 ToolBar 。所以需要先设置 layout_height ，然后再设置 minHeight


> 参考：
> 
> https://developer.android.com/training/appbar/index.html  
> https://developer.android.com/reference/android/support/v7/widget/Toolbar.html  
> https://developer.android.com/training/system-ui/status.html  
> http://www.jcodecraeer.com/a/anzhuokaifa/androidkaifa/2014/1118/2006.html  
> http://stormzhang.com/android/2015/08/16/boohee-toolbar/  
