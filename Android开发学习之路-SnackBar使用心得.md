# Android����ѧϰ֮·-SnackBarʹ���ĵ�

SnackBar��DesignSupportLibrary�е�һ����Ҫ�Ŀؼ��������ڽ���������ʾһЩ�ؼ���Ϣ����Toast��ͬ�ĵط���SnackBar�����û����һ�����������ͬʱ��Ҳ������SnackBar���趨һ��Action�����û������SnackBar����İ�ť��ʱ�򣬿��Խ���һЩ���������ԣ����ܾ����Ǻ�ǿ��ġ�

SnackBar�Ĺ��죺

    
    
    // �����ֱ��Ǹ���������ʾ��Ϣ������ʱ��  
    public static Snackbar make(@NonNull View view, @NonNull CharSequence text,@Duration int duration)

SnackBar�ĳ��÷�����

    
    
    // ���ڸ�SnackBar�趨һ��Action�����֮���ص�OnclickListener�е�Onclick����  
    public Snackbar setAction(CharSequence text, final View.OnClickListener listener)
    
    
    // �����趨Action��������ɫ  
    public Snackbar setActionTextColor(@ColorInt int color)
    
    
    // �趨��ʾ������  
    public Snackbar setText(@NonNull CharSequence message)
    
    
    // չʾSnackBar  
    public void show()
    
    
    // ���SnackBar  
    public void dismiss()
    
    
    // ���ûص�������OnDismissed����OnShown  
    public Snackbar setCallback(Callback callback)

����ʹ�þͲ�˵�ˣ�����ֱ��˵һ������Action������Callback

Ҫ��ʵ��һ�������Ĳ������û�����б���ÿһ���ɾ����ť֮��ɾ�������ݿ��У���Ӧ���У�Ȼ�󵯳�SnackBar��ʾ�Ƿ��������ѡ���ǣ���ָ�ɾ�����У�����ɾ�����У�Ч������

![](http://images2015.cnblogs.com/blog/852227/201603/852227-20160301194841611-1523472700.gif)

˼·��

���ȶ�����Ҫɾ��������н��б��ݣ�Ȼ���ڵ��ɾ����ť��ʱ�����ɾ���������ݴ�Դɾ����Ȼ��ˢ��

�ڵ���SnackBar���趨Action���������Yes���Ͱ����ݲ��뵽Դ�У�ˢ��

���趨CallBack������SnackBar����ʧ�¼���������ǵ��Action����ʧ�������ݴ�SQLite��ɾ��

ע�⣺����ΪʲôҪ�趨��������Ϊ��������м�������Ҫ��ɾ����ʱ��������ݿ⣬������ʱ��������ݿ�Ĳ�������������б��е���Ŀλ�ûᷢ���ı�

���ǿ�����Adapter�ж�ɾ����ť���м������������£�

    
    
     1 delete.setOnClickListener(new View.OnClickListener() {
     2             @Override
     3             public void onClick(View v) {
     4                 backupMap = mdata.get(position);
     5                 mdata.remove(position);
     6                 notifyDataSetChanged();
     7                 Snackbar snackBar = Snackbar.make(root, "�Ƿ���ɾ����", Snackbar.LENGTH_LONG);
     8                 snackBar.setAction("YES", new MyOnClickListener(position));
     9                 snackBar.setCallback(new MyCallback());
    10                 snackBar.show();
    11             }
    12         });

��������ɾ����ť�����Ƕ����ݽ��б��ݣ�Ȼ��ɾ������Դmdata��Ӧ�����map��������ˢ�½��档Ȼ���7�й�����һ��SnackBar��ѯ���Ƿ���ɾ�������ѳ���ʱ������ΪLONG�����û�δ�Ķ�������SnackBar����ʧ�ˡ���8���趨�˵����Action����ʾ��ϢΪ"YES"�����ṩ�˵���ļ�������9���趨��Callback���ж�SnackBar����ʧ״̬����10�о�ֱ�Ӱ�SnackBarչʾ������

��������Action�ĵ���¼���

    
    
     1 private class MyOnClickListener implements View.OnClickListener {
     2         private final int position;
     3 
     4         public MyOnClickListener(int position) {
     5             this.position = position;
     6         }
     7 
     8         @Override
     9         public void onClick(View v) {
    10             mdata.add(position, backupMap);
    11             notifyDataSetChanged();
    12         }
    13     }

��������Action�����ñ��ݵ����ݽ��лָ�

���濴Callback��

    
    
     1 private class MyCallback extends Snackbar.Callback {
     2         @Override
     3         public void onDismissed(Snackbar snackbar, int event) {
     4             super.onDismissed(snackbar, event);
     5             if (event == DISMISS_EVENT_SWIPE || event == DISMISS_EVENT_TIMEOUT || event ==
     6                     DISMISS_EVENT_CONSECUTIVE) {
     7                 mdatabase.execSQL("delete from tally where id=?", new String[]{backupMap.get
     8                         ("id").toString()});
     9             }
    10         }
    11     }

���￴��������д��onDismissed��������SnackBar��ʧ��ʱ���ص�����������������ж������ʧ�����ͣ�����ǵ����Action���Ͳ���ɾ�����ݿ��е����ݣ�����Ͷ����ݿ����ɾ����

SnackBar��ʧ�����Ͷ�Ӧ�ĳ�����

    
    
    /** Indicates that the Snackbar was dismissed via a swipe.*/
    public static final int DISMISS_EVENT_SWIPE = 0;
    /** Indicates that the Snackbar was dismissed via an action click.*/
    public static final int DISMISS_EVENT_ACTION = 1;
    /** Indicates that the Snackbar was dismissed via a timeout.*/
    public static final int DISMISS_EVENT_TIMEOUT = 2;
    /** Indicates that the Snackbar was dismissed via a call to {@link #dismiss()}.*/
    public static final int DISMISS_EVENT_MANUAL = 3;
    /** Indicates that the Snackbar was dismissed from a new Snackbar being shown.*/
    public static final int DISMISS_EVENT_CONSECUTIVE = 4;

���Կ�������0��4�ֱ��ǻ�����������Action������ʱ�����������dismiss�����Լ����µ�SnackBar����

��Ϊÿ��SnackBar��ʧ��ʱ���ǵ�����dismiss��������������ֻ��Ҫ�жϴ�ʱ��״̬�Ƿ�Ϊ�������������ʱ����������µ�SnackBar����������ǣ���֤���û�����Ҫ������������ݿ����ɾ����

������ʹ��SnackBar���ǵ������������

