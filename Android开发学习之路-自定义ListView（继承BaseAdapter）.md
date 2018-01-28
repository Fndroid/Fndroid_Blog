# Android����ѧϰ֮·-�Զ���ListView���̳�BaseAdapter��

����ѧ��һ����ϲ����̣�ϲ���ȸ裬ϲ��Android������ѡ��ķ�����Ȼ��AndroidӦ�ÿ�����������һƪ��ϣ���Ժ���и���Ľ�����

�������һ������App��ʱ����Ҫһ��Activity����ʾÿ���µ����Ѹ�����Ŀ�ı�����Activity����Ҫ�õ�һ��ListView��ListView�а���һ��TextView����ʾ���͵����ƣ�һ��TextView����ʾ��ռ������һ��ProgressBar����ʾ����������ÿ����Ŀ�ı�����������������ͼ������ֻ�ṩʵ�ַ���������Ч���ݲ��ṩ��

![](http://images2015.cnblogs.com/blog/852227/201512/852227-20151206194153409-962021546.png)

��Ϊ����Ч���Ƚ��ر���Ҫ�����Լ���ʵ���Զ����Ч����������ʵ������

һ������ÿ��Item�Ĳ��֣�tendency_list_item.xml

    
    
    <?xml version="1.0" encoding="utf-8"?>
    <FrameLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:descendantFocusability="beforeDescendants"
        android:paddingLeft="5dp"
        android:paddingRight="5dp" >
    
        <RelativeLayout
            android:layout_width="fill_parent"
            android:layout_height="wrap_content"
            android:descendantFocusability="afterDescendants" >
    
            <TextView
                android:id="@+id/tendency_title"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:layout_marginBottom="5dp"
                android:layout_marginLeft="10dp"
                android:layout_marginTop="15dp"
                android:textSize="15sp" />
    
            <ProgressBar
                android:id="@+id/tendency_progressBar"
                style="?android:attr/progressBarStyleHorizontal"
                android:layout_width="fill_parent"
                android:layout_height="wrap_content"
                android:layout_below="@id/tendency_title"
                android:layout_marginBottom="15dp"
                android:layout_marginLeft="10dp"
                android:layout_marginRight="10dp"
                android:max="100"
                android:progressDrawable="@drawable/progress_style" />
    
            <TextView
                android:id="@+id/tendency_total"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:layout_alignParentRight="true"
                android:layout_marginRight="10dp"
                android:layout_marginTop="15dp"
                android:textSize="15sp" />
        </RelativeLayout>
    
    </FrameLayout>



��������Ŀ������ӣ����Ǽ򵥵�����TextView��һ��ProgressBar����

��������һ���࣬�̳�BaseAdapter��������һ���������࣬Ҳ�����ǵ�ǰ��activity�н�����һ���ڲ��࣬��дһЩ�������ɣ����ﲼ�ֱ�Ӳ�����ˣ�����Ƿ�װ��һ���ⲿ�࣬����ͨ������������

    
    
    public class myAdapter extends BaseAdapter {
            private Context context; // ��ǰ��Context
            private LayoutInflater mInflater;  // ͨ��LayoutInflater������ÿ��item�Ĳ���
            private double total; // ����Ŀ�йأ����ܵĽ��������������Ҳ��ʹ���ڲ����ԭ��
            List<Map<String, Object>> map; // �������Դ
    
            public myAdapter(Context context, List<Map<String, Object>> map,
                    double total) {
                this.context = context;
                this.mInflater = LayoutInflater.from(context);
                this.total = total;
                this.map = map;
            }
    
            // ��д�����������õ�ǰListView���ж��ٸ�Item
            @Override
            public int getCount() {
                return listmap.size();
            }
            
            // ��д�����������õ�ǰ��position
            @Override
            public Object getItem(int position) {
                return position;
            }
    
            @Override
            public long getItemId(int position) {
                return position;
            }
    
            // һ��������
            public class viewHolder {
                TextView title;
                ProgressBar progress;
                TextView sum;
            }
    
            // һ������Ҫ�ķ�������д����������ӳ�䵽������
            @Override
            public View getView(int position, View view, ViewGroup parent) {
                view = mInflater.inflate(R.layout.tendency_list_item, null); // ����Item�Ĳ���
                viewHolder vh = new viewHolder(); // ����������
                vh.title = (TextView) view.findViewById(R.id.tendency_title); // ͨ��Id��ȡ����
                vh.progress = (ProgressBar) view
                        .findViewById(R.id.tendency_progressBar);
                vh.sum = (TextView) view.findViewById(R.id.tendency_total);
    
                vh.title.setText((String) map.get(position).get("type")); // ����ÿ��������������ӳ���ȥ
                double part = (Double) map.get(position).get("total");
                String part2 = String.format("%.0f", part * 100 / total);
                vh.sum.setText(part2 + "%");
                vh.progress.setProgress(Integer.parseInt(part2));
                return view;
            }
        }  
    

 �������ü���

    
    
    myAdapter adapter = new myAdapter(tendency_activity.this, listmap, sum);
    show.setAdapter(adapter);



ԭ����ʵҲ�Ƚϼ򵥣���ʵ������ʱ���ȵ���getCount�����������ж��ٸ�Item��Ȼ��ÿ��position������getView�������������ݵ�ӳ�䣨�������ܲ���ܺã�����Ż��պ��ٲ��䣩��



���д��󣬻���ָ����������л��

