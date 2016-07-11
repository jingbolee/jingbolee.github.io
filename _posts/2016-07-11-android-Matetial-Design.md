---
layout:     post
title:      "Material Design 控件的整理和使用"
subtitle:   "Toolbar"
date:       2016-07-11 
author:     "Jerome"
header-img: "img/post-bg-06.jpg"
---

## Material Design
新的控件	
	  
- CoordinatorLayout
	- 组织它的子views之间协作的一个 Layout，它可以给子View切换提供动画效果
	- Behaviors 
- AppBarLayout
	- 可以让包含在其中的控件响应被标记了 ScrollingViewBehavior 的 View 的滚动事件
- CollapsingToolbarLayout
	- 可以控制包含在 CollapsingToolbarLayout 其中的控件，在响应 collapse 时是移除屏幕和固定在最上面
- TabLayout
	- 结合 ViewPager，实现多个TAB的切换的功能

- Navigation View
	- Navigation Drawer在现在的APP中很常见, 以前实现一直不怎么容易，
现在提供的NavigationView组件可以直接放在DrawerLayout中，
通过设置menu resource就能显示菜单项了。 

- NestedScrollView
	- 与 ScrollView 基本相同，不过包含在 NestedScrollView 中的控件移动时才能使 AppBarLayout 缩放

- Palette

	- 调色板， Palette的作用是可以从图像中提取图片的颜色。我们可以把提取的颜色融入到App UI中，可以使UI风格更加美观融洽。


- Floating Action Button
	- Floating Action Button (FAB)是一个悬浮按钮, 它主要用于一些重要的操作，比如在列表界面上新增按钮。

- SnackBar
	- Snackbar是带有动画效果的快速提示条，它只会出现在屏幕底部

### CoordinatorLayout

