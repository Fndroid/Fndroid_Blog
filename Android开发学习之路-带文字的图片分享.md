# Android����ѧϰ֮·-�����ֵ�ͼƬ����

���ù�΢�ŷ���SDK�Ķ�Ӧ��֪����΢�ŷ�������Ȧ��ʱ���ǲ���ͬʱ����ͼƬ�����ֵģ�ֻҪ������ͼ����ô���־Ͳ�����Ч����ô��������ˣ�����������APP�ڵ�ĳЩ��������ͼƬһ�����΢�ţ��ǲ���û�취���أ�

���������ǳ��õ����������֣��������ǰѸ�������Ÿ�����ͼƬƴ��һ����һ��ͼ�������ٰ�����ͼƬ�����ȥ�ͺ��ˡ�

**��ô����ƪ�����ݾ��Ƕ�����һ�������ֵ�ͼƬ��**

����Ҳ��¼�������ģ���Ϊ����һ��ʧ�������App���������Ͻ���ʧ��֮�󣬿��Խ������Ϣ�����ȥ�������Ϣ��������Ʒ����Ϣ��ͼƬ����΢��SDKʼ���޷������������Ű���Ʒ��ϢǶ�뵽ͼƬ�з����ȥ���ȷ�һ��Ч��ͼ��

