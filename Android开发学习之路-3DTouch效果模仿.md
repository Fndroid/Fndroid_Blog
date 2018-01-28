# Android����ѧϰ֮·-3DTouchЧ��ģ��

3D Touch��ʲôЧ���Ĵ��Ӧ�ö�֪���ˡ�ʲô����֪������Ҳû�취ѽ����Ҳû��iPhone 6s��ʾ���㿴�ġ�

��ƪ����Ҫ����Ч��ͼ��

![](http://images2015.cnblogs.com/blog/852227/201608/852227-20160808103914918-716278800.png)

������������ͼ��

![](http://images2015.cnblogs.com/blog/852227/201608/852227-20160808100752934-1267782333.gif)

�����ͼЧ�����Ǻܺã�ʵ����ģ��Ч��Ӧ�����������һ��ͼ�����ģ������ų����룬����Ȥ�Ŀ�����������һ�¿���Ч����



��˵һ��˼·������Ҫʵ�����Ч����ʵֻ��Ҫ���ռ���������

  * ��Ļ��ͼ
  * [ģ������˹ģ����](http://www.cnblogs.com/Fndroid/p/5734696.html)
  * �����ͼ
  * ��������
  * �������¼�
  * �Ż���ģ���ٶȺ�ǿ�ȣ�

���̣����û�����һ��Item��ʱ�������Ƚ�ȡһ�ŵ�ǰ��Ļ��ͼƬ�����Ž�����ͼƬ����ѹ�����ٽ��и�˹ģ�����ٸ����������������棨��������Toolbar������������ģ����Ч���ͳ����ˡ��������Ƕ�̬����������һ��CardView���������ǵ�Item���֣����CardViewҪ���������ǵ���Ķ�Ӧ��Item�ϡ�������һ����Ӧ3D
Touch�����Ķ������ɡ�



����������һ��һ��������������̣�

�� ��Ļ��ͼ

��һ������ԱȽϼ򵥣���Ϊ����Ҫ�õ���ǰ��Ļ��ʾ���ݵ�Bitmap�����ֳɷ����ģ��������£�

    
    
        private Bitmap getScreenImage() { // ��ȡһ����Ļ��ͼƬ
            View view = root;
            view.setBackgroundColor(Color.WHITE);
            view.setDrawingCacheEnabled(true);
            view.buildDrawingCache();
            Bitmap bitmap = Bitmap.createBitmap(view.getDrawingCache(), 0, 0, view.getWidth(), view
                    .getHeight());
            view.destroyDrawingCache();
            return bitmap;
        }

��˵һ�²��֣�����Ĳ����ļ�������ʾ��

    
    
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

���Կ����������������һ��FrameLayout��ԭ����������Ҫ�����������и���һ����˹ģ���˵Ľ�ͼ�����Կ����������ImageView����������ģ��Ч���ģ��ʼ����ֻ��Ҫ������ImageAlpha����Ϊ0����͸�����ɡ��������CardView���ǵ����Ŀؼ������������˵�����ǽ�ͼ��root��FrameLayout�µ�LinearLayout����Ϊ������Ҫ��ToolBarҲģ������



�� ��˹ģ��

������ҵ���һƪ����--[��̬��˹ģ����ô��](http://www.cnblogs.com/Fndroid/p/5734696.html)���Ѿ�˵���ˣ����Խ��вο������������Ӧ�Ĵ��룺

    
    
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

 ���ö�ӦModule��build.gradle�ļ���

    
    
        defaultConfig {
            ...
            renderscriptTargetApi 18
            renderscriptSupportModeEnabled true
        }



 �� ������ͼ

�����ͼ������Ҫ��Item��View��ӵ�CardView�У�������CardView��λ���ڶ�ӦItemλ��֮�ϡ�

    
    
        // ��ʾ��Ӧ�Ŀ�Ƭ
        private void showView(int position, View view){
            newView = LayoutInflater.from(this).inflate(R.layout.item, null); // ����Itme�Ĳ���
            TextView tv = (TextView) newView.findViewById(R.id.item_tv); // ��ȡ��Ӧ�ؼ�
            tv.setText(data.get(position).get("name")); // ��Item��Ӧ�ؼ���ֵ���û�ȥ
            newView.setBackgroundColor(Color.WHITE);
            // ���ÿ�Ƭ����ʽ��λ��ͨ��margintop������
            FrameLayout.LayoutParams params = new FrameLayout.LayoutParams(view.getWidth() - 30, view.getHeight());
            params.topMargin = (int) (view.getY() + mToolbar.getHeight()); // ��Ƭ��marginTop����Ϊitem��Y����toolbar�ĸ߶�
            params.leftMargin = 15;
            params.rightMargin = 15;
            mCardView.setVisibility(View.VISIBLE);
            mCardView.setLayoutParams(params);
            mCardView.addView(newView, view.getLayoutParams()); // ��View���ؽ�CardView����������ʽΪitem��ʽ
            startAnimate(mCardView); // ���Ŷ���
        }

���ﲻ��ֱ�Ӱ�item��view���ؽ�CardView�У���Ϊitem��View�Ѿ��и������ˣ������쳣������취�����¸��ݲ���ӳ��һ����Ȼ��������ݽ�ȥ�������趨��Ƭ��λ����Ϣ�ʹ�С��Ϣ����Ϊ����Ҫ�ÿ�Ƭ��ʾ�ڶ�ӦItem���档



�� ��������

���ǱȽϼ򵥵Ĳ����ˣ�����ֱ��ʹ��PropertyValuesHolder����һ�������������Ķ�����Ϊ������Ҫͬʱ����X��Y����ȻҲ�����������������������£�

    
    
        private void startAnimate(CardView cardView) {
            PropertyValuesHolder pyhScaleX = PropertyValuesHolder.ofFloat("scaleX", 0.1f, 1.05f);
            PropertyValuesHolder pyhScaleY = PropertyValuesHolder.ofFloat("scaleY", 0.1f, 1.05f);
            ObjectAnimator animator_out = ObjectAnimator.ofPropertyValuesHolder(mCardView, pyhScaleX,
                    pyhScaleY); // ͬʱ����X��Y
            animator_out.setInterpolator(new AccelerateDecelerateInterpolator());
            animator_out.setDuration(350);
            PropertyValuesHolder pyhScaleX2 = PropertyValuesHolder.ofFloat("scaleX", 1.05f, 1f);
            PropertyValuesHolder pyhScaleY2 = PropertyValuesHolder.ofFloat("scaleY", 1.05f, 1f);
            ObjectAnimator animator_in = ObjectAnimator.ofPropertyValuesHolder(mCardView, pyhScaleX2,
                    pyhScaleY2);
            animator_in.setInterpolator(new AccelerateDecelerateInterpolator());
            animator_in.setDuration(100);
    
            AnimatorSet animatorSet = new AnimatorSet();
            animatorSet.playSequentially(animator_out, animator_in); // ��˳��ִ����������
            animatorSet.start();
        }



�� ���������¼�

��Ϊ����ֻ��ʹ����ListView����������ݣ�����ֱ��ͨ�����м�������ʵ�֣�

    
    
     1     @Override
     2     public boolean onItemLongClick(AdapterView<?> parent, View view, int position, long id) {
     3         mCover.setImageBitmap(blur(blur(getScreenImage(), 25f),25f)); // �Խ�ȡ��ͼƬ���θ�˹ģ��
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

����ĵ�3���е���������blur��������ͼƬ���и�˹ģ��
�����������һƪ���ͣ�ÿ�θ�˹ģ�������ģ���뾶��25�����Ҫ������iOS��Ҳ��ģ��Ч����25�ǲ����ģ����Կ��Զ�ģ��������ͼƬ��ģ����һ�Σ��Ա�ͼ�����Ϊ2��ģ�����ұ�1�Σ���

![](http://images2015.cnblogs.com/blog/852227/201608/852227-20160805100305622-146852294.png)
![](http://images2015.cnblogs.com/blog/852227/201608/852227-20160805100322278-1817233266.png)



�� �Ż�

����ʵ���ϣ� **����һ���ֱ��ʱȽϸߵ��ֻ�����ȡ����Ļ�ֱ��ʽϴ������£�ͨ�����ģ������������Ҳ�ǲ��Ƽ���**
�������������һ�£����������Ȼ�ȡ�������������Ƿ��ܽ������ȡ��ͼƬ�Ƚ���ѹ�����Ͼ����ڻ�����Ҫģ���ģ�Ҳ�������ͼƬ��ѹ������ʵ����Ӱ�����ǽ���ģ������Ϊ�������ģ���ˣ���ʵ���ϣ������ǽ���ͼƬѹ����֮�󣬻ᷢ������ͬģ���뾶֮�£�ͼƬ��ģ��Ч����ͬ�ˣ�������ͼ��

![](http://images2015.cnblogs.com/blog/852227/201608/852227-20160808094841184-115423597.png)
![](http://images2015.cnblogs.com/blog/852227/201608/852227-20160808094854824-1974878437.png)

ѹ��Ϊԭͼ1/3��ģ��                                              ԭͼֱ��ģ��

ԭ���ǣ���˹ģ�����õ��㷨��ȷ��һ�������ɫ��ͨ������㸽��������������ƽ������Ȩ���õ��ģ���ȡ�������Ǹ����ص㣬����ͨ��ģ���뾶��ȷ������ͼƬ��ѹ��֮����ͬģ���뾶�£�ÿ��ȡ��������ͱ���ˣ�����ģ��ǿ�Ⱦ͸����ˡ�

���������ǾͿ��Բ���Ҫ���ж��ģ�������ң�ѹ��ͼƬ�������ص���٣�ģ���ٶ�Ҳ�ͱ�ø����ˡ�

����ͬ������ͼƬѹ���Ĵ��룺

    
    
        private Bitmap getSmallSizeBitmap(Bitmap source, float percent) {
            if (percent > 1 || percent <= 0) {
                throw new IllegalArgumentException("percent must be > 1 and <= 0");
            }
            Matrix matrix = new Matrix();
            matrix.setScale(percent, percent);
            return Bitmap.createBitmap(source, 0, 0, source.getWidth(), source.getHeight(), matrix, true);
        }





Դ���ַ��[Github](https://github.com/Fndroid/Android3DTouchDemo)



��л֧�֡�

