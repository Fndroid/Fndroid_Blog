# Android����ѧϰ֮·-Android N������-�ര��ģʽ

���Ƕ�֪���������µ�Android
Nϵͳ�У�������һ���µĹ��ܣ����Ƕര��ģʽ���ര��ģʽ������������Ļ����ʾ�������ڣ�ÿ��������ʾ�����ݲ�ͬ��Ҳ����˵�����ǿ���һ�鿴���Ӿ磬һ����΢�š�

��������ͨ���ٷ��ṩ��һ��Demo���˽�һ�£���Ϊ�����ߣ���ô�����ǵ�AppҲ����ര��ģʽ��

�����������github��ַ����Ҫ�Ļ�����clone�����߿����˽⣺

    
    
    https://github.com/googlecodelabs/getting-ready-for-android-n



**����ָ���ĵ������Ϊ�������֣�**

**1\. �ര��ģʽ�Ŀ���**

**2\. �ര��ģʽ����**

**3\. �ര��ģʽ�д��µĴ��ڴ���**



����һ��һ�����˽��£�

**1\. �ര��ģʽ�Ŀ���**

Ĭ������£����ǵ�App��������ര�ڵģ����ǣ����û�н������Ե����ã���ϵͳ���׳�һ����ʾ���Ӧ�ÿ��ܲ�֧�ֶര��ģʽ

