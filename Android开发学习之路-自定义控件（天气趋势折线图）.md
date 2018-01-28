# Android����ѧϰ֮·-�Զ���ؼ���������������ͼ��

֮ǰд�˸�����APP����4��Ԥ����5����ʷ��Ϣ����������Ҫ��Ҫ��һ������ͼ����ʾһ�������仯���ƣ��ѵ��пգ���д��һ�£�������Щ��¼���Դ�����ʹ�������¡�

�ȷ�һ��Ч����

![](http://images2015.cnblogs.com/blog/852227/201607/852227-20160724094735060-398544444.gif)

�ؼ����ݱȽϼ򵥣�����һ����ͨ������ͼ�����·ֱ�������֣������ʱ����ʾ�����¶ȵĲ�ֵ��

* * *



����һ����̳���View��������������췽����

    
    
    public class TrendGraph extends View {
        public TrendGraph(Context context) { // ��java�����д�������
            super(context);
        }
    
        public TrendGraph(Context context, AttributeSet attrs) { // ��xml�д�������
            super(context, attrs);
        }
    }





��Ϊ���ﲻ��Ҫ����wrap_content�����������onMeasure����������д���ؼ�����onDraw����onDraw������ʵҲ�����ѣ�ֻ��Ҫȷ����ÿ����ľ���λ�þͺã���Ϊ����Ҳ����Ҫ������꣬����Ƚφ��£������Թ���

    
    
     1     @Override
     2     protected void onDraw(Canvas canvas) {
     3         super.onDraw(canvas);
     4         if (mElements == null || mElements.size() == 0) {
     5             return;
     6         }
     7         double max_up = getMaxUp();
     8         double min_down = getMinDown();
     9         canvas.setDrawFilter(mDrawFilter);
    10         mPaint.setStrokeWidth(lineWeith);
    11         float width = getWidth();
    12         float grap = width / mElements.size();
    13         float textSize = mTextPaint.getTextSize();
    14         int textMargin = circleRadius * 2;
    15         float margin_top = textSize + 2 * textMargin;
    16         Log.d(TAG, "onDraw: " + margin_top + "|" + textSize);
    17         float height = getHeight() - 2 * margin_top;
    18 
    19         for (int i = 0; i < mElements.size() - 1; i++) {
    20             float startX = i * grap + grap / 2;
    21             float stopX = (i + 1) * grap + grap / 2;
    22             float startY = (float) (max_up - mElements.get(i).getUp()) / (float) (max_up -
    23                     min_down) * height + margin_top;
    24             float stopY = (float) (max_up - mElements.get(i + 1).getUp()) / (float) (max_up -
    25                     min_down) * height + margin_top;
    26 
    27             canvas.drawText((int) mElements.get(i).getUp() + "��", startX - textSize, startY -
    28                     textMargin, mTextPaint);
    29             canvas.drawCircle(startX, startY, circleRadius, mPaint);
    30             canvas.drawLine(startX, startY, stopX, stopY, mPaint);
    31             if (i == mElements.size() - 2) {
    32                 canvas.drawText((int) mElements.get(i + 1).getUp() + "��", stopX - textSize, stopY
    33                         - textMargin, mTextPaint);
    34                 canvas.drawCircle(stopX, stopY, circleRadius, mPaint);
    35             }
    36 
    37             startY = (float) (max_up - mElements.get(i).getDown()) / (float) (max_up - min_down) *
    38                     height + margin_top;
    39             stopY = (float) (max_up - mElements.get(i + 1).getDown()) / (float) (max_up -
    40                     min_down) * height + margin_top;
    41             canvas.drawText((int) mElements.get(i).getDown() + "��", startX - textSize, startY +
    42                     textSize + textMargin, mTextPaint);
    43             canvas.drawCircle(startX, startY, circleRadius, mPaint);
    44             canvas.drawLine(startX, startY, stopX, stopY, mPaint);
    45             if (i == mElements.size() - 2) {
    46                 canvas.drawText((int) mElements.get(i + 1).getDown() + "��", stopX - textSize,
    47                         stopY + textSize + textMargin, mTextPaint);
    48                 canvas.drawCircle(stopX, stopY, circleRadius, mPaint);
    49             }
    50         }
    51     }





���ǵ���Ҫ�����û����м򵥵����ã������Ĵ�С�����ִ�С�ȵȣ����Զ���һЩ�Զ������ԣ�res/values/attr.xml����

    
    
    <?xml version="1.0" encoding="utf-8"?>
    <resources>
        <declare-styleable name="TrendGraph">
            <attr name="lineWidth" format="dimension"/>
            <attr name="circleRadius" format="dimension" />
            <attr name="textSize" format="dimension" />
            <attr name="textColor" format="reference" />
        </declare-styleable>
    </resources>



formatָ�����Եĸ�ʽ��ָ��Ϊdimension���ǳߴ磬ȡֵ��λ��dp��sp��px�ȵȣ���reference�������ã���һ����xml������������Դ��д������@string/app_name�������������ͣ��������в����ĵ���



���Զ������Խ��н����õ������������Ҫ�����涨��ĵڶ������췽���н��У��������£�

    
    
        public TrendGraph(Context context, AttributeSet attrs) {
            super(context, attrs);
            TypedArray array = getContext().obtainStyledAttributes(attrs, R.styleable.TrendGraph);
            circleRadius = array.getDimensionPixelSize(R.styleable.TrendGraph_circleRadius, 5);
            lineWeith = array.getDimensionPixelSize(R.styleable.TrendGraph_lineWidth, 3);
            mTextPaint.setTextSize(array.getDimensionPixelSize(R.styleable.TrendGraph_textSize, 35));
            mTextPaint.setColor(array.getColor(R.styleable.TrendGraph_textColor, Color.BLACK));
            array.recycle();
        }

getDimensionPixelSize��������ͨ�������ֵ��ת��Ϊ��������أ�px��ֵ��Ҳ����ȥ�����ֶ�ת�����鷳������Ҫע�⣬���е�defaultValue��Ȼ��px��



���ţ��Ϳ���ͨ��xmlָ����Щ���ԣ��ڲ����м��������ռ䣺

    
    
    xmlns:app="http://schemas.android.com/apk/res-auto"



��Android Studio���Զ����룬���ҿ��Բ�ȫ�õ�������ʹ�ã�

    
    
        <com.fndroid.byweather.views.TrendGraph
            android:id="@+id/tg"
            android:layout_width="match_parent"
            app:textColor="@color/colorAccent"
            app:textSize="22sp"
            app:circleRadius="2dp"
            android:layout_height="200dp"/>



* * *





������һ���¼��������ڵ��View��ʱ����лص���

�� ����ӿڣ�

    
    
        public interface onItemClickListener{
            void onItemClick(View view, Element element);
        }



�� ��View����ӽӿڶ��󣬲�����setter������

    
    
    public class TrendGraph extends View {
    
        private onItemClickListener mOnItemClickListener;
    
        // ʡ�Դ���
    
        public void setOnItemClickListener(onItemClickListener onItemClickListener) {
            mOnItemClickListener = onItemClickListener;
        }
    
    }



�� ����onTouchEvent����д�÷������������£�

    
    
        @Override
        public boolean onTouchEvent(MotionEvent event) {
            int viewWidth = getWidth();
            int itemWidth = viewWidth / mElements.size();
            int viewHeight = getHeight();
            boolean isMove = false; // �����������Ϊһ��NestedScrollView������Ϊ�˱��⻬��ʱҲ�����������������
    
            switch (event.getAction()) {
                case MotionEvent.ACTION_MOVE:
                    isMove = true;
                    break;
                case MotionEvent.ACTION_UP:
                    if (!isMove){ // �ж�ֻ�е��ʱ���лص�
                        int position = (int) (event.getX() / itemWidth); // ȡ�õ����λ��
                        mOnItemClickListener.onItemClick(this, mElements.get(position)); // �ص�
                    }
                    break;
            }
    
            return true;
        }



�� ��Activity�У����м������ã�������

    
    
    ����historyGraph.setOnItemClickListener(this);
    
    
        @Override
        public void onItemClick(View view, TrendGraph.Element element) {
            int dt = (int) (element.getUp() - element.getDown());
            Snackbar.make(root, "�����²�Ϊ��" + dt + "��", Snackbar.LENGTH_SHORT).show();
        }





Ч����ɣ���ӭ��ҹ�ע������



