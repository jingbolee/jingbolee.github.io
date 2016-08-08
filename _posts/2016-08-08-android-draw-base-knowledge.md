---
layout:     post
title:      "Android 绘图基础知识"
subtitle:   "Canvas, Paint, Matrix, ColorMatrix, Shader"
date:       2016-08-08 
author:     "Jerome"
header-img: "img/post-bg-02.jpg"
---


## Android 绘图基础知识

在自定义 View 的时候，重写方法 onDraw() 的时候，需要用到 Android 绘图基础知识，位于 android.graphics 包下，其中有几个类，Canvas（画布） ，Paint（画笔） ,Color（颜色）, Path（路径）, Matrix（矩阵） 等，这些类可以提供我们在自定义 View 中实现自己想要的效果。

### Color

Android 中颜色常用表示方法有

- int color = Color.BLUE;
- int color = Color.argb(150,200,0,100);
- 在 xml 文件中定义颜色

Color 类中颜色常量

| 常量       | 颜色   |
| -------------|:------------:|
| Color.BLACK    | 黑色    |
| Color.GREEN     | 绿色   |
| Color.BLUE     | 蓝色     |
| Color.LTGRAY     | 浅灰色    |
| Color.CYAN     | 青绿色     |
| Color.MAGENTA    | 红紫色     |
| Color.DKGRAY     | 灰黑色  |
| Color.RED     | 红色    |
| Color.YELLOW     | 黄色     |
| Color.TRANSPARENT     | 透明     |
| Color.GRAY     | 灰色     |
| Color.WHITE     | 白色     |

### Canvas

Canvas 画布，将图像绘制到画布上。Canvas 类可以实现各种图形的绘制工作，如直线，矩形，圆等。

- 点
	- drawPoint(float x,float y,Paint paint)
	- drawPoints(float[] pts,int offset,int count , Paint paint)

- 线
	- drawLine(float startX,float startY,float stopX,float stopY,Paint paint)
	- drawLines(float[] pts, int offset,int count ,Paint paint)
	- drawLines(float[] pts,Paint paint)

- 矩形
	- drawRect(float x1,float y1,float x2,float y2,Paint paint)
	- drawRect(Rect rect,Paint paint)

- 多边形
	- drawVertices(VertexMode mode, int vertexCount,float[] verts,
            int vertOffset, float[] texs, int texOffset,int[] colors,
            int colorOffset, short[] indices, int indexOffset, int indexCount,Paint paint)
- 弧线,圆弧,三点钟方向为0°
	- drawArc(Rect rect,float startAngle,float sweepAngle,boolean useCenter,Paint paint)

- 圆
	- drawCircle(float cx,float cy, float r ,Paint paint)

- 文字
	- drawText(String s,float x,float y,Paint paint)

- 位图（BitMap）
	- drawBitmap(Bitmap bitmap, float left, float top,  Paint paint)
	- drawBitmap(Bitmap bitmap,Rect src,Rect dst,Paint paint)
	- drawBitmap(Bitmap bitmap,  Matrix matrix,  Paint paint)
	- drawBitmap(Bitmap bitmap, Rect src, RectF dst,
            Paint paint)



### Paint

- Paint 画笔，主要保存了颜色，样式等画笔信息。用于指定图形或者文字的颜色，大小等

- 常用方法：

	- mPaint.setAntiAlias()：设置锯齿效果
	- mPaint.setColor() ：设置画笔颜色
	- mPaint.setStyle(Paint.Style.STROKE)：设置画笔风格，空心或者实心
	- mPaint.setStrokeWidth(4)：设置空心的边框宽度
	- mPaint.setAlpha()：设置 Alpha 值
	- mPaint.setARGB()：设置画笔的 a,r,g,b值
	- mPaint.setTextSize()：设置字体尺寸


- Paint 对象的设置方法大致分为两类
	- 与图形绘制有关
		- setARGB(int a,int r,int g,int b):设置绘制的颜色
		- setAlpha(int a)：设置绘制图形的透明度
		- setColor(int color)：设置绘制的颜色
		- setAntiAlias(boolean aa)：设置是否使用抗锯齿功能，会消耗较大资源，绘制图形速度会变慢
		- setDither(boolean aa)：设置是否使用图形抖动处理，会使绘制出来的图片颜色更加平滑和饱满，图形更加清晰
		- setFilterBitmap(boolean filter):
		- setMaskFilter(MaskFilter maskfilter):设置 MaskFilter ，可以用不同的 MaskFilter 实现滤镜的效果，如滤化，立体等
		- setColorFilter(ColorFilter colorfilter):设置颜色过滤器，可以在绘制颜色时实现不同颜色的变换效果
		- setPathEffect(PathEffect effect):设置绘制路径的效果，如点画线等
		- setShader(Shader shader):设置图像效果，使用 Shader 可以绘制出各种渐变效果。
		- setShadowLayout(float radius,float dx,float dy,int color):在图形下面设置阴影层，产生阴影效果，radius 为阴影的角度，dx 和 dy 分别为阴影在 X 轴和 Y 轴上的距离，color 为阴影的颜色。
		- setStyle(Paint.Style style):设置画笔的样式，为 FILL,FILL_OR_STROKE,或 STROE
		- setStrokeCap(Paint.Cap cap):当画笔样式为 STROKE 或者 FILL_OR_STROKE 时，设置笔刷的图形样式，如圆形样式 Cap.ROUND,或方形样式 Cap.SQUARE
		- setStrokeJoin(Paint.Join join):设置绘制时各图形的结合方法，如平滑效果等。
		- setStrokeWidth(float width):当画笔样式为 STROKE 或者 FILL_OR_STROKE 时，设置笔刷的粗细度
		- setXfermode(Xfermode xfermode):设置图形重叠时的处理方式，如何并，取交集或并集，经常用来制作橡皮的擦除效果
		 
	- 与文本绘制有关
		- setFakeBoldText(boolean fakeBoldText)：模拟实现粗体文字，设置在小字体上效果会比较的差
		- setSubpixelText(boolean subpixelText)：设置该项为 true,将有助于文本在屏幕上的显示效果
		- setTextAlign(Paint.Align align)：设置绘制文字的对齐方向
		- setTextScaleX(float scaleX)：设置绘制文字 X 轴的缩放比例，可以实现文字的拉伸的效果
		- setTextSize(float textSize)：设置绘制文字的字号大小
		- setTextSkewX(float skewX)：设置斜体文字， skewX 为倾斜弧度
		- setTypeface(Typeface typeface)：设置 Typeface 对象，即字体风格，包括粗体，斜体以及衬线体，非衬线体等
		- setUnderlineText(boolean underlineText)：设置带有下划线的文字效果
		- setStrikeThruText(boolean strikeThruText):设置带有删除线的效果

- 实例-奥运五环
	- OlympicView.java

			public class OlympicView extends View {
			
			    public OlympicView(Context context) {
			        super(context);
			    }
			    
			    @Override
			    protected void onDraw(Canvas canvas) {
			        super.onDraw(canvas);
			        int radius = DensityUtil.dip2px(getContext(),40);
			        int strokeWidth = DensityUtil.dip2px(getContext(),5);
			
			        Paint mPaint = new Paint();
			        mPaint.setStyle(Paint.Style.STROKE);
			        mPaint.setStrokeWidth(strokeWidth);
			        mPaint.setAntiAlias(true);
			
			        int width = getWidth();
			        int height = getHeight();
			
			        mPaint.setColor(Color.BLUE);
			        canvas.drawCircle(width*2/8,height/4,radius,mPaint);
			
			        mPaint.setColor(Color.YELLOW);
			        canvas.drawCircle(width*3/8,height/4+radius,radius,mPaint);
			
			        mPaint.setColor(Color.BLACK);
			        canvas.drawCircle(width*4/8,height/4,radius,mPaint);
			
			        mPaint.setColor(Color.GREEN);
			        canvas.drawCircle(width*5/8,height/4+radius,radius,mPaint);
			
			        mPaint.setColor(Color.RED);
			        canvas.drawCircle(width*6/8,height/4,radius,mPaint);
			
			        mPaint.setStyle(Paint.Style.FILL);
			        mPaint.setColor(Color.BLUE);
			        mPaint.setTextSize(60);
			        mPaint.setUnderlineText(true);
			        canvas.drawText("Welcome To Rio",width/2-200,height/2,mPaint);
			
			        mPaint.setUnderlineText(false);
			        canvas.drawText("欢迎来到里约",width/2-200,height/2+60,mPaint);
			    }
			}

	- 效果图

		![](http://i.imgur.com/PcuQFyz.png)

### Path

- Path 类可以在 View 上将 N 个点连成一条“路径”，然后调用 canvas 的 drawPath(path,paint) 即可验证路径绘制图形

- Android 还为路径绘制提供了 PathEffect 来定义绘制效果，PathEffect 包含子类如下：

	- DashPathEffect
	- PathDashPathEffect
	- SumPathEffect
	- CornerPathEffect
	- DiscretePathEffect
	- ComposePathEffect

- Path 常用方法：
	- moveTo(float x, float y)：设置起点
	- lineTo(float x, float y)：连接2点
	- close()：


- 实例-PathView.java

		public class PathView extends View {
		    private static final String TAG = "PathView";
		    float phase;
		    PathEffect[] effects = new PathEffect[7];
		    int[] colors;
		    private Paint paint;
		    Path path;
		
		    public PathView(Context context) {
		        super(context);
		
		        paint = new Paint();
		        paint.setStyle(Paint.Style.STROKE);
		        paint.setStrokeWidth(4);
		        //创建,并初始化Path
		        path = new Path();
		        path.moveTo(0, 0);
		        for (int i = 1; i <= 15; i++) {
		            //生成15个点,随机生成它们的坐标,并将它们连成一条Path
		            path.lineTo(i * DensityUtil.dip2px(getContext(),20), (float) Math.random() *
		                    DensityUtil.dip2px(getContext(),60));
		        }
		        //初始化七个颜色
		        colors = new int[]{
		                Color.BLACK, Color.BLUE, Color.CYAN,
		                Color.GREEN, Color.MAGENTA, Color.RED, Color.YELLOW
		        };
		    }
		
		    @Override
		    protected void onDraw(Canvas canvas) {
		        super.onDraw(canvas);
		        //将背景填充成白色
		        canvas.drawColor(Color.WHITE);
		        //-------下面开始初始化7中路径的效果
		        //使用路径效果
		        effects[0] = null;
		        //使用CornerPathEffect路径效果
		        effects[1] = new CornerPathEffect(10);
		        //初始化DiscretePathEffect
		        effects[2] = new DiscretePathEffect(3.0f, 5.0f);
		        //初始化DashPathEffect
		        effects[3] = new DashPathEffect(new float[]{20, 10, 5, 10}, phase);
		        //初始化PathDashPathEffect
		        Path p = new Path();
		        p.addRect(0, 0, 8, 8, Path.Direction.CCW);
		        effects[4] = new PathDashPathEffect(p, 12, phase, PathDashPathEffect.Style.ROTATE);
		        //初始化PathDashPathEffect
		        effects[5] = new ComposePathEffect(effects[2], effects[4]);
		        effects[6] = new SumPathEffect(effects[4], effects[3]);
		        //将画布移到8,8处开始绘制
		        canvas.translate(DensityUtil.dip2px(getContext(),8), DensityUtil.dip2px(getContext(),8));
		        //依次使用7中不同路径效果,7种不同的颜色来绘制路径
		        for (int i = 0; i < effects.length; i++) {
		            paint.setPathEffect(effects[i]);
		            paint.setColor(colors[i]);
		            canvas.drawPath(path, paint);
		            canvas.translate(0, DensityUtil.dip2px(getContext(),60));
		        }
		        //改变phase值,形成动画效果
		        phase += 1;
		        invalidate();
		    }
		}

- 效果图

	![](http://i.imgur.com/Ij60lDh.png)
	




### Matrix

Matrix,矩阵，在 Android 中由 9 个 float 值构成的一个 3x3 的矩阵，主要用于图像的变换，如平移，旋转，缩放，扭曲等，它本身不能对图像或View进行变换，但它可与其他API结合来控制图形、View 的变换，如 Canvas。

Matrix提供了一些方法来控制图片变换：

- setTranslate(float dx,float dy)：控制 Matrix 进行位移。
- setSkew(float kx,float ky)：控制 Matrix 进行倾斜，kx、ky为X、Y方向上的比例。
- setSkew(float kx,float ky,float px,float py)：控制 Matrix 以 px、py 为轴心进行倾斜，kx、ky 为 X、Y 方向上的倾斜比例。
- setRotate(float degrees)：控制 Matrix 进行 depress 角度的旋转，轴心为（0,0）。
- setRotate(float degrees,float px,float py)：控制 Matrix 进行 depress 角度的旋转，轴心为(px,py)。
- setScale(float sx,float sy)：设置 Matrix 进行缩放，sx、sy 为 X、Y 方向上的缩放比例。
- setScale(float sx,float sy,float px,float py)：设置 Matrix 以 (px,py) 为轴心进行缩放，sx、sy 为 X、Y 方向上的缩放比例。
- 除 Translate(平移) 外，Scale(缩放)、Rotate(旋转)、Skew(扭曲) 都可以围绕一个中心点来进行，如果不指定，在默认情况下是围绕 (0, 0) 来进行相应的变换的。


- 实例-通过 Matrix 缩放，移动，旋转和倾斜图片
	- MainActivity.java

			public class MainActivity extends AppCompatActivity {
			
			    private ImageView imageView, imageView1;
			    private Bitmap baseBitmap;
			
			    @Override
			    protected void onCreate(Bundle savedInstanceState) {
			        super.onCreate(savedInstanceState);
			        setContentView(R.layout.activity_main);
			        imageView = (ImageView) findViewById(R.id.imageview);
			        imageView1 = (ImageView) findViewById(R.id.imageview1);
			        baseBitmap = BitmapFactory.decodeResource(getResources(), R.mipmap.ic_launcher);
			    }
			    //放大2倍
			    public void scale(View view) {
			        Bitmap scaleBitmap = Bitmap.createBitmap(baseBitmap.getWidth() * 2, baseBitmap
			                .getHeight() * 2, baseBitmap.getConfig());
			        Paint p = new Paint();
			        Canvas canvas = new Canvas(scaleBitmap);
			        Matrix matrix = new Matrix();
			        matrix.setScale(2, 2);
			        canvas.drawBitmap(baseBitmap, matrix, p);
			        imageView1.setImageBitmap(scaleBitmap);
			    }
			
			    //x,y轴上各移动100个像素
			    public void translate(View view) {
			        Bitmap translateBitmap = Bitmap.createBitmap((int) (baseBitmap.getWidth() + 100), (int)
			                (baseBitmap
			                        .getHeight() + 100), baseBitmap.getConfig());
			        Paint p = new Paint();
			        Matrix matrix = new Matrix();
			        matrix.setTranslate(100,100);
			        Canvas canvas = new Canvas(translateBitmap);
			        canvas.drawBitmap(baseBitmap,matrix,p);
			        imageView1.setImageBitmap(translateBitmap);
			    }
			
			    //180度旋转
			    public void rotate(View view) {
			        Bitmap rotateBitmap = Bitmap.createBitmap(baseBitmap.getWidth(), baseBitmap
			                .getHeight(), baseBitmap.getConfig());
			        Paint p = new Paint();
			        Canvas canvas = new Canvas(rotateBitmap);
			        Matrix matrix = new Matrix();
			        matrix.setRotate(180, baseBitmap.getWidth() / 2,
			                baseBitmap.getHeight() / 2);
			        canvas.drawBitmap(baseBitmap, matrix, p);
			        imageView1.setImageBitmap(rotateBitmap);
			    }
			
			    //倾斜
			    public void skew(View view) {
			        Bitmap skewBitmap = Bitmap.createBitmap(baseBitmap.getWidth()+(int)(baseBitmap.getWidth()
			                *0.5),baseBitmap.getHeight()+(int)(baseBitmap.getHeight()*1.5),baseBitmap
			                .getConfig());
			        Paint p = new Paint();
			        Canvas canvas = new Canvas(skewBitmap);
			        Matrix matrix = new Matrix();
			        matrix.setSkew(0.5f,1.5f);
			        canvas.drawBitmap(baseBitmap,matrix,p);
			        imageView1.setImageBitmap(skewBitmap);
			    }
			}

	- 效果图
		- scale

			![](http://i.imgur.com/sjHsdO6.png)
		
		- translate

			![](http://i.imgur.com/HJuP2iX.png)			

		- rotate

			![](http://i.imgur.com/fZDPhbE.png)

		- skew

			![](http://i.imgur.com/44oUUXC.png)

### ColorMatrix

- 在 Android 中，图片是以一个个 RGBA 的像素点的形式加载到内存中的，所以如果需要改变图片的颜色，就需要针对这一个个像素点的 RGBA 的值进行修改，其实主要是 RGB，A 是透明度。在 Android 下，修改图片 RGBA 的值需要 ColorMatrix 类的支持，它定义了一个5*4的 float[] 类型的矩阵，矩阵中每一行表示 RGBA 中的一个参数。
- 一般常用指定 ColorMatrix 的 RGBA 值的方式有两种：

	- 通过构造函数 ColorMatrix(float[] src) 直接得到一个 ColorMatrix 对象，其中src 参数为 5*4的 float[] 类型的矩阵。
	- 通过构造函数 ColorMatrix() 得到 ColorMatrix 对象，再通过 set(float[] src)指定一个 5*4 的 float[] 类型的矩阵。
- 下面是定义了一个不修改的原图的 RGBA 的 ColorMatrix。

		ColorMatrix colorMatrix = new ColorMatrix();
	        colorMatrix.set(new float[] { 
	                1, 0, 0, 0, 0, 
	                0, 1, 0, 0, 0, 
	                0, 0, 1,0, 0, 
	                0, 0, 0, 1, 0 
	                });

	- 可以看到，代码中，第三行定义的是 R，第四行定义的是 G，第五行定义的是 B，第六行定义的是 A。在定义的时候，需要注意数组的顺序必须正确。

　　这个矩阵定义的是一连串 float 数组，其中不同的位置代表了不同的 RGBA 值，它的范围在0.0f~2.0f 之间，1 为保持原图的 RGBA 值。

- 使用 ColorMatrix 改变图片颜色的步骤，上面介绍了 ColorMatrix 设置图片的颜色，但是仅仅使用它还无法完成图片颜色的修改，需要配合 Canvas 和 Paint 使用，具体步骤如下：

	- 通过 Bitmap.createBitmap() 方法获得一个空白的 Bitmap 对象。
	- 定义 Paint，通过 Paint.setColorFilter(ColorFilter) 方法设置 Paint 的 RGBA 值。
	- 使用 Canvas.drawBitmap() 方法把原图使用定义的Paint画到空白的Bitmap对象上即可获得改变RGBA值后的图像。
	- 需要说明一下的是 Paint.setColorFilter() 方法传递的是一个 ColorFilter 对象，可以使用它的子类 ColorMatrixColorFilter 包装我们定义好的 ColorMatrix。

- 实例：改变图片 RGBA 
	- ColorMatrixActivity.java

			public class ColorMatrixActivity extends Activity implements SeekBar.OnSeekBarChangeListener{
			    private static final String TAG = "ColorMatrixActivity";
			
			    SeekBar sbRed,sbGreen,sbBlue,sbAlpha;
			    ImageView iv_show;
			    private Bitmap baseBitmap;
			    private Paint mPaint;
			    private Canvas canvas;
			    private Bitmap afterBitmap;
			
			    @Override
			    protected void onCreate(Bundle savedInstanceState) {
			        super.onCreate(savedInstanceState);
			        setContentView(R.layout.activity_colormatrix);
			        sbRed = (SeekBar) findViewById(R.id.sb_red);
			        sbGreen = (SeekBar) findViewById(R.id.sb_green);
			        sbBlue = (SeekBar) findViewById(R.id.sb_blue);
			        sbAlpha = (SeekBar) findViewById(R.id.sb_alpha);
			        iv_show = (ImageView) findViewById(R.id.iv_show);
			
			        sbRed.setOnSeekBarChangeListener(this);
			        sbGreen.setOnSeekBarChangeListener(this);
			        sbBlue.setOnSeekBarChangeListener(this);
			        sbAlpha.setOnSeekBarChangeListener(this);
			
			        baseBitmap = BitmapFactory.decodeResource(getResources(), R.drawable.water);
			
			        afterBitmap = Bitmap.createBitmap(baseBitmap.getWidth(),baseBitmap.getHeight(),
			                baseBitmap.getConfig());
			
			        canvas = new Canvas(afterBitmap);
			        mPaint = new Paint();
			    }
			
			    @Override
			    public void onProgressChanged(SeekBar seekBar, int i, boolean b) {
			        float red = sbRed.getProgress() / 128f;
			        float green = sbGreen.getProgress() / 128f;
			        float blue = sbBlue.getProgress() / 128f;
			        float alpha = sbAlpha.getProgress() / 128f;
			
			        System.out.println("argb:"+alpha+":"+red+":"+green+":"+blue);
			
			        float[] src = new float[]{
			                red,0,0,0,0,
			                0,green,0,0,0,
			                0,0,blue,0,0,
			                0,0,0,alpha,0,
			        };
			
			        ColorMatrix colorMatrix = new ColorMatrix();
			        colorMatrix.set(src);
			        mPaint.setColorFilter(new ColorMatrixColorFilter(colorMatrix));
			        canvas.drawBitmap(baseBitmap,new Matrix(),mPaint);
			        iv_show.setImageBitmap(afterBitmap);
			    }
			
			    @Override
			    public void onStartTrackingTouch(SeekBar seekBar) {
			
			    }
			
			    @Override
			    public void onStopTrackingTouch(SeekBar seekBar) {
			
			    }
			}

    - 效果图

		![](http://i.imgur.com/HhFQru8.png)
		
		![](http://i.imgur.com/iOzfDlQ.png)




### Shader

- Android 中提供了 Shader 类专门用来渲染图像以及一些几何图形，Shader 下面包括几个直接子类，分别是
- BitmapShader ,渲染图像
- ComposeShader ,混合渲染，可以和其它几个子类组合起来使用。
- LinearGradient ,线性渲染
- RadialGradient ,环形渲染
- SweepGradient ,梯度渲染。
- Shader 类的使用，都需要先构建 Shader 对象，然后通过 Paint 的 setShader 方法设置渲染对象，然后设置渲染对象，然后再绘制时使用这个 Paint 对象即可。当然，用不同的渲染时需要构建不同的对象。  

- 实例

	- ShaderView.java

			public class ShaderView extends View  {
			    /* 声明Bitmap对象 */
			    Bitmap mBitmap = null;
			    int mBitmapWidth = 0;
			    int mBitmapHeight = 0;
			
			    Paint mPaint = null;
			
			    /* Bitmap渲染 */
			    Shader mBitmapShader = null;
			
			    /* 线性渐变渲染 */
			    Shader mLinearGradient = null;
			
			    /* 混合渲染 */
			    Shader mComposeShader = null;
			
			    /* 唤醒渐变渲染 */
			    Shader mRadialGradient = null;
			
			    /* 梯度渲染 */
			    Shader mSweepGradient = null;
			
			
			    ShapeDrawable mShapeDrawable = null;
			
			    public ShaderView(Context context) {
			        super(context);
			        
			        mBitmap = BitmapFactory.decodeResource(getResources(), R.drawable.water);
			                /* 得到图片的宽度和高度 */
			        mBitmapWidth = mBitmap.getWidth();
			        mBitmapHeight = mBitmap.getHeight();
			
			                /* 创建BitmapShader对象 */
			        mBitmapShader = new BitmapShader(mBitmap, Shader.TileMode.REPEAT, Shader.TileMode.MIRROR);
			
			                /* 创建LinearGradient并设置渐变的颜色数组 说明一下这几天参数
			                 * 第一个 起始的x坐标
			                 * 第二个 起始的y坐标
			                 * 第三个 结束的x坐标
			                 * 第四个 结束的y坐标
			                 * 第五个 颜色数组
			                 * 第六个 这个也是一个数组用来指定颜色数组的相对位置 如果为null 就沿坡度线均匀分布
			                 * 第七个 渲染模式
			                 * */
			        mLinearGradient = new LinearGradient(0, 0, 100, 100,
			                new int[]{Color.RED, Color.GREEN, Color.BLUE, Color.WHITE},
			                null, Shader.TileMode.REPEAT);
			        /* 这里理解为混合渲染*/
			        mComposeShader = new ComposeShader(mBitmapShader, mLinearGradient, PorterDuff.Mode.DARKEN);
			
			        /* 构建RadialGradient对象，设置半径的属性 */
			        //这里使用了BitmapShader和LinearGradient进行混合
			        //当然也可以使用其他的组合
			        //混合渲染的模式很多，可以根据自己需要来选择
			        mRadialGradient = new RadialGradient(50, 200, 50,
			                new int[]{Color.GREEN, Color.RED, Color.BLUE, Color.WHITE},
			                null, Shader.TileMode.REPEAT);
			        /* 构建SweepGradient对象 */
			        mSweepGradient = new SweepGradient(30, 30, new int[]{Color.GREEN, Color.RED, Color.BLUE, Color.WHITE}, null);
			
			        mPaint = new Paint();
			    }
			
			    @Override
			    protected void onDraw(Canvas canvas) {
			        super.onDraw(canvas);
			
			        //将图片裁剪为椭圆形
			                /* 构建ShapeDrawable对象并定义形状为椭圆 */
			        mShapeDrawable = new ShapeDrawable(new OvalShape());
			
			                /* 设置要绘制的椭圆形的东西为ShapeDrawable图片 */
			        mShapeDrawable.getPaint().setShader(mBitmapShader);
			
			                /* 设置显示区域 */
			        mShapeDrawable.setBounds(0, 0, mBitmapWidth, mBitmapHeight);
			
			                /* 绘制ShapeDrawableQQ */
			        mShapeDrawable.draw(canvas);
			
			        //绘制渐变的矩形
			        mPaint.setShader(mLinearGradient);
			        canvas.drawRect(10, mBitmapHeight+50, getWidth()-50, mBitmapHeight+200, mPaint);
			
			        //显示混合渲染效果
			        mPaint.setShader(mComposeShader);
			        canvas.drawRect(20, mBitmapHeight+220, getWidth()-50, 300 + mBitmapHeight, mPaint);
			
			        //绘制环形渐变
			        mPaint.setShader(mRadialGradient);
			        canvas.drawCircle(100, 360 + mBitmapHeight, 50, mPaint);
			
			        //绘制梯度渐变
			        mPaint.setShader(mSweepGradient);
			        canvas.drawRect(40, mBitmapHeight+430, getWidth()-50, mBitmapHeight+530, mPaint);
			    }
			}		

	- 效果图

		![](http://i.imgur.com/26SmieX.png)


## Android 绘图技巧





> 参考：  
> Matrix:http://www.jianshu.com/p/a08e589ce5d4  
> Matrix:http://www.cnblogs.com/fighter/archive/2012/02/20/android-bitmap-drawable.html  
> Matrix:http://chroya.iteye.com/blog/712078  
> Matrix:http://zensheno.blog.51cto.com/2712776/513652  
> Path:http://www.cnblogs.com/tt_mc/archive/2012/12/07/2807518.html  
> 绘图：http://wpf814533631.iteye.com/blog/1847661  