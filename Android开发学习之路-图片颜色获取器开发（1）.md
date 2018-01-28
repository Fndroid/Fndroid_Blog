# Android����ѧϰ֮·-ͼƬ��ɫ��ȡ��������1��

ϵ�е�һƪ���Ӽ򵥵Ŀ�ʼ��һ��һ��������С��Ŀ��

��ɫ��ȡ����ͨ������ͼƬ�е�ÿ�����ص���ɫ������������ͼƬ��������ɫ������������ɫ�����ǿ�������ͼƬ���ڿ�Ƭ�ı������߱�����ɫ����������и���ǿ�ҡ�

����Ȥ�Ŀ���ѧϰ��ʹ�ùȸ��ṩ��Palette��Ҳ����ͬ���Ĺ��������͵�ַ��http://www.cnblogs.com/Fndroid/p/5201236.html

�ȿ�Ч��ͼ��

![](http://images2015.cnblogs.com/blog/852227/201605/852227-20160507110912841-314403128.gif)

����ԭ��Ƚϼ򵥣����ǻ�ȡͼƬ���������ص���ɫ��Ȼ��ͳ�ƣ���ͳ�Ƶ���Ŀ����Ȼ�󷵻ظ��û���

��������Ҫ��ע�⼸�����⣺

�� ��ȡ��ɫ�Ĺ��̻᲻�ᵼ��UI�߳̿��٣�

�� ��ôʵ����������Ż������о�����

��Ϊ���ǵ���ͼƬ���ؽ϶��ʱ�򣬷������ܲ���������ɣ����Է�������Ӧ�������߳�����ɣ���������UI�̡߳���Σ�ͳ�ƺ����򶼿���ͨ��Java�ṩ�����ݽṹ����ʵ�֣����Ҳ������������أ�ʵ�ֹ���Ϊ�ȡ�



�� ��Android�У�ͼƬһ�����Bitmap����ʾ����Bitmap����һ������getPixels�ķ�����

    
    
    public void getPixels(@ColorInt int[] pixels, int offset, int stride, int x, int y, int width, int height)

�����ֱ��ǣ�  

pixiels�����ʶ�����ɫ������

offset���������ʼ�±�

stride��ÿ�е�������������һ����200*200��ͼƬ����stride����Ϊ200��ʱ��pixels[200]Ϊ�ڶ��еĵ�һ��������ɫ����stride����Ϊ400��ʱ��pixels[200]Ϊ�������Ϣ����������ɫ�����ڶ��еĵ�һ�����ص���ɫӦ����pixels[400]

x��y����ʼ��x��y

width��height����Ҫ��ȡ��ɫ�Ŀ�Ⱥ͸߶ȣ���x��y����һ������

�� ��ȡ����ɫ֮�󣬶���ɫ����ͳ�ƣ���Ϊ�õ���һ�����飬���Զ��������ͳ�ƣ��������ͬ��Ҫ��һ���ó�ÿ����ɫ���ֵĴ������㷨������

    
    
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

�� ������Ϊ��Ҫ����һ����������飬���﷽��Ŀ�����TreeMapֱ���ţ�Ҫע��TreeMap�Ƕ�key����ģ�����Ĭ��������

    
    
    TreeMap<Integer, Integer> sortedColors = new TreeMap<>();
    for (Map.Entry<Integer, Integer> entry : colors.entrySet()) {
        sortedColors.put(entry.getValue(), entry.getKey());
    }

�� ����һ���������飬����򵥵ı���һ�´浽ArrayList�м���

    
    
    ArrayList<Integer> result = new ArrayList<>();
    for (Map.Entry<Integer, Integer> entry : sortedColors.entrySet()) {
        result.add(entry.getValue());
    }

�� ���ֱ�ӵ��ú����󣬻��������UI������Ҫ�����߳���������������򵥵Ļ�ֱ��newһ��Thread

�� �����߳����������ִ����ϣ�ͨ��Handler������Ϣ��֪ͨUI����



������������ࣺ

    
    
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

���Կ������ڹ����ʱ����Ҫһ��Handler�������Ǵ�����ͼƬ��ɫ���ݵ�ʱ�򣬾���ͨ�����Handler��֪ͨUI�̣߳�Ȼ���ͳ�Ƶ����ݴ浽Message.obj�У������߳���ֱ��ȡ�����ɵõ���ɫ�������ǰ��������еģ������õ�������ɫ����Ҫȡ��size-1�±��ֵ

    
    
    private Handler mHandler = new Handler() {
            @Override
            public void handleMessage(Message msg) {
                if (msg.what == ColorCaptureUtil.SUCCESS) {
                    ArrayList<Integer> colors = (ArrayList<Integer>) msg.obj;
                    color.setBackgroundColor(colors.get(colors.size() - 1));
                }
            }
        };



��һ���ֵ����ݱȽϼ򵥣�������Ϊ�Ƚϼ򵥣����Խ�����������һЩ������Ҫ�����

�� �����ٶ����⣬�����Ƿ�Ҫʹ�����������㷨���Ƿ���Ҫ����ȫ�������ص㣿�ܷ���оֲ�������

�� ��ͼƬ��Ч��ͼ�еĵ����ţ����£�һ����ʱ��ͳ�Ƴ�������������ɫ�Ƿ����

![](http://images2015.cnblogs.com/blog/852227/201605/852227-20160507142716437-2050777961.png)

�� ֱ��ʹ��new Thread�Ƿ�������д���ͼƬ��Ҫ�����ʱ��᲻��������⣿



���������ƪ����ӭָ��������



