---
layout:     post
title:      "Android 动画"
subtitle:   "Animation Animator"
date:       2016-07-12 
author:     "Jerome"
header-img: "img/post-bg-06.jpg"
---

## Android 动画

### Android 动画分类
Android 动画分为2类，在 View 动画中，又分为补间动画和帧动画

- View 动画：View Animation
	- 补间动画：Tween Animation
	- 帧动画：Frame Animation

- 属性动画：Property Animation

### View Animation

#### Tween Animation 补间动画
- 分类
	- TranslateAnimation：平移动画
	- RotateAnimation：旋转动画
	- ScaleAnimation：缩放动画
	- AlphaAnimation：透明动画
	- AnimationSet：动画集合

- 原理：对 View 的影像做处理，不会对 View 本身做出影响，比如Button，点击移动后的位置，并不会响应点击事件，可以响应点击事件的位置依旧是原来的位置。
- 缺点：只能针对 View 的影响做处理，当动画完成以后，无法进行交互处理。
- XML路径：res/anim
- XML定义


	| 动画        | XML标签    | 效果 |
	| -------------|:------------:| ------: |
	| TranslateAnimation   | \<translate>     | 平移 |
	| RotateAnimation| \<rotate> |  旋转 |
	| ScaleAnimation     | \<scale>	      | 缩放|
	| AlphaAnimation      | \<alpha>       |  透明 |
| AnimationSet      | \<set>       |  动画集合 |


- interpolator 和 shareInterpolator
	- interpolator：表示动画集合采用的插值器，插值器影响动画的速度，比如非匀速动画就需要通过插值器来控制动画的播放过程。这个属性可以不指定，默认为@android:anim/accelerate_decelerate_interpolator
	- shareInterpolator：表示集合中的动画是否和集合共享同一个插值器。


- 代码实现：
	- TransalteAnimation

			TranslateAnimation animation = new TranslateAnimation(100f,0,0,0);
	        animation.setDuration(300);
	        mButton.startAnimation(animation);
	- RotateAnimation

	        RotateAnimation animation = new RotateAnimation(180,0,Animation.RELATIVE_TO_SELF,-360,0,Animation.RELATIVE_TO_SELF);
	        animation.setDuration(300);
	        mButton.startAnimation(animation);
	- ScaleAnimation

	        ScaleAnimation animation = new ScaleAnimation(180,0,Animation.RELATIVE_TO_SELF,-360,0,Animation.RELATIVE_TO_SELF);
	        animation.setDuration(300);
	        mButton.startAnimation(animation);
	- AlphaAnimation

			AlphaAnimation animation = new AlphaAnimation(0, 0.7f);
	        animation.setDuration(300);
	        mButton.startAnimation(animation);
	- AnimationSet
	
	        AnimationSet animationSet = new AnimationSet(false);
	        ScaleAnimation scaleAnimation = new ScaleAnimation(180,0,Animation.RELATIVE_TO_SELF,-360,0,Animation.RELATIVE_TO_SELF);
	        scaleAnimation.setDuration(300);
	        RotateAnimation
	            rotateAnimation = new RotateAnimation(180,0,Animation.RELATIVE_TO_SELF,-360,0,Animation.RELATIVE_TO_SELF);
	        rotateAnimation.setDuration(300);
	        animationSet.addAnimation(scaleAnimation);
	        animationSet.addAnimation(rotateAnimation);
	        mButton.startAnimation(animationSet);

- 通过 Animation 的 setAnimationListener 方法可以给 View 动画添加过程监听。

        AlphaAnimation animation = new AlphaAnimation(0,0.7f);
        animation.setAnimationListener(new Animation.AnimationListener() {
          @Override public void onAnimationStart(Animation animation) {
          }  //动画开始
          @Override public void onAnimationEnd(Animation animation) {
          }  //动画结束
          @Override public void onAnimationRepeat(Animation animation) {
          }  //动画重复
        });

- 经验：
	- 每次写动画的XML的时候，都得比划半天动画怎么进入和退出，最后总结：平移动画，以 View 的左边和上边为参数，做判断。
	- 手机屏幕的坐标系为X轴的正为正，Y轴的负为正，就是在坐标系的第四象限，但是Y轴正负会颠倒，注意判断。

- LayoutAnimation
	- 作用：给 ViewGroup 指定一个动画，这样当它的子元素出场时都会具有这种动画效果，比如给 ListView 或者 RecyclerView
	- 使用：
		1. 定义 LayoutAnimation，路径：res/anim/anim_layout.xml，如下：
				
				<?xml version="1.0" encoding="utf-8"?>
				<layoutAnimation xmlns:android="http://schemas.android.com/apk/res/android"
				    android:delay="0.5"
				    android:animationOrder="normal"
				    android:animation="@anim/main_enter_anim"
				/>
		2. 为子元素指定具体的入场动画，路径：res/anim/main_enter_anim.xml,如下：

				<?xml version="1.0" encoding="utf-8"?>
				<set xmlns:android="http://schemas.android.com/apk/res/android">
				
				  <translate
				      android:duration="300"
				      android:fromXDelta="100%"
				      android:toXDelta="0%"
				      ></translate>
				</set>
		3. 为 ViewGroup 指定 android:layoutAnimation属性为： android:layoutAnimation="@anim/anim_layout"，这样 ListView 或者 RecyclerView 的 item 就具有了出场动画。
- Activity 或者 Fragment 切换动画
	- Activity:
	
			overridePendingTransition(R.anim.main_enter_anim,R.anim.main_exit_anim);

	- Fragment:
	
			getSupportFragmentManager().beginTransaction().setCustomAnimations(R.anim.main_enter_anim,R.anim.main_exit_anim);



#### Frame Animation 帧动画
- 原理：顺序播放预先定义好的一组图片
- 缺点：因为使用大量图片，尤其是大图片，容易 OOM
- 使用：
	1. XML定义一个 AnimationDrawable，路径：res/drawable/frame_animation.xml，如下：

			<?xml version="1.0" encoding="utf-8"?>
			<animation-list xmlns:android="http://schemas.android.com/apk/res/android"
			    android:oneshot="true">
			  <item android:drawable="@drawable/img01" android:duration="500"/>
			  <item android:drawable="@drawable/img02" android:duration="500"/>
			  <item android:drawable="@drawable/img03" android:duration="500"/>
			  <item android:drawable="@drawable/img04" android:duration="500"/>
			</animation-list>
	2. 作为 View 的背景并通过 Drawable 来播放动画
	
	        mButton.setBackgroundResource(R.drawable.frame_animation);
	        AnimationDrawable drawable = (AnimationDrawable) mButton.getBackground();
	        drawable.start();


### Property Animation 属性动画
- 分类：
	- ValueAnimator
	- ObjectAnimator：继承自 ValueAnimator
	- AnimatorSet：动画集合，可以定义一组动画

- 原理：
	- 属性动画根据传入的初始值和最终值，以动画的效果多次调用 set 方法，每次传递给 set 的值都不同，会随着时间的推移，所传递的值越来越接近最终值。假如没有传入初始值，首先会调用该属性的 get 方法，获取到初始值。
- 缺点：
	- Andoird 11 引入的，所以在 3.0 以前的设备上无法使用。但是目前 App 开发支持最低的版本一般都是 4.0，所以该缺点不算问题。

- 可能的Bug:
	- 当对象的属性没有初始值，且没有提供 get 方法，那么在动画运行的时候会 crash
	- 当对象的属性没有提供 set 方法，动画不会出现效果
- XML路径：res/animator
- XML定义：



	| 动画        | XML标签    |
	| -------------|:------------:|
	| ValueAnimator   | \<animator>    |
	| ObjectAnimator| \<objectAnimator> |
| AnimationSet      | \<set>       |


	- \<set>标签android:ordering属性有两个可选择：together，sequentially。
		- together：表示动画集合中的子动画同时播放
		- sequentially：表示动画集合中的子动画按照前后顺序依次播放

	- \<animator> 和 \<objectAnimator> 属性：
		- android:propertyName：属性动画的作用对象的属性的名称
		- android:valueFrom：属性的起始值
		- android:valueTo：属性的结束值
		- android:startOffset：动画的延迟时间，当动画开始后，需要延迟多长毫秒才会真正播放动画
		- android:repeatCount：动画的重复次数
		- android:repeatMode：动画的重复模式，restart 和 reverse
		- android:valueType：android:propertyName 的属性的类型，intType 和 floatType 和 colorType 和 pathType
		- android:duration：动画时长 

