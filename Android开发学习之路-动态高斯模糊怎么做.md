# Android开发学习之路-动态高斯模糊怎么做

什么是高斯模糊？

> **高斯模糊** （英语：Gaussian Blur），也叫高斯平滑，是在Adobe
Photoshop、GIMP以及Paint.NET等图像处理软件中广泛使用的处理效果，通常用它来减少图像噪声以及降低细节层次。这种模糊技术生成的图像，其视觉效果就像是经过一个半透明屏幕在观察图像，这与镜头焦外成像效果散景以及普通照明阴影中的效果都明显不同。

什么？看不明白？没关系，我也看不明白，维基百科复制回来的嘛。我们直接放一些图片来了解以下这个高斯模糊是怎么样的。因为高斯模糊在iOS中最常见，这里抓了几张iOS网易云的图片：

![](http://images2015.cnblogs.com/blog/852227/201608/852227-20160803202633403-940757137.jpg)![](http://images2015.cnblogs.com/blog/852227/201608/852227-20160803202651997-1292086399.jpg)

可以看到这个界面中的背景，其实就是通过图1中间那个小图片模糊得到的，这样做的好处就是整体性很好，并且不会因为图片过渡突兀而影响界面内容的阅读。

那么，究竟在Android上怎么去实现这个效果呢？这里推荐使用官方提供在Support Library中的一个工具来做，就是
**RenderScript**
。这个RenderScript的功能其实不止有这一个，而其他的一些功能，可以在[官方文档](https://developer.android.com/guide/topics/renderscript/compute.html#writing-
an-rs-kernel)中阅读学习，这里不给出。

使用这个工具的原因其实很简单，就是性能。因为涉及到绘图，所以如果性能不行，那么无论对于高质量图片或者是变化较多的需求都是很吃力的，而这个工具则会充分发挥设备的计算能力（CPU和GPU）来进行计算，并且是使用C99衍生语言进行脚本编写的，相较于Java性能是大大的提升。

说到这里，有些同学就开始方了，C99衍生？What？这个不需要担心，对于高斯模糊这个实现，Google官方已经给出了对应的解决方案，我们并不需要编写对应的脚本就可以使用了，所以无需担心。



我们把整个问题分为两个部分：①高斯模糊实现；②动态高斯模糊实现



① 高斯模糊实现

首先要说明，我们要使用Support Library，所以版本是有要求的：

  * Android SDK Tools 版本必须大于等于22.2
  * Android SDK Build-tools 版本必须大于等于18.1.0

如果没有达到，请使用SDK Manager升级一下。

接着创建我们的工程，并且在对应Module（默认创建的是app）的build.gradle文件中加入如下代码：

    
    
    defaultConfig {
            ...
            renderscriptTargetApi 18
            renderscriptSupportModeEnabled true
        }

renderscriptTargetApi：这个一般和App支持的最低版本相同即可。

    
    
    package com.fndroid.renderscriptdemo;
    
    import android.graphics.Bitmap;
    import android.graphics.BitmapFactory;
    import android.os.Bundle;
    import android.renderscript.Allocation;
    import android.renderscript.Element;
    import android.renderscript.RenderScript;
    import android.renderscript.ScriptIntrinsicBlur;
    import android.support.v7.app.AppCompatActivity;
    import android.widget.ImageView;
    
    public class MainActivity extends AppCompatActivity {
        private ImageView mImageView;
        private Bitmap sampleImg;
        private Bitmap gaussianBlurImg;
    
        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
            mImageView = (ImageView) findViewById(R.id.iv);
            sampleImg = BitmapFactory.decodeResource(getResources(), R.drawable.icon); // 获取原图
            gaussianBlurImg = blur(sampleImg, 25f);
            mImageView.setImageBitmap(gaussianBlurImg);
        }
    
        private Bitmap blur(Bitmap bitmap,float radius) {
            Bitmap output = Bitmap.createBitmap(bitmap); // 创建输出图片
            RenderScript rs = RenderScript.create(this); // 构建一个RenderScript对象
            ScriptIntrinsicBlur gaussianBlue = ScriptIntrinsicBlur.create(rs, Element.U8_4(rs)); // 创建高斯模糊脚本
            Allocation allIn = Allocation.createFromBitmap(rs, bitmap); // 创建用于输入的脚本类型
            Allocation allOut = Allocation.createFromBitmap(rs, output); // 创建用于输出的脚本类型
            gaussianBlue.setRadius(radius); // 设置模糊半径，范围0f<radius<=25f
            gaussianBlue.setInput(allIn); // 设置输入脚本类型
            gaussianBlue.forEach(allOut); // 执行高斯模糊算法，并将结果填入输出脚本类型中
            allOut.copyTo(output); // 将输出内存编码为Bitmap，图片大小必须注意
            rs.destroy(); // 关闭RenderScript对象，API>=23则使用rs.releaseAllContexts()
            return output;
        }
    }

这里说明都注释在代码中了。需要了解的是RenderScript有两个版本，分别是：

  * android.renderscript
  * android.support.v8.renderscript

上面代码使用了第一个，第二个的用法类似，可以自行尝试。

