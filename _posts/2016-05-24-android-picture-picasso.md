---
layout:     post
title:      "图片开源库Picasso的简单使用"
subtitle:   "Picasso"
date:       2016-05-24 
author:     "Jerome"
header-img: "img/post-bg-06.jpg"
---


#开源库-图片-Picasso

###简介：Picasso是Square公司开源的一个Android图形缓存库，可以实现图片下载和缓存功能。
  
1. [GitHub: https://github.com/square/picasso](https://github.com/square/picasso)  
2. [官网：http://square.github.io/picasso/](http://square.github.io/picasso/)
3. [javadoc:http://square.github.io/picasso/2.x/picasso/](http://square.github.io/picasso/2.x/picasso/)


###加入到项目(Android Studio)：


- 在项目的build.gradle的dependencies{ }中添加：

	compile 'com.squareup.picasso:picasso:2.5.2'



###介绍和使用：


-  一行代码把网络图片加载到imageView中：

			Picasso.with(context).load("http://i.imgur.com/DvpvklR.png").into(imageView);
![](http://www.jcodecraeer.com/uploads/20140731/67391406772378.png)
- Picasso不仅实现了图片异步加载的功能，还解决了android中加载图片时需要解决的一些常见问题：
	
	1. 在adapter中需要取消已经不在视野范围的ImageView图片资源的加载，否则会导致图片错位，Picasso已经解决了这个问题。
	
	2. 使用复杂的图片压缩转换来尽可能的减少内存消耗
		
	3. 自带内存和硬盘二级缓存功能
		 
特性和示例代码：

1. Adapter中的下载：Adapter的重用会被自动检测到，Picasso会取消上次的加载

		@Override 
 		public void getView(int position, View convertView, ViewGroup parent) {
		  SquaredImageView view = (SquaredImageView) convertView;
		  if (view == null) {
		    view = new SquaredImageView(context);
		  }
		  String url = getItem(position);
		
		  Picasso.with(context).load(url).into(view);
		}
2. 图片转换：转换图片以适应布局大小并减少内存占用

		Picasso.with(context)
		  .load(url)
		  .resize(50, 50)
		  .centerCrop()
		  .into(imageView)	 

3. 还可以自定义转换：

		 public class CropSquareTransformation implements Transformation {
		    @Override 
			public Bitmap transform(Bitmap source) {
		    int size = Math.min(source.getWidth(), source.getHeight());
		    int x = (source.getWidth() - size) / 2;
		    int y = (source.getHeight() - size) / 2;
		    Bitmap result = Bitmap.createBitmap(source, x, y, size, size);
		    if (result != source) {
		      source.recycle();
		    }
		    return result;
		  }
		
		    @Override 
			public String key() { return "square()"; }
		}
将CropSquareTransformation 的对象传递给transform 方法即可。

4. Place holders-空白或者错误占位图片：picasso提供了两种占位图片，未加载完成或者加载发生错误的时需要一张图片作为提示。

		Picasso.with(context)
		    .load(url)
		    .placeholder(R.drawable.user_placeholder)
		    .error(R.drawable.user_placeholder_error)
		    .into(imageView);
加载发生错误会重复三次请求，三次都失败才会显示erro Place holder

5. 资源文件的加载：除了加载网络图片picasso还支持加载Resources, assets, files, content providers中的资源文件。
		
		Picasso.with(context).load(R.drawable.landing_screen).into(imageView1);
		Picasso.with(context).load("file:///android_asset/DvpvklR.png").into(imageView2);
		Picasso.with(context).load(new File(...)).into(imageView3);
6. 调试支持：调用函数 setIndicatorsEnabled(true) 可以在加载的图片左上角显示一个 三角形 ，不同的颜色代表加载的来源

	- 红色：代表从网络下载的图片
		
	- 黄色：代表从磁盘缓存加载的图片
		
	- 绿色：代表从内存中加载的图片



###自己使用情况：

需求：网络图片放在轮播大图里，要求宽匹配设备宽，高显示为200dp，在没有图片或者加载失败时显示一张默认图片

	ImageView view = new ImageView(mActivity);
    int width = DensityUtil.getDevicePx(mActivity)[0]; //获取设备的宽，为px
    int heigth = DensityUtil.dip2px(mActivity, 200);   //把dp转换为当前设备的px
    Picasso.with(mActivity)
            .load(topNewsInfo.topimage)               //网络图片地址
            .placeholder(R.drawable.topnews_item_default) //图片未加载完成显示的默认图片
            .resize(width, heigth) //需要适配的宽和高
            .centerCrop()          //全部显示，假如图片出现不合适，会裁剪掉部分然后填充全部
            .into(view);