- 代码实现：
	- ValueAnimator
	- ObjectAnimator，继承自 ValueAnimator
		- textView 沿着 Y 轴向下移动 textView.getWidth() 距离，持续时间为 1000ms.

				ObjectAnimator.ofFloat(textView,"translationY",textView.getWidth()).setDuration(1000).start();

		- 改变 view 的背景色，3秒内实现从0xFFFF8080到0XFF8080FF的渐变，动画会无限循环而且会有反转效果

				ValueAnimator colorAnim = ObjectAnimator.ofInt(view,"backgroundColor",0xFFFF8080,0XFF8080FF);
			    colorAnim.setDuration(3000);
			    colorAnim.setEvaluator(new ArgbEvaluator());
			    colorAnim.setRepeatCount(ValueAnimator.INFINITE);
			    colorAnim.setRepeatMode(ValueAnimator.REVERSE);
			    colorAnim.start();
	- AnimatorSet，动画集合
		- 5 秒内对旋转、移动、缩放、透明进行改变


		        AnimatorSet set = new AnimatorSet();
		        set.playSequentially(
		            ObjectAnimator.ofFloat(text1,"rotateX",0,360),
		            ObjectAnimator.ofFloat(text1,"rotateY",0,180),
		            ObjectAnimator.ofFloat(text1,"rotate",0,-180),
		            ObjectAnimator.ofFloat(text1,"translationX",0,90),
		            ObjectAnimator.ofFloat(text1,"translationY",0,90),
		            ObjectAnimator.ofFloat(text1,"scaleX",1,1.5f),
		            ObjectAnimator.ofFloat(text1,"scaleY",1,0.5f),
		            ObjectAnimator.ofFloat(text1,"alpha",1,0.25f,1)
		        );
		        set.setDuration(5*1000).start();

- 属性动画的监听
	- 通过 addListener 方法添加监听
		- 实现 AnimatorListener 接口

		        ValueAnimator valueAnimator = new ObjectAnimator();
		        valueAnimator.addListener(new Animator.AnimatorListener() {
		          @Override public void onAnimationStart(Animator animation) {
		          }   //动画开始
		          @Override public void onAnimationEnd(Animator animation) {
		          }   //动画结束
		          @Override public void onAnimationCancel(Animator animation) {
		          }   //动画取消
		          @Override public void onAnimationRepeat(Animator animation) {
		          }   //动画重复
		        });

		- 实现 AnimatorListenerAdapter 接口，可以选择的实现接口的方法。
		
				ValueAnimator valueAnimator = new ObjectAnimator();
		        valueAnimator.addListener(new AnimatorListenerAdapter() {
		          @Override public void onAnimationCancel(Animator animation) {
		            super.onAnimationCancel(animation);
		          }
		
		          @Override public void onAnimationEnd(Animator animation) {
		            super.onAnimationEnd(animation);
		          }
		
		          @Override public void onAnimationRepeat(Animator animation) {
		            super.onAnimationRepeat(animation);
		          }
		
		          @Override public void onAnimationStart(Animator animation) {
		            super.onAnimationStart(animation);
		          }
		
		          @Override public void onAnimationPause(Animator animation) {
		            super.onAnimationPause(animation);
		          }
		
		          @Override public void onAnimationResume(Animator animation) {
		            super.onAnimationResume(animation);
		          }
		        });

	- 通过 addUpdateListener 方法添加监听，AnimatorUpdateListener 比较的特殊，它会监听整个动画过程，动画由很多帧组成，每播放一帧，onAnimationUpdate 就会被调用一次。

		     ValueAnimator valueAnimator = new ObjectAnimator();
		     valueAnimator.addUpdateListener(new ValueAnimator.AnimatorUpdateListener() {
		       @Override public void onAnimationUpdate(ValueAnimator animation) {
		          }
		      });

- 属性动画 crash 或者无效果的分析和解决办法
	- 原因：
		- crash：属性动画必须有初始值，假如没有传入的话，系统会通过 get 获取该属性的初始值，假如没有 初始值 也没有 get 方法，那么就会 crash。
		- 无效果：属性动画通过 set 方法来改变属性值的。没有效果的话，那就是该 set 方法有问题。

	- 解决办法：
		- 给对象的属性加上 get 和 set 方法，一般需要权限。一般使用下面 2 种
		- 用一个类包装原始对象，间接实现 get 和 set 方法

				  ViewWrapper viewWrapper = new ViewWrapper(text);
		          ObjectAnimator.ofFloat(viewWrapper, "width",    viewWrapper.getwidth()+500).setDuration(1000).start();
				
				  private static class ViewWrapper {
				    private View mTarget;
				    public ViewWrapper(View view) {
				      mTarget = view;
				    }
				    public int getwidth() {
				      return mTarget.getLayoutParams().width;
				    }
				    public void setwidth(int number) {
				      mTarget.getLayoutParams().width = number;
				      mTarget.requestLayout();
				    }
				  }
		- 采用 ValueAnimator ，监听动画过程，自己实现属性的改变。

		        ValueAnimator valueAnimator = ValueAnimator.ofInt(1,100);
		        valueAnimator.addUpdateListener(new ValueAnimator.AnimatorUpdateListener() {
		          IntEvaluator mEvaluator = new IntEvaluator();
		          @Override public void onAnimationUpdate(ValueAnimator animation) {
		            float fraction = animation.getAnimatedFraction();
		            textView.getLayoutParams().width=mEvaluator.evaluate(fraction,0,textView.getLayoutParams().width+500);
		            textView.requestLayout();
		          }
		        });
		        valueAnimator.setDuration(1000).start();