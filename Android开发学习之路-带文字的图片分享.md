# Android开发学习之路-带文字的图片分享

有用过微信分享SDK的都应该知道，微信分享到朋友圈的时候是不能同时分享图片和文字的，只要有缩略图，那么文字就不会生效。那么问题就来了，如果我们想把APP内的某些内容连带图片一起分享到微信，是不是没办法了呢？

再想想我们常用的网易云音乐，允许我们把歌词连带着歌曲的图片拼在一起变成一张图，我们再把这张图片分享出去就好了。

**那么，本篇的内容就是动手做一个带文字的图片。**

这里也记录下上下文，因为做了一个失物招领的App，当有人上交了失物之后，可以将这个消息分享出去，这个消息内容有物品的信息和图片，而微信SDK始终无法做到，就想着把物品信息嵌入到图片中分享出去，先放一个效果图：

![](http://images2015.cnblogs.com/blog/852227/201606/852227-20160630151810109-688194757.jpg)



这个分享出去的图片很简单，上面是图片，下面是文字组合在一起。



**先要知道，方案的原理是通过操作一个以Bitmap为基础的Canvas来做到的，思路很简单：**

**① 让画布作用在Bitmap上**

**② 在画布的上方绘制拍摄所得的图片**

**③ 在②所绘制的图片下面绘制文字**



第一步很简单，我们只需要构造一个Bitmap并且装载到Canvas中就可以了，假设拍摄得到的图片名为bitmap，则代码如下：

    
    
    Bitmap.Config config = bitmap.getConfig();
    Bitmap shareBitmap = Bitmap.createBitmap(bitmap.getWidth(), bitmap.getHeight(), config);
    Canvas canvas = new Canvas(shareBitmap);

到这里我们就要思考了，这个图片的宽高应该怎么设置比较合理呢？上面的代码设置为跟拍摄所得图片一样，也就是说，如果再需要添加文字，文字只能显示在图片上。这个时候，如果图片的颜色比较丰富，那么文字叠在上面就会很难看得清楚了。按照我上面图片的做法，是在拍摄图片的下面增加一些空间来绘制文字，这里要设置的高度应该要更加大一点。而究竟要多大我们等下再讨论。



第二步是在画布中绘制拍摄所得的图片，这里就很简单了，直接有这样的代码：

    
    
    canvas.drawBitmap(bitmap, 0, 0, paint);



接着是第三步，也是最难的地方。这里不能直接在canvas中直接调用drawText方法来绘制文字！为什么？因为我们的文字内容有可能比图片的宽度要大，当文字比图片更宽的时候，使用drawText是无法让文字内容换行的，这样文字就被截断了。

解决的方案是使用TextPaint这个Paint的子类。这个类还需要配合StaticLayout来绘制文字，我们看看它的用法：

    
    
    Paint paint = new Paint();
    paint.setColor(Color.BLACK); // 画笔颜色
    TextPaint textpaint = new TextPaint(paint);
    textpaint.setTextSize(textSize); // 文字大小
    textpaint.setAntiAlias(true); // 抗锯齿
    StaticLayout title_layout = new StaticLayout(title.getText().toString(), textpaint, sourceBitmapWidth, Layout.Alignment.ALIGN_CENTER, 1f, 1f, true);

直接通过我们的Paint对象来创建一个TextPaint，接着设置抗锯齿和文字大小。接着创建一个StaticLayout对象，构造方法需要传入的参数分别是：文字内容、TextPaint对象、文本宽度、对齐方式、行距倍数、行距加数和是否包含内边距。这里比较重要的地方是设置
**文本宽度** ，当文本宽度比这个值大的时候就会自动换行。

当我们构造好了这个StaticLayout之后，就可以对画布进行定位，然后将文字绘制出来：

    
    
    canvas.translate(0, sourceBitmapHeight); // 移动位置到图片的下面
    title_layout.draw(canvas); // 在画布中绘制文字

 完成了这几步之后，Canvas中的Bitmap就会有图片和文字了。



但是我们的问题其实还没有解决。

**①
这个分享出去的Bitmap的高度究竟是多少呢？上面我们跳过了这个问题，其实已经有答案了，我们可以让它的高度为图片的内容加上我们创建的StaticLayout的高度就可以了。这样图片的高度会跟随文字内容的多少变化。获取StaticLayout的高度比较简单：**

    
    
    title_layout.getHeight()



**②
这个时候，如果我们直接在App中显示这个图片，是没有什么问题的，但是如果我们把图片分享到微信，你会发现，图片的文字部分完全变成了黑色，连文字都看不到了。这里的解决办法也很简单，在绘制的时候，先给整个图片绘制一个白色的背景：**

    
    
    canvas.drawColor(Color.WHITE);



 这里就基本完成了，代码也给出来大家参考下吧：

    
    
    // 拍摄所得的图片为imageBitmap
        private Bitmap getShareingBitmap(int textSize) {
            Bitmap.Config config = imageBitmap.getConfig();
            int sourceBitmapHeight = imageBitmap.getHeight();
            int sourceBitmapWidth = imageBitmap.getWidth();
            Paint paint = new Paint();
            paint.setColor(Color.BLACK); // 画笔颜色
            TextPaint textpaint = new TextPaint(paint);
            textpaint.setTextSize(textSize); // 文字大小
            textpaint.setAntiAlias(true); // 抗锯齿
            StaticLayout title_layout = new StaticLayout(title.getText().toString(), textpaint,
                    sourceBitmapWidth, Layout.Alignment.ALIGN_CENTER, 1f, 1f, true);
            StaticLayout desc_layout = new StaticLayout("物品描述："+description.getText().toString(), textpaint,
                    sourceBitmapWidth, Layout.Alignment.ALIGN_NORMAL, 1f, 1f, true);
            StaticLayout phone_layout = new StaticLayout("联系电话："+phone.getText().toString(), textpaint,
                    sourceBitmapWidth, Layout.Alignment.ALIGN_NORMAL, 1f, 1f, true);
            Bitmap share_bitmap = Bitmap.createBitmap(sourceBitmapWidth, sourceBitmapHeight +
                    title_layout.getHeight() + desc_layout.getHeight() + phone_layout.getHeight(),
                    config);
            Canvas canvas = new Canvas(share_bitmap);
            canvas.drawColor(Color.WHITE);
            canvas.drawBitmap(imageBitmap, 0, 0, paint); // 绘制图片
            canvas.translate(0, sourceBitmapHeight);
            title_layout.draw(canvas);
    
            canvas.translate(0, title_layout.getHeight());
            phone_layout.draw(canvas);
    
            canvas.translate(0, phone_layout.getHeight());
            desc_layout.draw(canvas);
            return share_bitmap;
        }

 先计算所有文字内容的高度，然后构建图片的大小，绘制白色背景，绘制拍摄图片，在拍摄图片下绘制文字。

