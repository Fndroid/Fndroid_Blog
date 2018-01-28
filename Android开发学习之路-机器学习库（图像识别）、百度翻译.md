# Android开发学习之路-机器学习库（图像识别）、百度翻译

对于机器学习也不是了解的很深入，今天无意中在GitHub看到一个star的比较多的库，就用着试一试，效果也还行。比是可能比不上TensorFlow的，但是在Android上用起来比较简单，毕竟TensorFlow还要又JNI的知识。

这个库：[onyx](https://github.com/hanuor/onyx)

效果：

![](http://images2015.cnblogs.com/blog/852227/201609/852227-20160920205023981-2096658760.png)

用法非常简单，就是根据图片分析得到图片有可能的分类，这个学习的库是已经被训练过的，所以我们只需要直接让它识别就好了。得到的结果是根据可能概率由高到低排列。因为得到的结果都是英语，这里也用百度翻译来翻译了，所以结果中可能某些词会比较奇怪。



① onyx使用

依赖：

    
    
    compile 'com.hanuor.onyx:onyx:1.1.4'

直接编译会不通过，因为这个库用的是Java8写的，而Android
Studio默认新建的工程是1.7的，所以还要进行配置，在app下的build.gradle文件中，添加如下代码：

    
    
    android {
        ...
    
        compileOptions {
            sourceCompatibility JavaVersion.VERSION_1_8
            targetCompatibility JavaVersion.VERSION_1_8
        }
    
        defaultConfig {
            ...
            jackOptions {
                enabled true
            }
        }
    　　...
    }

使用就比较简单了：

    
    
    1 Onyx.with(MainActivity.this).fromURL(URL).getTagsfromApi(new OnTaskCompletion() {
    2                     @Override
    3                     public void onComplete(ArrayList<String> response) {
    4                        // do your stuff
    5                     }
    6                 });

这里如果分析成功会回调onComplete方法，得到上面我们显示的标签的英文。

这里其实还有另一个方法：getTagsandProbability()，这个方法则是会同时获取到每个标签对应的概率。



② 百度翻译

[接入文档](http://api.fanyi.baidu.com/api/trans/product/apidoc)

使用百度翻译其实不难，但是一定要吐槽一下百度翻译的问题，如果一个单词边上有一个其他符号，百度翻译会直接忽略这个单词。

![](http://images2015.cnblogs.com/blog/852227/201609/852227-20160920210705637-617782438.png)

![](http://images2015.cnblogs.com/blog/852227/201609/852227-20160920210723652-810675377.png)

接入基本看文档就明白了，但是有几个地方要注意：

  * MD5加密的时候，要得到一个十六进制串
  * 构造参数前要进行URLEncode



代码也存一下，免得以后碰到又忘了。

[Demo源码](https://github.com/Fndroid/MachineLearningDemo/tree/master)

