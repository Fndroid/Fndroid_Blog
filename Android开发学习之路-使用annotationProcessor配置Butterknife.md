# Android开发学习之路-使用annotationProcessor配置Butterknife

Apt工具的作者宣布了不再维护该工具了，而且Android Studio也有了自己的插件，并且可以通过gradle来简单的配置。

其实用Butterknife的都知道，没有apt，onClick绑定不了监听，而配置apt有时候又会出现问题，所以推荐使用annotationProcessor来解决这个问题。

首先，确定Android Studio的版本在2.2以上，且gradle的版本为2.2.1以上，因为低版本的没有试过，这是我当前的配置。

    
    
        dependencies {
            classpath 'com.android.tools.build:gradle:2.2.1'
        }

这样，配置Butterknife就很简单了。



我们看一下Butterknife的Github指导安装：

![](http://images2015.cnblogs.com/blog/852227/201610/852227-20161017091650935-1640256265.png)

如果不使用apt工具，我们可以不需要修改project下的build.gradle，直接修改module下的build.gradle，通过annotationProcessor替代apt，所以只需要添加：

    
    
    dependencies {
        ......
        annotationProcessor 'com.jakewharton:butterknife-compiler:8.4.0'
        compile 'com.jakewharton:butterknife:8.4.0'
    }