**我们来理一下思路，因为RenderScript是依赖于Script的，而上文也说到了，Script是由C99衍生语言编写，而代码中的ScriptIntrinsicBlur就是对应于高斯模糊算法的脚本。而Allocation对象则是将Java中的对象转换为Script脚本所需类型的帮手，代码中创建了两个Allocation对象分别用来充当输入和输出。接着设置了高斯模糊的半径（radius）。当调用forEach时，脚本会被执行，并且将执行结果填入输出对应的Allocation中，最后调用copyTo来转换为Bitmap对象返回。**

效果图：

![](http://images2015.cnblogs.com/blog/852227/201608/852227-20160803210836528-1382978843.png)



② 动态高斯模糊

很多时候，我们可能会需要一个图片以不同的模糊程度展现出来。你可能已经注意到上面方法中的模糊半径了，我们可以做一个实验，就是通过一个SeekBar来动态改变这个值看看效果：

![](http://images2015.cnblogs.com/blog/852227/201608/852227-20160803212946700-1525024003.gif)

由动图可以看到，我们拖动SeekBar的时候，SeekBar已经跟不上我们的拖动了。这是为什么？原因就是这个渲染工具虽然性能比较优异，但是如果图片的质量和尺寸都较高的时候，我们直接进行修改模糊半径重新渲染的做法往往时不可取的。

这里提出一个解决方案，这个Idea来自于郭大今天的推文作者"湫水长天"。

做法是，
**先创建一张模糊的图片加载在ImageView中，接着在这个ImageView的上面再放置一个加载原图ImageView，使用FrameLayout可以让这两个ImageView重叠再一起，接着当我们需要动态改变模糊程度的时候，改变上层的ImageView的BitmapAlpha就可以了**
。我们先看看效果图：

![](http://images2015.cnblogs.com/blog/852227/201608/852227-20160803213913856-2131277246.gif)

使用了这个方法，滑动起来就会比较流畅了。（GIF加载完才是会是正常速度哦）

这里给一下代码参考吧：

    
    
    public class MainActivity extends AppCompatActivity implements SeekBar.OnSeekBarChangeListener {
        private ImageView mImageView;
        private ImageView mImageViewCover;
        private Bitmap sampleImg;
        private Bitmap gaussianBlurImg;
        private SeekBar mSeekBar;
    
        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
            mImageView = (ImageView) findViewById(R.id.iv);
            mSeekBar = (SeekBar) findViewById(R.id.sb);
            mImageViewCover = (ImageView) findViewById(R.id.iv_cover);
            sampleImg = BitmapFactory.decodeResource(getResources(), R.drawable.icon); // 获取原图
            gaussianBlurImg = blur(sampleImg, 25f);
            mImageView.setImageBitmap(gaussianBlurImg);
            mSeekBar.setOnSeekBarChangeListener(this);
        }
    
        private Bitmap blur(Bitmap bitmap, float radius) {
            Bitmap output = Bitmap.createBitmap(bitmap); // 创建输出图片
            RenderScript rs = RenderScript.create(this); // 构建一个RenderScript对象
            ScriptIntrinsicBlur gaussianBlue = ScriptIntrinsicBlur.create(rs, Element.U8_4(rs)); //
            // 创建高斯模糊脚本
            Allocation allIn = Allocation.createFromBitmap(rs, bitmap); // 开辟输入内存
            Allocation allOut = Allocation.createFromBitmap(rs, output); // 开辟输出内存
            gaussianBlue.setRadius(radius); // 设置模糊半径，范围0f<radius<=25f
            gaussianBlue.setInput(allIn); // 设置输入内存
            gaussianBlue.forEach(allOut); // 模糊编码，并将内存填入输出内存
            allOut.copyTo(output); // 将输出内存编码为Bitmap，图片大小必须注意
            rs.destroy(); // 关闭RenderScript对象，API>=23则使用rs.releaseAllContexts()
            return output;
        }
    
        @Override
        public void onProgressChanged(SeekBar seekBar, int progress, boolean fromUser) {
            int alpha = 255 - progress;
            mImageViewCover.setImageAlpha(alpha);
        }
    
        @Override
        public void onStartTrackingTouch(SeekBar seekBar) {
    
        }
    
        @Override
        public void onStopTrackingTouch(SeekBar seekBar) {
    
        }
    }

 布局文件：

    
    
    <?xml version="1.0" encoding="utf-8"?>
    <LinearLayout
        xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:app="http://schemas.android.com/apk/res-auto"
        xmlns:tools="http://schemas.android.com/tools"
        android:id="@+id/activity_main"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:orientation="vertical"
        tools:context="com.fndroid.renderscriptdemo.MainActivity">
    
        <FrameLayout
            android:layout_width="match_parent"
            android:layout_height="0dp"
            android:layout_weight="1">
    
            <ImageView
                android:id="@+id/iv"
                android:layout_width="match_parent"
                android:layout_height="match_parent"/>
    
            <ImageView
                android:id="@+id/iv_cover"
                android:layout_width="match_parent"
                android:layout_height="match_parent"
                android:src="@drawable/icon"/>
    
        </FrameLayout>
    
        <SeekBar
            android:id="@+id/sb"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:max="255"/>
    
    </LinearLayout>



如发现有误，请指出，感谢。