![](http://images2015.cnblogs.com/blog/852227/201606/852227-20160630151810109-688194757.jpg)



��������ȥ��ͼƬ�ܼ򵥣�������ͼƬ�����������������һ��



**��Ҫ֪����������ԭ����ͨ������һ����BitmapΪ������Canvas�������ģ�˼·�ܼ򵥣�**

**�� �û���������Bitmap��**

**�� �ڻ������Ϸ������������õ�ͼƬ**

**�� �ڢ������Ƶ�ͼƬ�����������**



��һ���ܼ򵥣�����ֻ��Ҫ����һ��Bitmap����װ�ص�Canvas�оͿ����ˣ���������õ���ͼƬ��Ϊbitmap����������£�

    
    
    Bitmap.Config config = bitmap.getConfig();
    Bitmap shareBitmap = Bitmap.createBitmap(bitmap.getWidth(), bitmap.getHeight(), config);
    Canvas canvas = new Canvas(shareBitmap);

���������Ǿ�Ҫ˼���ˣ����ͼƬ�Ŀ��Ӧ����ô���ñȽϺ����أ�����Ĵ�������Ϊ����������ͼƬһ����Ҳ����˵���������Ҫ������֣�����ֻ����ʾ��ͼƬ�ϡ����ʱ�����ͼƬ����ɫ�ȽϷḻ����ô���ֵ�������ͻ���ѿ�������ˡ�����������ͼƬ����������������ͼƬ����������һЩ�ռ����������֣�����Ҫ���õĸ߶�Ӧ��Ҫ���Ӵ�һ�㡣������Ҫ������ǵ��������ۡ�



�ڶ������ڻ����л����������õ�ͼƬ������ͺܼ��ˣ�ֱ���������Ĵ��룺

    
    
    canvas.drawBitmap(bitmap, 0, 0, paint);



�����ǵ�������Ҳ�����ѵĵط������ﲻ��ֱ����canvas��ֱ�ӵ���drawText�������������֣�Ϊʲô����Ϊ���ǵ����������п��ܱ�ͼƬ�Ŀ��Ҫ�󣬵����ֱ�ͼƬ�����ʱ��ʹ��drawText���޷����������ݻ��еģ��������־ͱ��ض��ˡ�

����ķ�����ʹ��TextPaint���Paint�����ࡣ����໹��Ҫ���StaticLayout���������֣����ǿ��������÷���

    
    
    Paint paint = new Paint();
    paint.setColor(Color.BLACK); // ������ɫ
    TextPaint textpaint = new TextPaint(paint);
    textpaint.setTextSize(textSize); // ���ִ�С
    textpaint.setAntiAlias(true); // �����
    StaticLayout title_layout = new StaticLayout(title.getText().toString(), textpaint, sourceBitmapWidth, Layout.Alignment.ALIGN_CENTER, 1f, 1f, true);

ֱ��ͨ�����ǵ�Paint����������һ��TextPaint���������ÿ���ݺ����ִ�С�����Ŵ���һ��StaticLayout���󣬹��췽����Ҫ����Ĳ����ֱ��ǣ��������ݡ�TextPaint�����ı���ȡ����뷽ʽ���о౶�����о�������Ƿ�����ڱ߾ࡣ����Ƚ���Ҫ�ĵط�������
**�ı����** �����ı���ȱ����ֵ���ʱ��ͻ��Զ����С�

�����ǹ���������StaticLayout֮�󣬾Ϳ��ԶԻ������ж�λ��Ȼ�����ֻ��Ƴ�����

    
    
    canvas.translate(0, sourceBitmapHeight); // �ƶ�λ�õ�ͼƬ������
    title_layout.draw(canvas); // �ڻ����л�������

 ������⼸��֮��Canvas�е�Bitmap�ͻ���ͼƬ�������ˡ�



�������ǵ�������ʵ��û�н����

**��
��������ȥ��Bitmap�ĸ߶Ⱦ����Ƕ����أ���������������������⣬��ʵ�Ѿ��д��ˣ����ǿ��������ĸ߶�ΪͼƬ�����ݼ������Ǵ�����StaticLayout�ĸ߶ȾͿ����ˡ�����ͼƬ�ĸ߶Ȼ�����������ݵĶ��ٱ仯����ȡStaticLayout�ĸ߶ȱȽϼ򵥣�**

    
    
    title_layout.getHeight()



**��
���ʱ���������ֱ����App����ʾ���ͼƬ����û��ʲô����ģ�����������ǰ�ͼƬ����΢�ţ���ᷢ�֣�ͼƬ�����ֲ�����ȫ����˺�ɫ�������ֶ��������ˡ�����Ľ���취Ҳ�ܼ򵥣��ڻ��Ƶ�ʱ���ȸ�����ͼƬ����һ����ɫ�ı�����**

    
    
    canvas.drawColor(Color.WHITE);



 ����ͻ�������ˣ�����Ҳ��������Ҳο��°ɣ�

    
    
    // �������õ�ͼƬΪimageBitmap
        private Bitmap getShareingBitmap(int textSize) {
            Bitmap.Config config = imageBitmap.getConfig();
            int sourceBitmapHeight = imageBitmap.getHeight();
            int sourceBitmapWidth = imageBitmap.getWidth();
            Paint paint = new Paint();
            paint.setColor(Color.BLACK); // ������ɫ
            TextPaint textpaint = new TextPaint(paint);
            textpaint.setTextSize(textSize); // ���ִ�С
            textpaint.setAntiAlias(true); // �����
            StaticLayout title_layout = new StaticLayout(title.getText().toString(), textpaint,
                    sourceBitmapWidth, Layout.Alignment.ALIGN_CENTER, 1f, 1f, true);
            StaticLayout desc_layout = new StaticLayout("��Ʒ������"+description.getText().toString(), textpaint,
                    sourceBitmapWidth, Layout.Alignment.ALIGN_NORMAL, 1f, 1f, true);
            StaticLayout phone_layout = new StaticLayout("��ϵ�绰��"+phone.getText().toString(), textpaint,
                    sourceBitmapWidth, Layout.Alignment.ALIGN_NORMAL, 1f, 1f, true);
            Bitmap share_bitmap = Bitmap.createBitmap(sourceBitmapWidth, sourceBitmapHeight +
                    title_layout.getHeight() + desc_layout.getHeight() + phone_layout.getHeight(),
                    config);
            Canvas canvas = new Canvas(share_bitmap);
            canvas.drawColor(Color.WHITE);
            canvas.drawBitmap(imageBitmap, 0, 0, paint); // ����ͼƬ
            canvas.translate(0, sourceBitmapHeight);
            title_layout.draw(canvas);
    
            canvas.translate(0, title_layout.getHeight());
            phone_layout.draw(canvas);
    
            canvas.translate(0, phone_layout.getHeight());
            desc_layout.draw(canvas);
            return share_bitmap;
        }

 �ȼ��������������ݵĸ߶ȣ�Ȼ�󹹽�ͼƬ�Ĵ�С�����ư�ɫ��������������ͼƬ��������ͼƬ�»������֡�

