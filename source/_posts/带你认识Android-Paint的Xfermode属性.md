---
title: 带你认识Android-Paint的Xfermode属性
date: 2018-01-05 14:21:26
tags: Android
---
###### xfermode是什么
> Xfermode is the base class for objects that are called to implement custom "transfer-modes" in the drawing pipeline. The static function Create(Modes) can be called to return an instance of any of the predefined subclasses as specified in the Modes enum. When an Xfermode is assigned to an Paint, then objects drawn with that paint have the xfermode applied.
<!--more-->
上面是Google官方的解释，大概意思是
> Xfermode是在绘图通道中自定义“传输模式”的基类。静态函数创建可以调用或者返回任意作为模式枚举指定的预定义子类实例。当Xfermode分配给Paint，然后绘制对象的Paint就具备了所添加的xfermode。

呵呵，傻雕，根本不知道你在说啥......

哈，没关系，没关系的，你走吧！

##### paint.setXfermode方法熟悉吗？
什么？熟悉，好吧，你可以滚了。我不爽了！
其实，xfermode也叫做图片混排模式，学过高中的数学没，就是两个数据取交集并集等的操作，这里是对图片的处理。
嗯，等会再说怎么用，急个鸡儿。

来看看官方的资料：
![儿砸.png](http://upload-images.jianshu.io/upload_images/4463150-29e60b2028ccb330.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

妈卖批，怎么只剩下一个儿子了，传说中它有3个儿砸，老大是AvoidXfermode，老二是PixelXorXfermode，老三才是PorterDuffXfermode. 前两个都可能光荣牺牲了，剩下老三最得力。感性趣的自行Google前两个的光荣事迹。

##### 我们来看看三儿崽PorterDuffXfermode
![狗造方法.png](http://upload-images.jianshu.io/upload_images/4463150-fc5256ef4f3971e1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
可以看到只有一个构造方法，参数也只有一个，PorterDuff.Mode。这个就是图片混排模式，Android最新的api提供了18中混排模式，[地址这里](https://developer.android.com/reference/android/graphics/PorterDuff.Mode.html)，官网的图片太大，我先拿其他童鞋的顶住先
![盗图.png](http://upload-images.jianshu.io/upload_images/4463150-9cff7cd85697790b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

这里的src是源图层，dst是目标图层。经过18中不同的混排模式混合，得到不一样的效果，下面对这些效果一一讲解。

```
public class XfermodeView extends View {

    private Xfermode mXfermode;
	private Bitmap srcBitmap,dstBitmap;
	private Paint paint;

    public XfermodeView(Context context) {
        this(context,null);
    }

    public XfermodeView(Context context, @Nullable AttributeSet attrs) {
        super(context, attrs);
        init();
    }

    private void init() {
        mXfermode = new PorterDuffXfermode(PorterDuff.Mode.DST_OVER);
        srcBitmap = makeSrcBitmap();
        dstBitmap = makeDstBitmap();
        initPaint();
    }

    private void initPaint() {
        paint = new Paint();
        paint.setAntiAlias(true);
        paint.setStyle(Paint.Style.FILL);
        paint.setFilterBitmap(false);
    }

    private Bitmap makeDstBitmap() {
        Bitmap bitmap = Bitmap.createBitmap(800,800, Bitmap.Config.ARGB_8888);
        Canvas canvas = new Canvas(bitmap);
        Paint paint = new Paint();
        paint.setAntiAlias(true);
        paint.setColor(Color.parseColor("#269647"));
        canvas.drawCircle(300,300,200,paint);
        return bitmap;
    }

    private Bitmap makeSrcBitmap() {
        Bitmap bitmap = Bitmap.createBitmap(800,800, Bitmap.Config.ARGB_8888);
        Canvas canvas = new Canvas(bitmap);
        Paint paint = new Paint();
        paint.setAntiAlias(true);
        paint.setColor(Color.parseColor("#E08F53"));
        canvas.drawRect(200,200,600,600,paint);
        return bitmap;
    }

    @Override
    protected void onDraw(Canvas canvas) {
        int sc = 0;
        if (android.os.Build.VERSION.SDK_INT >= android.os.Build.VERSION_CODES.LOLLIPOP) {
            sc = canvas.saveLayer(0,0,800,800,paint);
        }

        canvas.drawBitmap(dstBitmap,0,0,paint);
        paint.setXfermode(mXfermode);

        canvas.drawBitmap(srcBitmap,0,0,paint);
        paint.setXfermode(null);
        canvas.restoreToCount(sc);
    }
}
```
上面的是代码，我们通过更改xfermode的属性来看看不同的效果显示。
![PorterDuff.Mode.DST.png](http://upload-images.jianshu.io/upload_images/4463150-de10b18c44359268.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![PorterDuff.Mode.SRC.png](http://upload-images.jianshu.io/upload_images/4463150-a2613c842b40750f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


![PorterDuff.Mode.DST_OVER.png](http://upload-images.jianshu.io/upload_images/4463150-64918eb496238a9a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![PorterDuff.Mode.SRC_OVER.png](http://upload-images.jianshu.io/upload_images/4463150-8057441c391736f4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![PorterDuff.Mode.SRC_ATOP.png](http://upload-images.jianshu.io/upload_images/4463150-73377589966cb910.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![PorterDuff.Mode.SRC_IN.png](http://upload-images.jianshu.io/upload_images/4463150-d98a33ff7d04647a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![PorterDuff.Mode.SRC_OUT.png](http://upload-images.jianshu.io/upload_images/4463150-19f0cd732d1bcb83.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![PorterDuff.Mode.DST_ATOP.png](http://upload-images.jianshu.io/upload_images/4463150-4710e57d4e04c703.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![PorterDuff.Mode.DST_IN.png](http://upload-images.jianshu.io/upload_images/4463150-f9dac44b2207156c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![PorterDuff.Mode.DST_OUT.png](http://upload-images.jianshu.io/upload_images/4463150-1b3ee6e9c1ec9dd9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![PorterDuff.Mode.SCREEN.png](http://upload-images.jianshu.io/upload_images/4463150-b5fc386a0a51bba8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![PorterDuff.Mode.ADD.png](http://upload-images.jianshu.io/upload_images/4463150-9bad95f81dfcff9d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![PorterDuff.Mode.CLEAR.png](http://upload-images.jianshu.io/upload_images/4463150-a85efb8c9959a3fb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![PorterDuff.Mode.DARKEN.png](http://upload-images.jianshu.io/upload_images/4463150-79ee2fbeca3973a2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![PorterDuff.Mode.LIGHTEN.png](http://upload-images.jianshu.io/upload_images/4463150-52e3b310cc791623.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![PorterDuff.Mode.MULTIPLY.png](http://upload-images.jianshu.io/upload_images/4463150-6bde01c0438a037b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![PorterDuff.Mode.OVERLAY.png](http://upload-images.jianshu.io/upload_images/4463150-558b356c54374ea4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![PorterDuff.Mode.XOR.png](http://upload-images.jianshu.io/upload_images/4463150-55c465a68ed43983.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

##### 应用于哪些方面
额，不太好说吧，需要这种骚操作的话都能用上，举个常见的栗子，圆形头像的处理，就可以用这种方法来完成。

下面的完整的代码，如果需要用的话还是需要做一些处理的，不是很完善
```
public class RoundImageView extends ImageView {

	private Xfermode xfermode;
	private Paint mPaint;

	public RoundImageView(Context context) {
		this(context, null);
	}

	public RoundImageView(Context context, @Nullable AttributeSet attrs) {
		this(context, attrs, 0);
	}

	public RoundImageView(Context context, @Nullable AttributeSet attrs, int defStyleAttr) {
		super(context, attrs, defStyleAttr);
		initPaint();
	}

	@Override
	protected void onDraw(Canvas canvas) {
		Drawable drawable = getDrawable();
		if (drawable == null) {
			super.onDraw(canvas);
			return;
		}
		int widthAndHeightSize = Math.min(getWidth(), getHeight());
		int left = (getWidth() - widthAndHeightSize) / 2;
		int height = (getHeight() - widthAndHeightSize) / 2;

		int layer = canvas.saveLayer(left, height, getWidth() - left, getHeight() - height, mPaint, Canvas.ALL_SAVE_FLAG);

		canvas.drawBitmap(createDstBitmap(widthAndHeightSize), left, height, mPaint);// 目标dst
		mPaint.setXfermode(xfermode);

		canvas.drawBitmap(convertDrawable2BitmapByCanvas(drawable),0,0,mPaint);//源src
		mPaint.setXfermode(null);
		canvas.restoreToCount(layer);
	}

	public Bitmap convertDrawable2BitmapByCanvas(Drawable drawable) {
		Bitmap bitmap = Bitmap.createBitmap(drawable.getIntrinsicWidth(), drawable.getIntrinsicWidth(),
				drawable.getOpacity() != PixelFormat.OPAQUE ? Bitmap.Config.ARGB_8888 : Bitmap.Config.RGB_565);
		Canvas canvas = new Canvas(bitmap);
		drawable.setBounds(0, 0, drawable.getIntrinsicWidth(), drawable.getIntrinsicHeight());
		drawable.draw(canvas);
		return bitmap;
	}

	private Bitmap createDstBitmap(int widthAndHeight) {
		Bitmap bitmap = Bitmap.createBitmap(widthAndHeight, widthAndHeight, Bitmap.Config.ARGB_8888);
		Canvas canvas = new Canvas(bitmap);
		Paint paint = new Paint();
		paint.setAntiAlias(true);
		paint.setColor(Color.RED);
		paint.setStyle(Paint.Style.FILL);
		canvas.drawCircle(widthAndHeight / 2, widthAndHeight / 2, widthAndHeight / 2, paint);
		return bitmap;
	}

	private void initPaint() {
		mPaint = new Paint();
		mPaint.setAntiAlias(true);
		mPaint.setFilterBitmap(false);
		mPaint.setStyle(Paint.Style.FILL);
		xfermode = new PorterDuffXfermode(PorterDuff.Mode.SRC_IN);
	}
}

```

恩，你以为就这样完了吗，对没错，就是这样玩。