![](http://images2015.cnblogs.com/blog/852227/201606/852227-20160624105606016-78409403.jpg)

��ô��������ǵ�Ӧ��Ҫ֧�����ģʽ���Ҳ��������Ϣ��������Ҫ��ô���أ�

�ܼ򵥣�ֻ��Ҫ��Activity������ʱ�����һ������resizeableActivity������������ֵΪtrue����`��`

    
    
    <activity
        android:name=".MainActivity"
        android:resizeableActivity="true">
        <intent-filter>
           <action android:name="android.intent.action.MAIN"/>
    ��������<category android:name="android.intent.category.LAUNCHER"/>
        </intent-filter>
    </activity>

������Ե����ûᵼ�����������

**�� ���������������ԣ���ôĬ���������ര��ģʽ�����ǻ�������ͼƬ����ʾ����һ�����е�ʱ��**

**�� ���������������ԣ�������ֵΪtrue����ô�������ര��ģʽ�����Ҳ�����ʾ**

**�� ���������������ԣ�������ֵΪfalse����ô���������ര��ģʽ��ֻ����ȫ����ʾ**



**2\. �ര��ģʽ������**

����������App����ര��ģʽ֮��Appֻ��ռ����Ļ��һ���֣��������ǵ�App���еĽ������£��ٷ�Demo����

![](http://images2015.cnblogs.com/blog/852227/201606/852227-20160624110515485-1527434612.jpg)

���Կ�������App���ϰ벿����һ����ɫ��ͼƬ��������������ʾ�˵�ǰ������״��������������ǲ�������Ӧ����ô�����˶ര��ģʽ֮��������ֵ����ݾͻἸ��ռ���������ڣ����ʱ�����Ǿ���Ҫ����һ�����䣬������ര��ģʽ֮���������һ�����֣������ݽ��������Ű棬�Ա���ʾ��������ݡ�

Ĭ������µĲ��֣�

    
    
     1 <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
     2     android:layout_width="match_parent"
     3     android:layout_height="wrap_content"
     4     android:gravity="center_vertical"
     5     android:minHeight="?android:attr/listPreferredItemHeight"
     6     android:orientation="horizontal"
     7     android:background="@drawable/today_touch_selector">
     8 
     9     <LinearLayout
    10         android:layout_height="wrap_content"
    11         android:layout_width="0dp"
    12         android:layout_weight="7"
    13         android:layout_marginTop="16dp"
    14         android:layout_marginBottom="16dp"
    15         android:layout_marginLeft="60dp"
    16         android:orientation="vertical">
    17 
    18         <TextView
    19             android:id="@+id/list_item_date_textview"
    20             android:layout_width="match_parent"
    21             android:layout_height="wrap_content"
    22             android:textAppearance="?android:textAppearanceLarge"
    23             android:fontFamily="sans-serif-condensed"
    24             android:textColor="@color/white" />
    25 
    26         <TextView
    27             android:id="@+id/list_item_high_textview"
    28             android:layout_width="match_parent"
    29             android:layout_height="wrap_content"
    30             android:textSize="72sp"
    31             android:fontFamily="sans-serif-light"
    32             android:textColor="@color/white" />
    33 
    34         <TextView
    35             android:id="@+id/list_item_low_textview"
    36             android:layout_width="match_parent"
    37             android:layout_height="wrap_content"
    38             android:textColor="@color/white"
    39             android:textSize="36sp"
    40             android:layout_marginLeft="8dp"/>
    41     </LinearLayout>
    42 
    43     <LinearLayout
    44         android:layout_height="wrap_content"
    45         android:layout_width="0dp"
    46         android:layout_weight="5"
    47         android:layout_marginRight="16dp"
    48         android:orientation="vertical"
    49         android:gravity="center_horizontal|bottom">
    50 
    51         <ImageView
    52             android:id="@+id/list_item_icon"
    53             android:layout_width="wrap_content"
    54             android:layout_height="wrap_content"
    55             android:layout_gravity="center_horizontal"/>
    56 
    57         <TextView
    58             android:id="@+id/list_item_forecast_textview"
    59             android:layout_width="wrap_content"
    60             android:layout_height="wrap_content"
    61             android:fontFamily="sans-serif-condensed"
    62             android:layout_gravity="center_horizontal"
    63             android:textAppearance="?android:textAppearanceLarge"
    64             android:textColor="@color/white"/>
    65     </LinearLayout>

������Ҫ�������������������˱���ͼ�����ͼƬ�����Ǹ����ǿ�����ɫ����һ�ţ����涨����һЩTextView����ʾ��Ϣ��������һ��ImageView����ʾ������ͼ�꣬���˽��²��ּ��ɡ�

��Ϊ�����õ�Fragment������ֱ�ӿ����̲�������������Ǵ�res�µ�values-sw400dp��Ȼ��������refs.xml�ļ����������£�

    
    
    <resources>
        <item type="layout" name="fragment_detail">@layout/fragment_detail_wide</item>
        <item type="layout" name="list_item_forecast_today">@layout/list_item_forecast_today_big</item>
    </resources>

���ǽ�����ƶ����ڶ���Item��name���Ե�ֵ�У�Ȼ���� _ **Alt+F7**_ �ҵ���Ŀ���õ����value�ĵط���

![](http://images2015.cnblogs.com/blog/852227/201606/852227-20160624112102125-368321149.png)

���Կ�����ֻ��һ���ط�ʹ�������value�����ǵ��ȥ���Կ��������������ʵ�Ǳ�������һ��CursorAdapter�У�����Ӧ�þ�֪���ˣ���������Ǳ�����һ��ListView��ͷ����ʹ�á�

�����Ȳ��ܹ��������ʵ�ֵģ�����ֻ��Ҫ֪��������ֻᱻ���ڽ����е�ListView�е�ͷ���оͿ����ˡ����ʱ�������ٿ������folder���ļ���values-
sw400dp��sw400dp�ͱ��������value������Ļ��̱ߴ��ڵ���400dp��ʱ����Ч��ǰ������������ͬ��value�ļ��У���

��Ϊ�ڶര��ģʽ������£�ÿ�����ڷֵĴ�С�������û����Ƶģ�����ͨ���м�Ļ������ı��������ڵĴ�С���������û����������ϻ������п��ܻ�ᵼ����̱�С��400dp����ˣ����ǿ����ڹ��̵�res�´���һ����С��values�ļ���Ϊvalues-
sw220dp�������ٴ���һ�������ļ�������Ŀؼ�id���������Ĳ���һ�£�����Demo���Ѿ�������һ�����֣�������list_item_forecast����������values-
sw220dp�´���һ��refs.xml���ļ����ļ��������£�

    
    
    <resources>
        <item type="layout" name="list_item_forecast_today">@layout/list_item_forecast</item>
    </resources>

���е�Ч��������ʾ�����Կ���������Ļ��̱ߴ���220dp��С��400dp��ʱ�򣬻���ʾ����ͼ�Ĳ��֣�

![](http://images2015.cnblogs.com/blog/852227/201606/852227-20160624140457328-1216252768.jpg)
![](http://images2015.cnblogs.com/blog/852227/201606/852227-20160624140518891-48529125.jpg)

���ﻹҪע��һ�����⣬������ǰ������涨���220dp����������ô�����Ļ���������������ᵼ����С��С��220dp�����ʱ���ֻ�ָ�����ߵ�������֣��������취�ǰ�220dp���õĸ�С����100dp�����ɡ�

���ﲹ��һ��Ч����

![](http://images2015.cnblogs.com/blog/852227/201606/852227-20160624143058922-697042443.gif)

** **

**3\. �ڶര���д�һ��Activity**

�ڹٷ���Demo�У�����ͨ�����ϽǵĲ˵��е�"Map Location"���򿪵�ͼApp�����ʱ��������������ã���ͼApp���ڵ�ǰ�����С�����д򿪡�

![](http://images2015.cnblogs.com/blog/852227/201606/852227-20160624143843860-292273952.gif)

��ô���ǿɲ�������ϵͳ�������������ڴ��أ���Ϊ���ǵ��û����Բ����������ǵ�App���Ե�ͼApp���в������ȸ�ٷ�Ҳ���ṩ��������ܣ����ұȽϼ򵥡�

�����ҵ�ForecastFragment.class����࣬��λ��206�У��������£�

    
    
    Intent intent = new Intent(Intent.ACTION_VIEW);
    intent.setData(geoLocation);

������������Ҫ��һ��Activity����������ֻ��Ҫ�����Intent����һ����ǩ�������������£�

    
    
    Intent intent = new Intent(Intent.ACTION_VIEW);
    intent.setData(geoLocation);
    intent.addFlags(Intent.FLAG_ACTIVITY_LAUNCH_ADJACENT);

���ʱ�������ٴ�����App���򿪵�ͼApp���ᷢ�֣���ͼApp������һ�������б��򿪡�

![](http://images2015.cnblogs.com/blog/852227/201606/852227-20160624143655485-1334242574.gif)

