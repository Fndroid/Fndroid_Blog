# Android����ѧϰ֮·-����ˢ����ô����

��Ϊ����Ŀ����漰���������ȡ���ݣ���ô��Ȼ�ٲ��˵ľ�������ˢ�µĹ��ܣ������ķ���һ�����Լ�ȥ�Զ���ListView����RecyclerView����дOnTouch����OnScroll������ʵ�����Ƶļ���Ȼ�󲥷Ŷ������ˢ�½���

����˵����һ��Google�ٷ��ṩ������ˢ�²��֣����ֽ���SwipeRefreshLayout���ҵ�������ֵ�ʱ�������ϲ�����Ⱑ����������¼һ������ô�á�

�����һ��Ч��ͼ�ȣ������������СȦȦ��

![](http://images2015.cnblogs.com/blog/852227/201603/852227-20160314150516631-118881221.gif)

��������Ҫ�������������������Ҫˢ�µĿؼ�֮�⣬������RecyclerView

    
    
    <android.support.v4.widget.SwipeRefreshLayout
            android:id="@+id/reglost_srl"
            android:layout_width="match_parent"
            android:layout_height="match_parent">
    
            <android.support.v7.widget.RecyclerView
                android:id="@+id/reglost_rv"
                android:layout_width="match_parent"
                android:layout_height="match_parent" />
    
        </android.support.v4.widget.SwipeRefreshLayout>

��������Ҫ��ʾ��������ˢ�����С������ʱ����Ҫ���������������

    
    
    public void setRefreshing(boolean refreshing)

���ǣ�����Ǹտ�ʼ��ʱ������Ҫ��ʾ������صĶ���������ֱ�Ӱ�true�����������Ȼ����ã���Ϊ������Դ�����п��Կ���SwipeRefreshLayout�е�һ����ΪmOriginalOffsetTop�ı���û�б���ʼ�������Զ����ǿ�����ʾ�������ģ���ôӦ����������ʾ�أ�������ķ���

    
    
    refreshlayout.post(new Runnable() {
                @Override
                public void run() {
                    refreshlayout.setRefreshing(true);
                }
            });

**���£����ʹ��24.2.0���߸��µ�Support Library����ô����ֱ�ӵ���setRefreshing(true)���ɡ�**

Ȼ�����ǻ���Ҫ���������������һ������

    
    
    refreshlayout.setOnRefreshListener(this);
    
    
    @Override
        public void onRefresh() {
            refreshData(); // refresh the view
        }

�����ݼ�������ˣ��Ѷ������أ��ܼ�

    
    
    refreshlayout.setRefreshing(false);

�������ʵ�Ѿ�������ȫ�����÷��ˣ������������Ƿ��㣬���ǣ����Ǿ�û��һ�������ˣ��Բ�ע�⣬��ʵ���ǻ�����һЩ����ġ�

�����ϵĺܶ�����У����ٵĿ����߶�ϲ���ڴ������������ǰ�����ݼ�����գ�Ҳ������onRefresh�������ȵ���data.clear()��������������һ�����⣬������ڼ��ص�ʱ���������ϻ������棬App�ͻ�ֱ��Crash������ͼ

![](http://images2015.cnblogs.com/blog/852227/201603/852227-20160314151719099-414844062.gif)

���������ʵҲ�ܼ���Ϊ������ǰ���������ˣ������б�û��ˢ�£�������������Ļ��ͻ�ȥ����data�е����ݣ�ֱ�Ӿ�Խ���ˡ�

������ȷ������Ӧ������ִ�������������ݣ������ݼ������˾���ռ��ϣ��������벢ˢ�¡�



