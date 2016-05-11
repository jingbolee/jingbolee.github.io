---
layout:     post
title:      "使用views的setBackgroundResource()方法加载大图片造成OOM"
subtitle:   "设备大图片为view背景造成OOM"
date:       2016-05-11 
author:     "Jerome"
header-img: "img/post-bg-06.jpg"
---


- 问题：使用ViewPager，给页面设置背景的时候使用setBackgroundResource()，时间长了以后容易造成OOM
- 错误：OOM
- 原因：
   - 通过setBackgroundResource()设置大图片的时候，这些函数在完成decode后，最终都是通过java层的createBitmap来完成的，需要消耗更多内存。
- 解决办法：
   
		BitmapFactory.Options opt = new BitmapFactory.Options();
        opt.inPreferredConfig = Bitmap.Config.RGB_565;
        opt.inPurgeable = true;
        opt.inInputShareable = true;
        InputStream is = ctx.getResources().openRawResource(R.drawable.splash );
        Bitmap bm = BitmapFactory.decodeStream(is, null, opt);
        BitmapDrawable bd = new BitmapDrawable(ctx.getResources(), bm);
        view.setBackgroundDrawable(bd);