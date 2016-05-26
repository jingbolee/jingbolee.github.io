---
layout:     post
title:      "Android布局RelativeLayout和LinearLayout常用属性"
subtitle:   "RelativeLayout LinearLayout"
date:       2016-05-26 
author:     "Jerome"
header-img: "img/post-bg-06.jpg"
---


# 在Android的布局中，常用的布局有RelativeLayout(相对布局)和LinearLayout(线性布局)

## RelativeLayout-相对布局

>通过设置View之间的相对位置，调整布局

### 第一类:属性值为true或false

- android:layout_centerHrizontal 水平居中
- android:layout_centerVertical 垂直居中
- android:layout_centerInparent 相对于父元素完全居中


- android:layout_alignParentBottom 紧贴父元素的下边缘
- android:layout_alignParentLeft 紧贴父元素的左边缘
- android:layout_alignParentRight 紧贴父元素的右边缘
- android:layout_alignParentTop 紧贴父元素的上边缘　
- android:layout_alignWithParentIfMissing 如果对应的兄弟元素找不到的话就以父元素做参照物
>API17加入
>
- android:layout_alignParentStart 子控件和父控件开始对齐
- android:layout_alignParentEnd 子控件和父控件结束对齐 　



### 第二类：属性值必须为id的引用名“@id/id-name”

- android:layout_below 在某元素的下方
- android:layout_above 在某元素的的上方
- android:layout_toStartOf 在某个元素开始的地方，紧挨着父布局，类似layout_toLeftOf
- android:layout_toEndOf 在某个元素结束的地方，紧挨着父布局，类似layout_toRightOf
- android:layout_toLeftOf 在某元素的左边，紧挨着父布局
- android:layout_toRightOf 在某元素的右边，紧挨着父布局


- android:layout_alignTop 本元素的上边缘和某元素的的上边缘对齐
- android:layout_alignBottom 本元素的下边缘和某元素的的下边缘对齐
- android:layout_alignLeft 本元素的左边缘和某元素的的左边缘对齐
- android:layout_alignRight 本元素的右边缘和某元素的的右边缘对齐
- android:layout_alignBaseline 以某元素为基准线对齐
>API17加入
>
- android:layout_alignStart 两个控件开始对齐
- android:layout_alignEnd 两个控件结束对齐

### 第三类：属性值为具体的像素值，如30dip，40px
- android:layout_marginBottom 离某元素底边缘的距离
- android:layout_marginLeft 离某元素左边缘的距离
- android:layout_marginRight 离某元素右边缘的距离
- android:layout_marginTop 离某元素上边缘的距离
- android:padding: 内边框，会撑大View。


## LinearLayout-线性布局

>通过属性orientation设置为vertical竖着或者horizontal横着来调整布局

- android:orientation：设置布局走向
- android:gravity：属性是对该view中内容的限定 
- android:layout_gravity：是用来设置该view相对与父view 的位置
- android:layout_weight：权重，权值越大的，重要度则越小
- android:baselineAligned： 子元素底部对齐



## 注意

- padding和layout_margin区别：
   - padding：内边框，把view撑开一定的dp，可以扩大该view的大小
   - layout_margin:外边框，view距离其他view的距离，该view大小不变

- LinearLayout和RelativeLayout居中处理：
	- RelativeLayout：
		- android:layout_centerHrizontal 水平居中
		- android:layout_centerVertical 垂直居中
		- android:layout_centerInparent 相对于父元素完全居中
		- android:gravity对RelativeLayout不起作用
	- LinearLayout：
		- android:gravity： 属性是对该view中内容的限定 
		- android:layout_gravity： 是用来设置该view相对与父view 的位置

- android:gravity和android:layout_gravity
	- android:gravity： 属性是对该view中内容的限定 
	- android:layout_gravity： 是用来设置该view相对与父view 的位置