# Android����ѧϰ֮·-ʹ��annotationProcessor����Butterknife

Apt���ߵ����������˲���ά���ù����ˣ�����Android StudioҲ�����Լ��Ĳ�������ҿ���ͨ��gradle���򵥵����á�

��ʵ��Butterknife�Ķ�֪����û��apt��onClick�󶨲��˼�����������apt��ʱ���ֻ�������⣬�����Ƽ�ʹ��annotationProcessor�����������⡣

���ȣ�ȷ��Android Studio�İ汾��2.2���ϣ���gradle�İ汾Ϊ2.2.1���ϣ���Ϊ�Ͱ汾��û���Թ��������ҵ�ǰ�����á�

    
    
        dependencies {
            classpath 'com.android.tools.build:gradle:2.2.1'
        }

����������Butterknife�ͺܼ��ˡ�



���ǿ�һ��Butterknife��Githubָ����װ��

![](http://images2015.cnblogs.com/blog/852227/201610/852227-20161017091650935-1640256265.png)

�����ʹ��apt���ߣ����ǿ��Բ���Ҫ�޸�project�µ�build.gradle��ֱ���޸�module�µ�build.gradle��ͨ��annotationProcessor���apt������ֻ��Ҫ��ӣ�

    
    
    dependencies {
        ......
        annotationProcessor 'com.jakewharton:butterknife-compiler:8.4.0'
        compile 'com.jakewharton:butterknife:8.4.0'
    }



