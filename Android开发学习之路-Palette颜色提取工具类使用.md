# Android����ѧϰ֮·-Palette��ɫ��ȡ������ʹ��

��Ƶ��ҪFQ����https://www.youtube.com/watch?v=5u0dtzXL3PQ

Palette��һ����support-v7���е�һ����ɫ��ȡ�����࣬�÷��Ƚϼ򵥣������ǹȸ�ٷ��ṩ���б�Ҫ�˽�һ�¡�������Ч��ͼ��ToolBar����ɫ��ͼƬ�ı�

![](http://images2015.cnblogs.com/blog/852227/201602/852227-20160219151535909-1437194347.gif)

���������Ч����ô��������һƪ�������ˡ�����ֻ˵Palette�����Կ�������ͼƬ������ȥ֮��������Ҫ��Toolbarһ����ɫ����Ϊ����������ͼƬ�������ǵ�Ч���Ǹ���������ͼƬ��Toolbar���ú��ʵ���ɫ�������������ۡ�

�÷���

    
    
    1    // Synchronous
    2    Palette p = Palette.from(bitmap).generate();
    3   
    4    // Asynchronous
    5    Palette.from(bitmap).generate(new PaletteAsyncListener() {
    6        public void onGenerated(Palette p) {
    7            // Use generated instance
    8        }
    9    });

���Կ�����������֧��ͬ�����첽�ģ�����ò������������ķ�ʽ�Ѿ��������ˣ���������API����֪����Builder�����ˣ��������£�

    
    
    Palette.Builder bd = new Palette.Builder(BitmapFactory.decodeResource(getResources(), imageId));
    
    
    Palette palette = bd.generate();



�����ǹ������һ��Palette����֮�󣬿���ͨ��getXXXColor(int
defaultColor)�ķ�������Ҫ����һ��Ĭ�ϵ���ɫ���������Ӧ����ɫ��Ȼ�������ڸ���Ӧ�Ŀؼ�������ɫ����

�����XXX���Զ�ӦΪ��

Vibrant            ǿ�ҵ�  
Vibrant Dark  
Vibrant Light  
Muted             ��͵�  
Muted Dark  
Muted Light

Ȼ���������gradle������

    
    
    compile 'com.android.support:palette-v7:23.1.1'



�����Ҫ˵��һ�㣬 **Palette������������ɫ��ʱ���ǻ�ʧ�ܵ�** ��������ͼ�������ɫδ��ʶ���������ʹ��������getXXXColor(int
defaultColor)�������趨��Ĭ����ɫ

![](http://images2015.cnblogs.com/blog/852227/201602/852227-20160219154827050-1180741943.png)