#### 实现功能
- 功能：滑动的时候，让子 View 能够协调的变化
- 作为顶层布局
- 协调子布局
	- CoordinatorLayout 通过设置子 View 的 Behaviors 来调度子 View
	- 来自 Behavior 的代理 —— 布局    
		![](http://i.imgur.com/b1Ky59r.png)
	- CoordinatorLayout 实现了 NestedScrollingParent 接口，要用到 Behavior 特性的话，默认不实现 NestedScrollingChild 接口 (pre Lollipop) 且不调用dispatchNestedScroll 相关接口的 View 没法使用。  
		![](http://i.imgur.com/yymnTS7.png)
- 综上，需要使用 CoorDinatorLayout 的 BeHavior ,那么子 View 必须实现了 NestedScrollingChild 接口。假如像 ListView 的话，需要自己重写并实现 NestedScrollingChild 接口。

#### Behavior
Behavior 是 Android 新出的 Design 库里新增的布局概念.Behavior只有是CoordinatorLayout的直接子View才有意义。Behavior是一系列回调。让你有机会以非侵入的为View添加动态的依赖布局，和处理父布局(CoordinatorLayout)滑动手势的机会。

#### 自定义View 定义 Behaviors，使该 view 可以与 CoordinatorLayout 协调滑动。

![](http://i.imgur.com/Llp6aFe.png)

创建自定义 Behaviors，并没有想象的那么难，首先我们得搞清楚两个核心元素 **child** 和 **dependency**
##### Childs and dependencies

child 是指需要应用 behavior 的 View ，dependency 担任触发 behavior 的角色，并与 child 进行互动。
在这个例子中， child 是 ImageView， dependency 是 Toolbar，如果 Toolbar 发生移动， ImageView 也会做相应的移动。

##### 效果图
![](http://androcode.es/wp-content/uploads/2015/10/2015-10-27-04_30_50.gif)

##### 实现过程
- 第一步我们需要继承 CoordinatorLayout.Behavior，T 是指某一个 View，
在我们的例子中是 ImageView， 继承后，我们必须实现以下2个方法:
	- layoutDependsOn
		- layoutDependsOn 方法在每次l ayout 发生变化时都会调用，我们需要在 dependency 控件发生变化时返回 True，在我们的例子中是用户在屏幕上滑动时（因为 Toolbar 发生了移动），然后我们需要让 child 做出相应的反应。
			
				@Override
				  public boolean layoutDependsOn(     
				     CoordinatorLayout parent,
				     CircleImageView, child,
				     View dependency) {
				
				     return dependency instanceof Toolbar;
				 }

	- onDependentViewChanged
		- 一旦 layoutDependsOn 返回了 True，第二个方法 onDependentViewChanged 就会被调用，
在这个方法里我们需要实现动画，转场等效果。

				public boolean onDependentViewChanged(
				      CoordinatorLayout parent,
				      CircleImageView avatar,
				      View dependency) {
				
				      modifyAvatarDependingDependencyState(avatar, dependency);
				   }
				
				   private void modifyAvatarDependingDependencyState(
				    CircleImageView avatar, View dependency) {
				        //  avatar.setY(dependency.getY());
				        //  avatar.setBlahBlat(dependency.blah / blah);
				    }

	- 整合后的代码：

			public static class AvatarImageBehavior
			   extends CoordinatorLayout.Behavior<CircleImageView> {
			
			   @Override
			   public boolean layoutDependsOn(
			    CoordinatorLayout parent,
			    CircleImageView, child,
			    View dependency) {
			
			    return dependency instanceof Toolbar;
			  }
			
			  public boolean onDependentViewChanged(   
			    CoordinatorLayout parent,
			    CircleImageView avatar,
			    View dependency) {
			      modifyAvatarDependingDependencyState(avatar, dependency);
			   }
			
			  private void modifyAvatarDependingDependencyState(
			    CircleImageView avatar, View dependency) {
			        //  avatar.setY(dependency.getY());
			        //  avatar.setBlahBlah(dependency.blah / blah);
			    }    
			}

### AppBarLayout
- 继承 LinearLayout ，默认为垂直方向，可以管理其中的控件在内容滚动时的行为，实现滚动手势。
- AppBarLayout 中有一个内置的 Behavior 类，所以可以和 CoordinatorLayout 协调，但是 Toolbar 和 Tablayout 没有，仅仅使用 AppBarLayout 包裹 toolbar 和 tablayout 还不够， AppBarLayout 的子控件还要设置 layout_scrollFlags 属性(或者通过 setScrollFlags( ))才能达到其想要的滑动行为效果。
- 通过给子 View 设置 layout_scrollFlags 参数来控制滚动时候的表现
	- scroll： View 会根据滚动事件进行移动。。当可滚动时，没有设置 scroll 的 View 位置不会变化。
	- snap：滚动结束时，如果View只有部分可见，它将会自动滑动到最近的边界（完全可见或完全隐藏）
	- enterAlways：实现 quick return 效果, 当向下移动时，立即显示 View（比如Toolbar)。
	- enterAlwaysCollapsed：当你的View已经设置minHeight属性又使用此标志时，你的View只能以最小高度进入，只有当滚动视图到达顶部时才扩大到完整高度。当滚动视图滑动到页面顶部的时候，才能显示出来。
	- exitUntilCollapsed：向上移出屏幕时，View 会一直收缩到最小高度后，再移出屏幕，但可以固定Toolbar一直在上面。
	> AppbarLayout 的子 View 不设置任何的 flag ，那么就会一直可见。  
	> snap 和 enterAlwaysCollapsed 使用感觉比较的像，都是向上滑动，隐藏，向下滑动到视图顶部的时候，才显示。
### CollapsingToolbarLayout
- CollapsingToolbarLayout 作用是提供了一个可以折叠的 Toolbar ,它继承 FrameLayout,给它设置 layout_scrollFlags ，可以控制包含在 CollapsingToolbarLayout 中的控件（如：ImageView、Toolbar）在响应 layout_behavior 事件时做出相应的 scrollFlags 滚动事件（移除屏幕或固定在屏幕顶端）
- CollapsingToolbarLayout 的属性：
	- app:expandedTitleMarginStart：设置扩张时候(还没有收缩时) title 向左填充的距离。
	- app:collapsedTitleTextAppearance 这是在收缩时Title文字特点外形的设置
	- app:expandedTitleTextAppearance 同理这是在展开时Title文字特点外形的设置
	- app:contentScrim 设置当完全 CollapsingToolbarLayout折叠(收缩)后的背景颜色。
	- app:expandedTitleMarginStart 设置扩张时候(还没有收缩时)title向左填充的距离
	- app:expandedTitleMarginEnd 这个同理是收缩结束时向左填空的距离
	- 给子 View 的属性：
		- app:layout_collapseMode (折叠模式) -有两个值：
			- pin： 设置为这个模式时，当 CollapsingToolbarLayout 完全收缩后，Toolbar还可以保留在屏幕上。
			- parallax：设置为这个模式时，在内容滚动时，CollapsingToolbarLayout 中的 View （比如 ImageView )也可以同时滚动，实现视差滚动效果，通常和layout_collapseParallaxMultiplier (设置视差因子)搭配使用。
		- app:layout_collapseParallaxMultiplier (视差因子)： 设置视差滚动因子，值为：0~1。



### 示例代码

	<android.support.design.widget.CoordinatorLayout 
	    xmlns:android="http://schemas.android.com/apk/res/android"
	    xmlns:app="http://schemas.android.com/apk/res-auto"
	    xmlns:tools="http://schemas.android.com/tools"
	    android:layout_width="match_parent"
	    android:layout_height="match_parent"
	    tools:context=".MainActivity">
	    <android.support.design.widget.AppBarLayout
	        android:layout_width="match_parent"
	        android:layout_height="256dp"
	        android:fitsSystemWindows="true">
	        <android.support.design.widget.CollapsingToolbarLayout
	            android:id="@+id/collapsing_toolbar_layout"
	            android:layout_width="match_parent"
	            android:layout_height="match_parent"
	            app:contentScrim="#30469b"
	            app:expandedTitleMarginStart="48dp"
	            app:layout_scrollFlags="scroll|exitUntilCollapsed">
	            <ImageView
	                android:layout_width="match_parent"
	                android:layout_height="match_parent"
	                android:scaleType="centerCrop"
	                android:src="@mipmap/bg"
	                app:layout_collapseMode="parallax"
	                app:layout_collapseParallaxMultiplier="0.7"  />
	            <android.support.v7.widget.Toolbar
	                android:id="@+id/toolbar"
	                android:layout_width="match_parent"
	                android:layout_height="?attr/actionBarSize"
	                app:layout_collapseMode="pin" />
	        </android.support.design.widget.CollapsingToolbarLayout>
	    </android.support.design.widget.AppBarLayout>
	    <LinearLayout
	        android:layout_width="match_parent"
	        android:layout_height="match_parent"
	        android:orientation="vertical"
	        app:layout_behavior="@string/appbar_scrolling_view_behavior">
	        <android.support.v7.widget.RecyclerView
	            android:id="@+id/recyclerView"
	            android:layout_width="match_parent"
	            android:layout_height="match_parent"
	            android:scrollbars="none" />
	    </LinearLayout>
	</android.support.design.widget.CoordinatorLayout>

> 使用CollapsingToolbarLayout时必须把title设置到CollapsingToolbarLayout上，设置到Toolbar上不会显示。即：  
> mCollapsingToolbarLayout.setTitle(" ");  

> 改变title的字体颜色：  

> 扩张时候的title颜色：mCollapsingToolbarLayout.setExpandedTitleColor();  

> 收缩后在Toolbar上显示时的title的颜色：  mCollapsingToolbarLayout.setCollapsedTitleTextColor();  

综上分析：当设置了layout_behavior的控件响应起了CollapsingToolbarLayout中的layout_scrollFlags事件时，ImageView会有视差效果的向上滚动移除屏幕，当开始折叠时CollapsingToolbarLayout的背景色(也就是Toolbar的背景色)就会变为我们设置好的背景色，Toolbar也一直会固定在最顶端。

> 注意： 在AppBarLayout 中添加 ToolBar 、CollapsingToolbarLayout、ImageView 的时候，这些控件可以处于同一等级，也可以把 ToolBar 和 ImageView 放在 CollapsingToolbarLayout 中。反正是看需求。不一定非要把 ToolBar 或 ImageView 放在 CollapsingToolbarLayout。  

### Tablayout

TabLayout可以很容易地在APP中添加Tab分组功能
有好几种方式来使用它:
- 固定Tabs，根据View的宽度适配
- 
![](https://d262ilb51hltx0.cloudfront.net/max/800/1*pmTCUt3WtAEXWh0vUX1Tsw.png)
- 固定Tabs, 在View中居中显示

![](https://d262ilb51hltx0.cloudfront.net/max/800/1*xGCDe6ARHeHk2v9pnXS_bA.png)
- 可滑动的Tabs

![](https://d262ilb51hltx0.cloudfront.net/max/800/1*CiuC-A6TxcOJvn_hMVr0jA.ogv)

要实现上述效果，首先我们需要加入TabLayout:

	<android.support.design.widget.TabLayout
	    android:id="@+id/sliding_tabs"
	    android:layout_width="match_parent"
	    android:layout_height="wrap_content"
	    app:tabMode="fixed"
	    app:tabGravity="fill" />

然后, 我们可以通过以下这些属性调整TabLayout的外观:

- tabMode - TabLayoutd模式，可以选择 fixed 或 scrollable
- tabGravity - Tab的对齐方式, 可以选择 fill 或 centre
- setText() - 设置Tab上的文字
- setIcon() - 设置Tab上的图标


我们还可以给TabLayout设置一些Listener:

- OnTabSelectedListener - Tab被选中时，触发的Listener
- TabLayoutOnPageChangeListener
- ViewPagerOnTabSelectedListener


我们添加好TabLayout后, 我们只需要通过setupWithViewPager方法加入viewpager:

	ViewPager pager = (ViewPager)rootView.findViewById(R.id.viewPager);
	pager.setAdapter(new MyPagerAdapter(getActivity().getSupportFragmentManager()));
	
	TabLayout tabLayout = (TabLayout) rootView.findViewById(R.id.sliding_tabs);
	tabLayout.addTab(tabLayout.newTab().setText("Tab One"));
	tabLayout.addTab(tabLayout.newTab().setText("Tab Two"));
	tabLayout.addTab(tabLayout.newTab().setText("Tab Three"));
	tabLayout.setupWithViewPager(pager);

### NavigationView
Navigation Drawer在现在的APP中很常见, 以前实现一直不怎么容易，
现在提供的NavigationView组件可以直接放在DrawerLayout中，
通过设置menu resource就能显示菜单项了。

![](https://d262ilb51hltx0.cloudfront.net/max/800/1*AsV0mMvJ21ni3aoSQqQ5LA.png)

	<android.support.v4.widget.DrawerLayout
	    xmlns:android="http://schemas.android.com/apk/res/android"
	    xmlns:app="http://schemas.android.com/apk/res-auto"
	    android:id="@+id/drawer_layout"
	    android:layout_width="match_parent"
	    android:layout_height="match_parent"
	    android:fitsSystemWindows="true">
	
	    <FrameLayout
	        android:id="@+id/main_content_frame"
	        android:layout_width="match_parent"
	        android:layout_height="match_parent" />
	
	    <android.support.design.widget.NavigationView
	        android:id="@+id/navigation_view"
	        android:layout_width="wrap_content"
	        android:layout_height="match_parent"
	        android:layout_gravity="start"
	        app:headerLayout="@layout/navigation_header"
	        app:menu="@menu/drawer" />
	
	</android.support.v4.widget.DrawerLayout>

这个View有两个主要属性:

#### Header Layout

headerLayout是一个可选得属性，通过设置它我们可以在导航栏上面增加一个Header,通用的做法我们在上面显示用户信息。

#### Menu

menu属性用来定义需要引用的menu resource。

NavigationView menus我们一般有两种用法，第一种是使用标准的单选模式：

	<menu xmlns:android="http://schemas.android.com/apk/res/android"
	    xmlns:tools="http://schemas.android.com/tools"    
	    tools:context=".MainActivity">
	    <group android:checkableBehavior="single">
	        <item
	            android:id="@+id/navigation_item_1"
	            android:checked="true"
	            android:icon="@drawable/ic_android"
	            android:title="@string/navigation_item_1" />
	        <item
	            android:id="@+id/navigation_item_2"
	            android:icon="@drawable/ic_android"
	            android:title="@string/navigation_item_2" />
	    </group>
	</menu>

这样菜单项只是简单的罗列，所有的菜单项都属于同一个分组。

第二种用法也是相似的，不过我们可以进行分组，给每一个分组定义标题，如下所示：
	
	<menu xmlns:android="http://schemas.android.com/apk/res/android"
	    xmlns:tools="http://schemas.android.com/tools"                       
	    tools:context=".MainActivity">
	    <group android:checkableBehavior="single">
	        <item
	            android:id="@+id/navigation_subheader"
	            android:title="@string/nav_header">
	            <menu>
	                <!-- Menu items go here -->
	            </menu>
	         </item>
	    </group>
	</menu>

这样我们就可以把我们的菜单项进行分组，这还是很有用得，我们可以按功能把菜单项进行分组。

还有一些重要属性的属性我们可以设置，如下:

- itemBackground — 设置菜单项的背景
- itemIconTint — 设置菜单项的图标
- itemTextColor — 这只菜单项目的文本颜色


我们可以通过实现OnNavigationItemSelectedListener方法，处理菜单项的点击事件。

### NestedScrollView
该控件，目前主要使用在包裹没有实现 behavior 的控件，比如ListView，Webview等。以便让这些控件可以在 CoordinatorLayout 中协调滑动，实现滑动手势。

### Palette

Palette可以提取的颜色如下：

- Vibrant （有活力的）
- Vibrant dark（有活力的 暗色）
- Vibrant light（有活力的 亮色）
- Muted （柔和的）
- Muted dark（柔和的 暗色）
- Muted light（柔和的 亮色）

通过Palette对象获取到六个样本swatch

	Palette.Swatch s = p.getVibrantSwatch();       //获取到充满活力的这种色调
	Palette.Swatch s = p.getDarkVibrantSwatch();    //获取充满活力的黑
	Palette.Swatch s = p.getLightVibrantSwatch();   //获取充满活力的亮
	Palette.Swatch s = p.getMutedSwatch();           //获取柔和的色调
	Palette.Swatch s = p.getDarkMutedSwatch();      //获取柔和的黑
	Palette.Swatch s = p.getLightMutedSwatch();    //获取柔和的亮

swatch对象对应的颜色方法

- getPopulation(): 像素的数量
- getRgb(): RGB颜色
- getHsl(): HSL颜色
- getBodyTextColor(): 用于内容文本的颜色
- getTitleTextColor(): 标题文本的颜色

Palette经常用于和ViewPager，Fragment搭配使用，当我们的Pager切换时伴随着Fragment的变化，而Fragment里的内容一般是不同的，所以每个Fragment里的一般视觉效果也是不同的，所以我们可以用Palette来去提取Fragment中的主色调，把这个主色调用于整体的UI风格。

先看效果图，如下：
![](https://raw.githubusercontent.com/loonggg/MaterialDesignDemo/master/image/tmpdir__16_6_28_18_41_09.gif)

代码：

	// 用来提取颜色的Bitmap
	Bitmap bitmap = BitmapFactory.decodeResource(getResources(), PaletteFragment.getBackgroundBitmapPosition(position));
	// Palette的部分
	Palette.Builder builder = Palette.from(bitmap);
	builder.generate(new Palette.PaletteAsyncListener() {@Override public void onGenerated(Palette palette) {
	        //获取到充满活力的这种色调
	        Palette.Swatch vibrant = palette.getVibrantSwatch();
	        //根据调色板Palette获取到图片中的颜色设置到toolbar和tab中背景，标题等，使整个UI界面颜色统一
	        toolbar_tab.setBackgroundColor(vibrant.getRgb());
	        toolbar_tab.setSelectedTabIndicatorColor(colorBurn(vibrant.getRgb()));
	        toolbar.setBackgroundColor(vibrant.getRgb());
	
	        if (android.os.Build.VERSION.SDK_INT >= 21) {
	            Window window = getWindow();
	            window.setStatusBarColor(colorBurn(vibrant.getRgb()));
	            window.setNavigationBarColor(colorBurn(vibrant.getRgb()));
	        }
	    }
	});

### Floating Action Button
这个按钮我们使用时一般用以下2中尺寸:
Normal (56dp) — 大部分情况下使用
Mini (40dp) — 只有在与屏幕上其他组件保持一致性的时候使用  
FAB按钮默认会使用主题中定义的背景色，但我们也可以很容易的修改背景色，以下是一些我们一般会修改的属性：

- fabSize - 设定FAB的大小 (‘normal’ or ‘mini’)
- backgroundTint - 设置边框大小
- rippleColor - 设定按下时的颜色
- src - 设定在FAB中显示的图标
- layout_anchor - 设置显示坐标的锚点
- layout_anchorGravity - 设置锚点的对齐方式


我们只要简单加入以下代码，就可以实现FAB:

		<android.support.design.widget.FloatingActionButton
		     android:id=”@+id/fab_normal”
		     android:layout_width=”wrap_content”
		     android:layout_height=”wrap_content”
		     android:src=”@drawable/ic_plus”
		     app:fabSize=”normal” />


### SnackBar

Snackbar是带有动画效果的快速提示条，它只会出现在屏幕底部。
它基本上继承了Toast的方法和属性，但与Toast不同的是，Snackbar上可以带有按钮，
当Snackbar出现时，用户可以点击按钮做相应的处理；
Snackbar支持滑动消失，类似通知栏的消息；
如果用户没做任何操作，Snackbar在到达设定的时间后也会自动消失。

	Snackbar.make(mDrawerLayout, "Your message", Snackbar.LENGTH_SHORT)
	    .setAction(getString(R.string.text_undo), this)
	    .show();

> 注意：不能同时显示多个Snackbar

>
> http://www.jianshu.com/p/a506ee4afecb
> https://www.aswifter.com/2015/11/12/mastering-coordinator/
> https://segmentfault.com/a/1190000005024216?utm_source=Weibo&utm_medium=shareLink&utm_campaign=socialShare&from=singlemessage&isappinstalled=0
> http://www.open-open.com/lib/view/open1438265746378.html