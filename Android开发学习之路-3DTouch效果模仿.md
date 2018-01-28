# Android开发学习之路-3DTouch效果模仿

3D Touch是什么效果的大家应该都知道了。什么？不知道，那也没办法呀，我也没有iPhone 6s演示给你看的。

本篇博客要做的效果图：

![](http://images2015.cnblogs.com/blog/852227/201608/852227-20160808103914918-716278800.png)

来个低质量动图：

![](http://images2015.cnblogs.com/blog/852227/201608/852227-20160808100752934-1267782333.gif)

这个动图效果不是很好，实际上模糊效果应该是像上面第一张图那样的，后面会放出代码，有兴趣的可以试着运行一下看看效果。



先说一下思路，我们要实现这个效果其实只需要掌握几个东西：

  * 屏幕截图
  * [模糊（高斯模糊）](http://www.cnblogs.com/Fndroid/p/5734696.html)
  * 添加视图
  * 弹出动画
  * 处理长按事件
  * 优化（模糊速度和强度）

流程：当用户长按一个Item的时候，我们先截取一张当前屏幕的图片，接着将这张图片进行压缩后再进行高斯模糊，再覆盖在整个布局上面（包括覆盖Toolbar），这样界面模糊的效果就出来了。接着我们动态的向界面添加一个CardView来呈现我们的Item布局，这个CardView要出现在我们点击的对应的Item上。最后添加一个对应3D
Touch弹出的动画即可。



接下来我们一步一步的完成整个流程：

① 屏幕截图

这一部分相对比较简单，因为我们要得到当前屏幕显示内容的Bitmap是有现成方法的，代码如下：

    
    
        private Bitmap getScreenImage() { // 截取一张屏幕的图片
            View view = root;
            view.setBackgroundColor(Color.WHITE);
            view.setDrawingCacheEnabled(true);
            view.buildDrawingCache();
            Bitmap bitmap = Bitmap.createBitmap(view.getDrawingCache(), 0, 0, view.getWidth(), view
                    .getHeight());
            view.destroyDrawingCache();
            return bitmap;
        }

先说一下布局，这里的布局文件如下所示：

    
    
    <?xml version="1.0" encoding="utf-8"?>
    <FrameLayout
        xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:app="http://schemas.android.com/apk/res-auto"
        xmlns:tools="http://schemas.android.com/tools"
        android:id="@+id/activity_main"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        tools:context="com.fndroid.threedtouchdemo.MainActivity">
    
        <LinearLayout
            android:id="@+id/root"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:orientation="vertical">
    
            <android.support.v7.widget.Toolbar
                android:id="@+id/toolbar"
                android:layout_width="match_parent"
                android:layout_height="?attr/actionBarSize"
                android:background="@color/colorPrimary"
                app:title="@string/app_name"
                app:titleTextColor="#fff"/>
    
            <ListView
                android:id="@+id/lv"
                android:layout_width="match_parent"
                android:layout_height="match_parent"
                tools:listitem="@layout/item"/>
        </LinearLayout>
    
        <ImageView
            android:id="@+id/cover"
            android:layout_width="match_parent"
            android:layout_height="match_parent"/>
    
        <android.support.v7.widget.CardView
            android:id="@+id/cv"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:translationZ="5dp"
            app:cardCornerRadius="10dp"/>
    
    </FrameLayout>

可以看到我们最外层用了一个FrameLayout，原因是我们需要往整个布局中覆盖一个高斯模糊了的截图，可以看到最下面的ImageView就是用来做模糊效果的，最开始我们只需要给它的ImageAlpha设置为0让其透明即可。最下面的CardView则是弹出的控件，这个等下再说。我们截图的root是FrameLayout下的LinearLayout，因为我们需要让ToolBar也模糊化。



② 高斯模糊

这个在我的上一篇博客--[动态高斯模糊怎么做](http://www.cnblogs.com/Fndroid/p/5734696.html)中已经说过了，可以进行参考，这个给出对应的代码：

    
    
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

 配置对应Module的build.gradle文件：

    
    
        defaultConfig {
            ...
            renderscriptTargetApi 18
            renderscriptSupportModeEnabled true
        }



 ③ 弹出视图

这个视图我们需要将Item的View添加到CardView中，并且让CardView的位置在对应Item位置之上。

    
    
        // 显示对应的卡片
        private void showView(int position, View view){
            newView = LayoutInflater.from(this).inflate(R.layout.item, null); // 加载Itme的布局
            TextView tv = (TextView) newView.findViewById(R.id.item_tv); // 获取对应控件
            tv.setText(data.get(position).get("name")); // 将Item对应控件的值设置回去
            newView.setBackgroundColor(Color.WHITE);
            // 设置卡片的样式，位置通过margintop来计算
            FrameLayout.LayoutParams params = new FrameLayout.LayoutParams(view.getWidth() - 30, view.getHeight());
            params.topMargin = (int) (view.getY() + mToolbar.getHeight()); // 卡片的marginTop设置为item的Y加上toolbar的高度
            params.leftMargin = 15;
            params.rightMargin = 15;
            mCardView.setVisibility(View.VISIBLE);
            mCardView.setLayoutParams(params);
            mCardView.addView(newView, view.getLayoutParams()); // 把View加载进CardView，并设置样式为item样式
            startAnimate(mCardView); // 播放动画
        }

这里不能直接把item的view加载进CardView中，因为item的View已经有父布局了，会抛异常。解决办法是重新根据布局映射一个，然后填充数据进去。接着设定卡片的位置信息和大小信息，因为我们要让卡片显示在对应Item上面。



④ 弹出动画

这是比较简单的部分了，我们直接使用PropertyValuesHolder来做一个弹出和收缩的动，因为我们需要同时缩放X和Y，当然也可以用其他方法，代码如下：

    
    
        private void startAnimate(CardView cardView) {
            PropertyValuesHolder pyhScaleX = PropertyValuesHolder.ofFloat("scaleX", 0.1f, 1.05f);
            PropertyValuesHolder pyhScaleY = PropertyValuesHolder.ofFloat("scaleY", 0.1f, 1.05f);
            ObjectAnimator animator_out = ObjectAnimator.ofPropertyValuesHolder(mCardView, pyhScaleX,
                    pyhScaleY); // 同时缩放X和Y
            animator_out.setInterpolator(new AccelerateDecelerateInterpolator());
            animator_out.setDuration(350);
            PropertyValuesHolder pyhScaleX2 = PropertyValuesHolder.ofFloat("scaleX", 1.05f, 1f);
            PropertyValuesHolder pyhScaleY2 = PropertyValuesHolder.ofFloat("scaleY", 1.05f, 1f);
            ObjectAnimator animator_in = ObjectAnimator.ofPropertyValuesHolder(mCardView, pyhScaleX2,
                    pyhScaleY2);
            animator_in.setInterpolator(new AccelerateDecelerateInterpolator());
            animator_in.setDuration(100);
    
            AnimatorSet animatorSet = new AnimatorSet();
            animatorSet.playSequentially(animator_out, animator_in); // 按顺序执行两个动画
            animatorSet.start();
        }



⑤ 监听长按事件

因为这里只是使用了ListView来简化这个内容，可以直接通过已有监听器来实现：

    
    
     1     @Override
     2     public boolean onItemLongClick(AdapterView<?> parent, View view, int position, long id) {
     3         mCover.setImageBitmap(blur(blur(getScreenImage(), 25f),25f)); // 对截取的图片两次高斯模糊
     4         mCover.setVisibility(View.VISIBLE);
     5         mCover.setImageAlpha(0);
     6         new Thread(new Runnable() {
     7             int progress = 50;
     8 
     9             @Override
    10             public void run() {
    11                 while (progress < 255) {
    12                     try {
    13                         Thread.sleep(1);
    14                     } catch (InterruptedException e) {
    15                         e.printStackTrace();
    16                     }
    17                     Message msg = new Message();
    18                     msg.obj = progress++;
    19                     mHandler.sendMessage(msg);
    20                 }
    21             }
    22         }).start();
    23         showView(position, view);
    24         return true;
    25     }

这里的第3行中调用了两次blur方法来对图片进行高斯模糊
，如果看过上一篇博客，每次高斯模糊的最大模糊半径是25，如果要做到向iOS那也的模糊效果，25是不够的，所以可以对模糊出来的图片再模糊化一次，对比图（左边为2次模糊，右边1次）：

![](http://images2015.cnblogs.com/blog/852227/201608/852227-20160805100305622-146852294.png)
![](http://images2015.cnblogs.com/blog/852227/201608/852227-20160805100322278-1817233266.png)



⑥ 优化

但是实际上， **对于一个分辨率比较高的手机，截取的屏幕分辨率较大的情况下，通过多次模糊这样的做法也是不推荐的**
。这里可以试想一下，假设我们先获取到截屏，接着是否能将这个截取的图片先进行压缩，毕竟后期还是需要模糊的，也就是这个图片被压缩了其实并不影响我们进行模糊（因为到最后都是模糊了）。实际上，当我们进行图片压缩的之后，会发现在相同模糊半径之下，图片的模糊效果不同了，如下两图：

![](http://images2015.cnblogs.com/blog/852227/201608/852227-20160808094841184-115423597.png)
![](http://images2015.cnblogs.com/blog/852227/201608/852227-20160808094854824-1974878437.png)

压缩为原图1/3后模糊                                              原图直接模糊

原因是：高斯模糊采用的算法中确定一个点的颜色是通过这个点附近的其他点来求平均（带权）得到的，而取附近多是个像素点，就是通过模糊半径来确定。当图片被压缩之后，相同模糊半径下，每次取样的区域就变大了，所以模糊强度就更大了。

这样，我们就可以不需要进行多次模糊，并且，压缩图片后，总像素点变少，模糊速度也就变得更快了。

这里同样给出图片压缩的代码：

    
    
        private Bitmap getSmallSizeBitmap(Bitmap source, float percent) {
            if (percent > 1 || percent <= 0) {
                throw new IllegalArgumentException("percent must be > 1 and <= 0");
            }
            Matrix matrix = new Matrix();
            matrix.setScale(percent, percent);
            return Bitmap.createBitmap(source, 0, 0, source.getWidth(), source.getHeight(), matrix, true);
        }





源码地址：[Github](https://github.com/Fndroid/Android3DTouchDemo)



感谢支持。

