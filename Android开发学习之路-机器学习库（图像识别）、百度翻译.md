# Android����ѧϰ֮·-����ѧϰ�⣨ͼ��ʶ�𣩡��ٶȷ���

���ڻ���ѧϰҲ�����˽�ĺ����룬������������GitHub����һ��star�ıȽ϶�Ŀ⣬��������һ�ԣ�Ч��Ҳ���С����ǿ��ܱȲ���TensorFlow�ģ�������Android���������Ƚϼ򵥣��Ͼ�TensorFlow��Ҫ��JNI��֪ʶ��

����⣺[onyx](https://github.com/hanuor/onyx)

Ч����

![](http://images2015.cnblogs.com/blog/852227/201609/852227-20160920205023981-2096658760.png)

�÷��ǳ��򵥣����Ǹ���ͼƬ�����õ�ͼƬ�п��ܵķ��࣬���ѧϰ�Ŀ����Ѿ���ѵ�����ģ���������ֻ��Ҫֱ������ʶ��ͺ��ˡ��õ��Ľ���Ǹ��ݿ��ܸ����ɸߵ������С���Ϊ�õ��Ľ������Ӣ�����Ҳ�ðٶȷ����������ˣ����Խ���п���ĳЩ�ʻ�Ƚ���֡�



�� onyxʹ��

������

    
    
    compile 'com.hanuor.onyx:onyx:1.1.4'

ֱ�ӱ���᲻ͨ������Ϊ������õ���Java8д�ģ���Android
StudioĬ���½��Ĺ�����1.7�ģ����Ի�Ҫ�������ã���app�µ�build.gradle�ļ��У�������´��룺

    
    
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
    ����...
    }

ʹ�þͱȽϼ��ˣ�

    
    
    1 Onyx.with(MainActivity.this).fromURL(URL).getTagsfromApi(new OnTaskCompletion() {
    2                     @Override
    3                     public void onComplete(ArrayList<String> response) {
    4                        // do your stuff
    5                     }
    6                 });

������������ɹ���ص�onComplete�������õ�����������ʾ�ı�ǩ��Ӣ�ġ�

������ʵ������һ��������getTagsandProbability()������������ǻ�ͬʱ��ȡ��ÿ����ǩ��Ӧ�ĸ��ʡ�



�� �ٶȷ���

[�����ĵ�](http://api.fanyi.baidu.com/api/trans/product/apidoc)

ʹ�ðٶȷ�����ʵ���ѣ�����һ��Ҫ�²�һ�°ٶȷ�������⣬���һ�����ʱ�����һ���������ţ��ٶȷ����ֱ�Ӻ���������ʡ�

![](http://images2015.cnblogs.com/blog/852227/201609/852227-20160920210705637-617782438.png)

![](http://images2015.cnblogs.com/blog/852227/201609/852227-20160920210723652-810675377.png)

����������ĵ��������ˣ������м����ط�Ҫע�⣺

  * MD5���ܵ�ʱ��Ҫ�õ�һ��ʮ�����ƴ�
  * �������ǰҪ����URLEncode



����Ҳ��һ�£�����Ժ����������ˡ�

[DemoԴ��](https://github.com/Fndroid/MachineLearningDemo/tree/master)

