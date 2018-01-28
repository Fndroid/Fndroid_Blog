# Android开发学习之路-Palette颜色提取工具类使用

视频（要FQ）：https://www.youtube.com/watch?v=5u0dtzXL3PQ

Palette是一个在support-v7包中的一个颜色提取工具类，用法比较简单，而且是谷歌官方提供，有必要了解一下。下面是效果图，ToolBar的颜色随图片改变

![](http://images2015.cnblogs.com/blog/852227/201602/852227-20160219151535909-1437194347.gif)

这个滑动的效果怎么做就是上一篇的问题了。这里只说Palette，可以看到，在图片收缩上去之后我们需要给Toolbar一个颜色，因为我们有两张图片，而我们的效果是根据这两张图片给Toolbar设置合适的颜色，这样更加美观。

用法：

    
    
    1    // Synchronous
    2    Palette p = Palette.from(bitmap).generate();
    3   
    4    // Asynchronous
    5    Palette.from(bitmap).generate(new PaletteAsyncListener() {
    6        public void onGenerated(Palette p) {
    7            // Use generated instance
    8        }
    9    });

可以看到，构造是支持同步和异步的，不过貌似这两个构造的方式已经被遗弃了，经过查阅API可以知道被Builder代替了，代码如下：

    
    
    Palette.Builder bd = new Palette.Builder(BitmapFactory.decodeResource(getResources(), imageId));
    
    
    Palette palette = bd.generate();



当我们构造出来一个Palette对象之后，可以通过getXXXColor(int
defaultColor)的方法（需要传入一个默认的颜色）来获得相应的颜色，然后我们在给对应的控件设置颜色即可

上面的XXX可以对应为：

Vibrant            强烈的  
Vibrant Dark  
Vibrant Light  
Muted             柔和的  
Muted Dark  
Muted Light

然后添加以下gradle的依赖

    
    
    compile 'com.android.support:palette-v7:23.1.1'



最后需要说明一点， **Palette解析出来的颜色有时候是会失败的** ，比如下图，如果颜色未能识别出来，就使用我们在getXXXColor(int
defaultColor)函数中设定的默认颜色

![](http://images2015.cnblogs.com/blog/852227/201602/852227-20160219154827050-1180741943.png)

