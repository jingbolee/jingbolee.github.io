---
layout:     post
title:      "Android 自定义属性的使用"
subtitle:   "attrs.xml"
date:       2016-05-11 
author:     "Jerome"
header-img: "img/post-bg-06.jpg"
---


- 使用：在自定义控件的时候，自定义属性，可以通过在values文件夹下创建一个attrs.xml文件来实现。
   - 如：设置了两个属性loop和loopovertime,类型分别为boolean和integer。

			<?xml version="1.0" encoding="utf-8"?>
			<resources>
		    <declare-styleable name="Lbanner">
		        <attr name="loop" format="boolean"></attr>
		        <attr name="loopovertime" format="integer"></attr>
		    </declare-styleable>
			</resources>

- 在布局文件中使用：
  - 如：布局文件中引用了该自定义控件，并通过自定义属性设置参数
  
		     <com.views.Lbanner
		        android:id="@+id/lb_simple"
		        android:layout_width="match_parent"
		        android:layout_height="wrap_content"
		        app:loop="true"
		        app:loopovertime="2000"></com.views.Lbanner>

- 在代码中获取属性：
   - 代码中获取布局文件中定义的属性参数。
      - context.obtainStyledAttributes(attrs, R.styleable.Lbanner)获取到TypedArray，R.styleable.Lbanner为自定义的属性文件名。
      - typedArray.getBoolean(R.styleable.Lbanner_loop,false)获取到loop的属性，需要一个默认值。R.styleable.Lbanner_loop为通过自定义属性文件名中的属性名称获取赋予的值。
   
			    public Lbanner(Context context, AttributeSet attrs) {
			        super(context, attrs);
			        TypedArray typedArray = context.obtainStyledAttributes(attrs, R.styleable.Lbanner);
			        mLoop = typedArray.getBoolean(R.styleable.Lbanner_loop,false);
			        mLoopovertime = typedArray.getInteger(R.styleable.Lbanner_loopovertime,5000);
			    }

- 源码：RelativeLayout源码
  

		    private void initFromAttributes(
		            Context context, AttributeSet attrs, int defStyleAttr, int defStyleRes) {
		        final TypedArray a = context.obtainStyledAttributes(
		                attrs, R.styleable.RelativeLayout, defStyleAttr, defStyleRes);
		        mIgnoreGravity = a.getResourceId(R.styleable.RelativeLayout_ignoreGravity, View.NO_ID);
		        mGravity = a.getInt(R.styleable.RelativeLayout_gravity, mGravity);
		        a.recycle();
		    }