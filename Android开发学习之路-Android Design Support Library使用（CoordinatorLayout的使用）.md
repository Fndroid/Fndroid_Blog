# Android����ѧϰ֮·-Android Design Support Libraryʹ�ã�CoordinatorLayout��ʹ�ã�

Ч��ͼ��

![](http://images2015.cnblogs.com/blog/852227/201602/852227-20160219093023206-2086679262.gif)

��������ͼ������Ч���ǣ�һ���Ƕ�����ͼƬ�����ϻ�֮�����������������ʾ��һ��ToolBar��ToolBar������ActionBar������ֻ��ToolBar�Ǽ���Material
Desig�Ŀ⣩����һ���ǵײ��������ť�������ť���֮������һ��SnackBar����ToastҪǿ����Ϊ�����趨����ļ����¼������ڰ�ť���֮��SnackBar����֮��ť���Զ��������ƶ����ⱻ�ڵ�������CoordinatorLayout��һ�����ܡ�

Android Design Support Library��һ���ȸ��ṩ������ʵ��Material
Design�����¼��MD����һ�����ݿ⣬��ʵ�ֵ�Ч��������������android5.0���ϵ��豸���У���ʵ���õ������е�һЩ�ؼ�������SnackBar��FloatingActionButton�ȵ�

����һ�²����������ͣ�

    
    
     1 <?xml version="1.0" encoding="utf-8"?>
     2 <android.support.design.widget.CoordinatorLayout
     3     android:id="@+id/root"
     4     xmlns:android="http://schemas.android.com/apk/res/android"
     5     xmlns:app="http://schemas.android.com/apk/res-auto"
     6     android:layout_width="match_parent"
     7     android:layout_height="match_parent">
     8 
     9 
    10     <android.support.v4.widget.NestedScrollView
    11         android:layout_width="match_parent"
    12         android:layout_height="match_parent"
    13         app:layout_behavior="@string/appbar_scrolling_view_behavior">
    14 
    15         <LinearLayout
    16             android:layout_width="match_parent"
    17             android:layout_height="match_parent"
    18             android:orientation="vertical">
    19 
    20             <!-- ����Ϊ��ʵ�����϶�������Ӷ��ImageView��ʡ��...-->
    21         </LinearLayout>
    22 
    23     </android.support.v4.widget.NestedScrollView>
    24 
    25     <android.support.design.widget.AppBarLayout
    26         android:layout_width="match_parent"
    27         android:layout_height="250dp"
    28         android:theme="@style/ThemeOverlay.AppCompat.Dark.ActionBar">
    29 
    30         <android.support.design.widget.CollapsingToolbarLayout
    31             android:id="@+id/collapsingtoolbarlayout"
    32             android:layout_width="match_parent"
    33             android:layout_height="match_parent"
    34             app:contentScrim="#636EEE"
    35             app:title="Windows10"
    36             app:expandedTitleMarginStart="50dp"
    37             app:layout_scrollFlags="scroll|exitUntilCollapsed">
    38 
    39             <ImageView
    40                 android:layout_width="match_parent"
    41                 android:layout_height="match_parent"
    42                 android:scaleType="fitXY"
    43                 android:src="@drawable/my"
    44                 app:layout_collapseMode="parallax"/>
    45 
    46 
    47             <android.support.v7.widget.Toolbar
    48                 android:id="@+id/toolbar"
    49                 android:layout_width="match_parent"
    50                 android:layout_height="?attr/actionBarSize"
    51                 android:minHeight="20dp"
    52                 app:layout_collapseMode="pin"
    53                 app:popupTheme="@style/Theme.AppCompat.Light"
    54                 app:theme="@style/ThemeOverlay.AppCompat.Dark.ActionBar"
    55                 />
    56         </android.support.design.widget.CollapsingToolbarLayout>
    57 
    58     </android.support.design.widget.AppBarLayout>
    59 
    60     <android.support.design.widget.FloatingActionButton
    61         android:id="@+id/fab_btn"
    62         android:layout_width="wrap_content"
    63         android:layout_height="wrap_content"
    64         android:layout_gravity="bottom|right"
    65         android:layout_margin="10dp"
    66         android:src="@mipmap/ic_launcher"
    67         app:fabSize="normal"/>
    68 
    69 </android.support.design.widget.CoordinatorLayout>

�������ʹ��һ��CoordinatorLayout���������֣��ڲ���������ʹ�������ռ���Ϊapp�����������һ������ǿ���FrameLayout������������ֵ����������ǲ������������Ч�������Կ��������������Ƿ���һ��FloatingActiongButton����Ϊ����ؼ�������������У�����������Ǹ����趨����¼�����������һ��SnackBar������������������Ч���ˡ�

����������ͼƬ���������Ч�����������ʹ��һ��AppBarLayout�����������һ�������LinearLayout����Ϊ�˼���MD����Ƶ�һ�����֣���CoordinatorLayout��ʹ������������һЩЭ����Ч��������API���Կ��������÷����£�

�ܷ�ǽ�ľͿ���http://developer.android.com/reference/android/support/design/widget/AppBarLayout.html

![](http://images2015.cnblogs.com/blog/852227/201602/852227-20160217192315347-1045291977.png)![](http://images2015.cnblogs.com/blog/852227/201602/852227-20160217192327316-1171944962.png)

�����Ѿ�����������Ч��ʵ�ֵĿ���ˣ�����������Ҫ��ToolBarLayout�����һ��CollapsingToolBarLayout��������������Ʒ�����"�����Ĺ���������"��Ī��ϲ�й���������ʹ������Ŀ�ľ���ʵ��ͼƬ������ʧ��Ȼ����ʾToolBar,������������

![](http://images2015.cnblogs.com/blog/852227/201602/852227-20160217193750409-2046767142.png)

    
    
    app:contentScrim="#636EEE"
    app:title="Windows10"
    app:expandedTitleMarginStart="50dp"
    app:layout_scrollFlags="scroll|exitUntilCollapsed"

 ������ֵ��ĸ����Էֱ������������ɫ���������֣��򿪺�ı�������Լ�ͼƬ������Ч����ÿһ����ʲô�����Լ�����һ�¡�

�������������������һ��ImageView��һ��ToolBar�Ϳ����ˣ����ڻ�����Ч���Ϳ����Լ�����һ�£�API�ǿ��Կ����ġ�

����һ��Activity�Ĵ��룺

    
    
    public class CoordinatorLayoutActivity extends AppCompatActivity {
        private FloatingActionButton ftb_btn;
        private CoordinatorLayout root;
        private Toolbar toolbar;
        private CollapsingToolbarLayout collapsingToolbarLayout;
    
        @Override
        protected void onCreate(@Nullable Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_coordinatorlayout);
            ftb_btn = (FloatingActionButton) findViewById(R.id.fab_btn);
            root = (CoordinatorLayout) findViewById(R.id.root);
            toolbar = (Toolbar) findViewById(R.id.toolbar);
            collapsingToolbarLayout = (CollapsingToolbarLayout) findViewById(R.id.collapsingtoolbarlayout);
            setSupportActionBar(toolbar);
    
            ftb_btn.setOnClickListener(new View.OnClickListener() {
                @Override
                public void onClick(View v) {
                    Snackbar.make(root,"����һ��Snackbar",Snackbar.LENGTH_LONG).setAction("��֪����", new View.OnClickListener() {
                        @Override
                        public void onClick(View v) {
    
                        }
                    }).show();
                }
            });
        }
    }





���˵������ЩЧ����ʵ�ֶ�����һЩ�������Ƚ��µĲ��ֺͿؼ�������ʵ������Щ�ؼ���������ƽ��ʹ�õ��ģ���ʵֻ�ǰѾɵĶ�����װ�˼�����һЩ�µ����ԣ�Ȼ��ϵͳ�Ѿ��������ڵײ�ʵ����һЩ����Ч�������⣬��CoordinatorLayout��ʹ�õĸ��ֿؼ�Э������Ч������ҪһЩ��������Ŀؼ������ǲ��������������µ�������ԣ����ԾɵľͲ������ˡ�

