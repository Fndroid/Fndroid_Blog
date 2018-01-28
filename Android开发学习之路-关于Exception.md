# Android开发学习之路-关于Exception

Exception在Java中是表示异常的一个类。它是Throwable的子类。

而Exception的子类RuntimeException是一个特殊的异常类，在代码中不需要对此类进行throw，而是由JVM来抛出。

很多时候，我们的某些问题，可以通过一个try/catch简单的解决掉，但是前提是要了解Exception。



很多人对于Exception的理解，仅仅是在于IDE会提示错误，然后补全一下try/catch就完事了，不会考虑到为什么，以至于自己编写的库交给别人，别人也不愿意使用。

首先要明白的是，除了RuntimeException以外的其他Exception都是需要通过throw抛出的。如果被调用的方法中有通过throw抛出异常，则需要在调用的方法中使用try/catch来进行捕获处理或者通过在方法尾使用throws将异常继续抛出。

这都是最基础的。



对于Exception，要明白两点：

  1. 异常捕获，可以进行特殊的判断
  2. 只要是Exception，就可以被try/catch捕获得到，包括RuntimeException



首先说明第一点，什么是特殊判断？就是说我们用if/else语句判断不了。

举个例子，FingerprintManager这个类，是在API23中引入，用来操作手机指纹模块的，那么在API22及一下的平台，是不存在这个类的。但是，国内某些厂商，将指纹模块引入到API22以下的平台中，并且移植了官方的这些类，也就是说在某些国产Rom（如MIUI）中API22及以下是可能存在FingerprintManager这个类的，并且是可以操作指纹模块的。这样就导致了，我们需要判断Rom中是否存在这个类了。怎么判断，当然不能用if/else语句。

做法很简单，直接进行反射，而反射时如果没有这个类，会抛出ClassNotFoundException，代码如下：

    
    
    1         try {
    2             Class.forName("android.hardware.fingerprint.FingerprintManager");
    3         } catch (ClassNotFoundException e) {
    4             e.printStackTrace();
    5             Log.d(TAG, "doSomething: 没有该api");
    6         }



接着是第二点，RuntimeException是可以被捕获的。

因为RuntimeException不是由throw来抛出，所以IDE不会引导我们使用try/catch来捕获这个异常，而实际上，我们可以自己进行添加。

例子：在Android开发中，假设要对某个View设置背景颜色，我们可以调用setBackgroundColor方法来进行设置，这个方法传入一个int作为参数。实际上这个int代表的是一个颜色的ARGB值，如白色0xFFFFFFFF。假设需要编写一个方法，设置一个View的背景颜色，传入一个参数表示颜色的值或者是颜色的id（R.color.xxx)，方法名：

    
    
    private void setViewBackground(View view, int color)

要怎么写？

首先，不能通过重载，因为颜色的值和id都是一个int，参数类型相同，就不能重载，多增加一个参数又显得多余。

这个时候，就可以通过异常的捕获来做到：

    
    
    1     private void setViewBackground(View view, int color) {
    2         try {
    3             view.setBackgroundColor(ContextCompat.getColor(this, color));
    4         } catch (Resources.NotFoundException e) {
    5             e.printStackTrace();
    6             view.setBackgroundColor(color);
    7         }
    8     }

可以看到，我们直接将这个int作为一个id来使用，通过ContextCompat的getColor来获取id对应的颜色值，这个时候，假设传入的直接就是一个颜色，那么在资源文件R中，是找不到这个id的，这样会抛出一个RuntimeException，就是上面给出的Resources.NotFoundException，如果捕获到异常，就正面这个参数不是id，直接设置给View即可。

当然，这个方法也不是都正确，因为有可能传入的颜色值刚好与某个颜色的id一样，那么就会出现得不到想要颜色的问题。这个例子，只是说明RuntimeException可以被我们处理，即使IDE不提示。



