# Android����ѧϰ֮·-��̬��˹ģ����ô��

ʲô�Ǹ�˹ģ����

> **��˹ģ��** ��Ӣ�Gaussian Blur����Ҳ�и�˹ƽ��������Adobe
Photoshop��GIMP�Լ�Paint.NET��ͼ��������й㷺ʹ�õĴ���Ч����ͨ������������ͼ�������Լ�����ϸ�ڲ�Ρ�����ģ���������ɵ�ͼ�����Ӿ�Ч�������Ǿ���һ����͸����Ļ�ڹ۲�ͼ�����뾵ͷ�������Ч��ɢ���Լ���ͨ������Ӱ�е�Ч�������Բ�ͬ��

ʲô���������ף�û��ϵ����Ҳ�������ף�ά���ٿƸ��ƻ����������ֱ�ӷ�һЩͼƬ���˽����������˹ģ������ô���ġ���Ϊ��˹ģ����iOS�����������ץ�˼���iOS�����Ƶ�ͼƬ��

![](http://images2015.cnblogs.com/blog/852227/201608/852227-20160803202633403-940757137.jpg)![](http://images2015.cnblogs.com/blog/852227/201608/852227-20160803202651997-1292086399.jpg)

���Կ�����������еı�������ʵ����ͨ��ͼ1�м��Ǹ�СͼƬģ���õ��ģ��������ĺô����������Ժܺã����Ҳ�����ΪͼƬ����ͻأ��Ӱ��������ݵ��Ķ���

��ô��������Android����ôȥʵ�����Ч���أ������Ƽ�ʹ�ùٷ��ṩ��Support Library�е�һ����������������
**RenderScript**
�����RenderScript�Ĺ�����ʵ��ֹ����һ������������һЩ���ܣ�������[�ٷ��ĵ�](https://developer.android.com/guide/topics/renderscript/compute.html#writing-
an-rs-kernel)���Ķ�ѧϰ�����ﲻ������

ʹ��������ߵ�ԭ����ʵ�ܼ򵥣��������ܡ���Ϊ�漰����ͼ������������ܲ��У���ô���۶��ڸ�����ͼƬ�����Ǳ仯�϶�������Ǻܳ����ģ��������������ַ����豸�ļ���������CPU��GPU�������м��㣬������ʹ��C99�������Խ��нű���д�ģ������Java�����Ǵ���������

˵�������Щͬѧ�Ϳ�ʼ���ˣ�C99������What���������Ҫ���ģ����ڸ�˹ģ�����ʵ�֣�Google�ٷ��Ѿ������˶�Ӧ�Ľ�����������ǲ�����Ҫ��д��Ӧ�Ľű��Ϳ���ʹ���ˣ��������赣�ġ�



���ǰ����������Ϊ�������֣��ٸ�˹ģ��ʵ�֣��ڶ�̬��˹ģ��ʵ��



�� ��˹ģ��ʵ��

����Ҫ˵��������Ҫʹ��Support Library�����԰汾����Ҫ��ģ�

  * Android SDK Tools �汾������ڵ���22.2
  * Android SDK Build-tools �汾������ڵ���18.1.0

���û�дﵽ����ʹ��SDK Manager����һ�¡�

���Ŵ������ǵĹ��̣������ڶ�ӦModule��Ĭ�ϴ�������app����build.gradle�ļ��м������´��룺

    
    
    defaultConfig {
            ...
            renderscriptTargetApi 18
            renderscriptSupportModeEnabled true
        }

renderscriptTargetApi�����һ���App֧�ֵ���Ͱ汾��ͬ���ɡ�

    
    
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
            sampleImg = BitmapFactory.decodeResource(getResources(), R.drawable.icon); // ��ȡԭͼ
            gaussianBlurImg = blur(sampleImg, 25f);
            mImageView.setImageBitmap(gaussianBlurImg);
        }
    
        private Bitmap blur(Bitmap bitmap,float radius) {
            Bitmap output = Bitmap.createBitmap(bitmap); // �������ͼƬ
            RenderScript rs = RenderScript.create(this); // ����һ��RenderScript����
            ScriptIntrinsicBlur gaussianBlue = ScriptIntrinsicBlur.create(rs, Element.U8_4(rs)); // ������˹ģ���ű�
            Allocation allIn = Allocation.createFromBitmap(rs, bitmap); // ������������Ľű�����
            Allocation allOut = Allocation.createFromBitmap(rs, output); // ������������Ľű�����
            gaussianBlue.setRadius(radius); // ����ģ���뾶����Χ0f<radius<=25f
            gaussianBlue.setInput(allIn); // ��������ű�����
            gaussianBlue.forEach(allOut); // ִ�и�˹ģ���㷨�����������������ű�������
            allOut.copyTo(output); // ������ڴ����ΪBitmap��ͼƬ��С����ע��
            rs.destroy(); // �ر�RenderScript����API>=23��ʹ��rs.releaseAllContexts()
            return output;
        }
    }

����˵����ע���ڴ������ˡ���Ҫ�˽����RenderScript�������汾���ֱ��ǣ�

  * android.renderscript
  * android.support.v8.renderscript

�������ʹ���˵�һ�����ڶ������÷����ƣ��������г��ԡ�

**��������һ��˼·����ΪRenderScript��������Script�ģ�������Ҳ˵���ˣ�Script����C99�������Ա�д���������е�ScriptIntrinsicBlur���Ƕ�Ӧ�ڸ�˹ģ���㷨�Ľű�����Allocation�������ǽ�Java�еĶ���ת��ΪScript�ű��������͵İ��֣������д���������Allocation����ֱ������䵱�������������������˸�˹ģ���İ뾶��radius����������forEachʱ���ű��ᱻִ�У����ҽ�ִ�н�����������Ӧ��Allocation�У�������copyTo��ת��ΪBitmap���󷵻ء�**

Ч��ͼ��

![](http://images2015.cnblogs.com/blog/852227/201608/852227-20160803210836528-1382978843.png)



�� ��̬��˹ģ��

�ܶ�ʱ�����ǿ��ܻ���Ҫһ��ͼƬ�Բ�ͬ��ģ���̶�չ�ֳ�����������Ѿ�ע�⵽���淽���е�ģ���뾶�ˣ����ǿ�����һ��ʵ�飬����ͨ��һ��SeekBar����̬�ı����ֵ����Ч����

![](http://images2015.cnblogs.com/blog/852227/201608/852227-20160803212946700-1525024003.gif)

�ɶ�ͼ���Կ����������϶�SeekBar��ʱ��SeekBar�Ѿ����������ǵ��϶��ˡ�����Ϊʲô��ԭ����������Ⱦ������Ȼ���ܱȽ����죬�������ͼƬ�������ͳߴ綼�ϸߵ�ʱ������ֱ�ӽ����޸�ģ���뾶������Ⱦ����������ʱ����ȡ�ġ�

�������һ��������������Idea�����ڹ���������������"��ˮ����"��

�����ǣ�
**�ȴ���һ��ģ����ͼƬ������ImageView�У����������ImageView�������ٷ���һ������ԭͼImageView��ʹ��FrameLayout������������ImageView�ص���һ�𣬽��ŵ�������Ҫ��̬�ı�ģ���̶ȵ�ʱ�򣬸ı��ϲ��ImageView��BitmapAlpha�Ϳ�����**
�������ȿ���Ч��ͼ��

![](http://images2015.cnblogs.com/blog/852227/201608/852227-20160803213913856-2131277246.gif)

ʹ����������������������ͻ�Ƚ������ˡ���GIF��������ǻ��������ٶ�Ŷ��

�����һ�´���ο��ɣ�

    
    
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
            sampleImg = BitmapFactory.decodeResource(getResources(), R.drawable.icon); // ��ȡԭͼ
            gaussianBlurImg = blur(sampleImg, 25f);
            mImageView.setImageBitmap(gaussianBlurImg);
            mSeekBar.setOnSeekBarChangeListener(this);
        }
    
        private Bitmap blur(Bitmap bitmap, float radius) {
            Bitmap output = Bitmap.createBitmap(bitmap); // �������ͼƬ
            RenderScript rs = RenderScript.create(this); // ����һ��RenderScript����
            ScriptIntrinsicBlur gaussianBlue = ScriptIntrinsicBlur.create(rs, Element.U8_4(rs)); //
            // ������˹ģ���ű�
            Allocation allIn = Allocation.createFromBitmap(rs, bitmap); // ���������ڴ�
            Allocation allOut = Allocation.createFromBitmap(rs, output); // ��������ڴ�
            gaussianBlue.setRadius(radius); // ����ģ���뾶����Χ0f<radius<=25f
            gaussianBlue.setInput(allIn); // ���������ڴ�
            gaussianBlue.forEach(allOut); // ģ�����룬�����ڴ���������ڴ�
            allOut.copyTo(output); // ������ڴ����ΪBitmap��ͼƬ��С����ע��
            rs.destroy(); // �ر�RenderScript����API>=23��ʹ��rs.releaseAllContexts()
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

 �����ļ���

    
    
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



�緢��������ָ������л��

