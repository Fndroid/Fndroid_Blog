# Android开发学习之路-图片颜色获取器开发（1）

系列第一篇，从简单的开始，一步一步完成这个小项目。

颜色获取就是通过分析图片中的每个像素的颜色，来分析整个图片的主调颜色，有了主调颜色，我们可以用于图片所在卡片的背景或者标题颜色，这样整体感更加强烈。

有兴趣的可以学习下使用谷歌提供的Palette，也是做同样的工作，博客地址：http://www.cnblogs.com/Fndroid/p/5201236.html

先看效果图：

![](http://images2015.cnblogs.com/blog/852227/201605/852227-20160507110912841-314403128.gif)

分析原理比较简单，就是获取图片的所有像素的颜色，然后统计，把统计的数目排序，然后返回给用户。

但是这里要先注意几个问题：

① 获取颜色的过程会不会导致UI线程卡顿；

② 怎么实现排序（如何优化后面研究）；

因为考虑到当图片像素较多的时候，分析可能不会马上完成，所以分析过程应该在子线程中完成，避免阻塞UI线程。其次，统计和排序都可以通过Java提供的数据结构来简单实现，暂且不考虑性能因素，实现功能为先。



① 在Android中，图片一般会用Bitmap来表示，而Bitmap中有一个叫做getPixels的方法：

    
    
    public void getPixels(@ColorInt int[] pixels, int offset, int stride, int x, int y, int width, int height)

参数分别是：  

pixiels：存放识别出颜色的数组

offset：数组的起始下标

stride：每行的数据量，比如一下张200*200的图片，在stride设置为200的时候，pixels[200]为第二行的第一个像素颜色，当stride设置为400的时候，pixels[200]为额外的信息，不包含颜色，而第二行的第一个像素的颜色应该在pixels[400]

x、y：开始的x和y

width、height：需要获取颜色的宽度和高度，和x、y构成一个矩形

② 获取完颜色之后，对颜色进行统计，因为得到了一个数组，所以对数组进行统计，如果有相同的要加一，得出每个颜色出现的次数，算法简单如下

    
    
    HashMap<Integer, Integer> colors = new HashMap<>();
    for (int pixel : pixels) {
       Integer num = colors.get(pixel);
       if (num == null) {
           colors.put(pixel, 1);
       } else {
           num += 1;
           colors.put(pixel, num);
       }
    }

③ 排序，因为需要返回一个有序的数组，这里方便的可以用TreeMap直接排，要注意TreeMap是对key排序的，而且默认是升序

    
    
    TreeMap<Integer, Integer> sortedColors = new TreeMap<>();
    for (Map.Entry<Integer, Integer> entry : colors.entrySet()) {
        sortedColors.put(entry.getValue(), entry.getKey());
    }

④ 返回一个有序数组，这里简单的遍历一下存到ArrayList中即可

    
    
    ArrayList<Integer> result = new ArrayList<>();
    for (Map.Entry<Integer, Integer> entry : sortedColors.entrySet()) {
        result.add(entry.getValue());
    }

⑤ 如果直接调用函数求，会可能阻塞UI，所以要用子线程来做这个工作，简单的话直接new一个Thread

⑥ 在子线程中如果任务执行完毕，通过Handler发送消息，通知UI更新



下面给出整个类：

    
    
    public class ColorCaptureUtil {
        private static final String TAG = "ColorCaptureUtil";
        private Handler mHandler;
        public static final int SUCCESS = 1;
    
        /**
         * Construct a ColorCaptureUtil for analysing the color of the bitmap
         * @param handler When the analysing done, it would be used to send back the result and call for update
         */
        public ColorCaptureUtil(Handler handler) {
            mHandler = handler;
        }
    
        /**
         * Capture the bitmap's colors by counting every pixels, use the constructor's Handler to send back
         * message, the ArrayList which contains the colors and sorted by the color quantity would be send
         * back with the message in the Message.obj
         * @param bitmap The Bitmap for analysing
         */
        public void getBitmapColors(Bitmap bitmap){
            getBitmapColors(bitmap, 0, 0, bitmap.getWidth(), bitmap.getHeight());
        }
    
        /**
         * Capture the bitmap's colors by counting every pixels, use the constructor's Handler to send back
         * message, the ArrayList which contains the colors and sorted by the color quantity would be send
         * back with the message in the Message.obj
         * @param bitmap The Bitmap for analysing
         * @param fromX  The start X in the bitmap, can not be negative
         * @param fromY  The start Y in the bitmap, can not be negative
         * @param toX    The end X in the bitmap, can not less than fromX
         * @param toY    The end Y in the bitmap, can not less than fromY
         */
        public void getBitmapColors(Bitmap bitmap, int fromX, int fromY, int toX, int
                toY) {
            new Thread(new MyRunnable(bitmap,fromX,fromY,toX,toY,mHandler)).start();
        }
    
        private class MyRunnable implements Runnable{
            private Bitmap bitmap;
            private int fromX,fromY,toX,toY;
            private Handler mHandler;
    
            public MyRunnable(Bitmap bitmap, int fromX, int fromY, int toX, int toY, Handler handler) {
                this.bitmap = bitmap;
                this.fromX = fromX;
                this.fromY = fromY;
                this.toX = toX;
                this.toY = toY;
                this.mHandler = handler;
            }
    
            @Override
            public void run() {
                int[] pixels = new int[bitmap.getWidth() * bitmap.getHeight()];
                HashMap<Integer, Integer> colors = new HashMap<>();
                TreeMap<Integer, Integer> sortedColors = new TreeMap<>();
                ArrayList<Integer> result = new ArrayList<>();
                bitmap.getPixels(pixels, 0, bitmap.getWidth(), fromX, fromY, toX - fromX, toY - fromY);
                for (int pixel : pixels) {
                    Integer num = colors.get(pixel);
                    if (num == null) {
                        colors.put(pixel, 1);
                    } else {
                        num += 1;
                        colors.put(pixel, num);
                    }
                }
                for (Map.Entry<Integer, Integer> entry : colors.entrySet()) {
                    sortedColors.put(entry.getValue(), entry.getKey());
                }
                for (Map.Entry<Integer, Integer> entry : sortedColors.entrySet()) {
                    result.add(entry.getValue());
                    Log.d(TAG, "run: color:"+entry.getValue()+",count:"+entry.getKey());
                }
                Message msg = new Message();
                msg.obj = result;
                msg.what = SUCCESS;
                mHandler.sendMessage(msg);
            }
        }
    }

可以看到，在构造的时候需要一个Handler，当我们处理完图片颜色数据的时候，就是通过这个Handler来通知UI线程，然后把统计的数据存到Message.obj中，在主线程中直接取出即可得到颜色，数据是按升序排列的，如果想得到最多的颜色，需要取出size-1下标的值

    
    
    private Handler mHandler = new Handler() {
            @Override
            public void handleMessage(Message msg) {
                if (msg.what == ColorCaptureUtil.SUCCESS) {
                    ArrayList<Integer> colors = (ArrayList<Integer>) msg.obj;
                    color.setBackgroundColor(colors.get(colors.size() - 1));
                }
            }
        };



第一部分的内容比较简单，正是因为比较简单，所以接下来还是有一些问题需要解决的

① 解析速度问题，考虑是否要使用其他排序算法？是否需要处理全部的像素点？能否进行局部采样？

② 当图片像效果图中的第四张（如下）一样的时候，统计出现最多的像素颜色是否合理？

![](http://images2015.cnblogs.com/blog/852227/201605/852227-20160507142716437-2050777961.png)

③ 直接使用new Thread是否合理？当有大量图片需要处理的时候会不会出现问题？



尽快更新下篇，欢迎指正交流。



