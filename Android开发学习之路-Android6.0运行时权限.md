# Android����ѧϰ֮·-Android6.0����ʱȨ��

��Android6.0�Ժ�ʼ�����ڲ������е�"Σ��"Ȩ�ޣ���Ҫ��Ӧ������ʱ���û����룬ֻ���û��������������Ȩ�޲Żᱻ�����Ӧ�á�������û���˵��������һ��������ȫ�Ե���������ô���ڿ����ߣ�Ӧ����ô���أ�



Android6.0�涨��Σ��Ȩ����������Щ��

Permission Group| Permissions  
---|---  
`[CALENDAR](https://developer.android.com/reference/android/Manifest.permission_group.html#CALENDAR)`
|

  * `[READ_CALENDAR](https://developer.android.com/reference/android/Manifest.permission.html#READ_CALENDAR)`

  * `[WRITE_CALENDAR](https://developer.android.com/reference/android/Manifest.permission.html#WRITE_CALENDAR)`

  
`[CAMERA](https://developer.android.com/reference/android/Manifest.permission_group.html#CAMERA)`
|

  * `[CAMERA](https://developer.android.com/reference/android/Manifest.permission.html#CAMERA)`

  
`[CONTACTS](https://developer.android.com/reference/android/Manifest.permission_group.html#CONTACTS)`
|

  * `[READ_CONTACTS](https://developer.android.com/reference/android/Manifest.permission.html#READ_CONTACTS)`
  * `[WRITE_CONTACTS](https://developer.android.com/reference/android/Manifest.permission.html#WRITE_CONTACTS)`
  * `[GET_ACCOUNTS](https://developer.android.com/reference/android/Manifest.permission.html#GET_ACCOUNTS)`

  
`[LOCATION](https://developer.android.com/reference/android/Manifest.permission_group.html#LOCATION)`
|

  * `[ACCESS_FINE_LOCATION](https://developer.android.com/reference/android/Manifest.permission.html#ACCESS_FINE_LOCATION)`
  * `[ACCESS_COARSE_LOCATION](https://developer.android.com/reference/android/Manifest.permission.html#ACCESS_COARSE_LOCATION)`

  
`[MICROPHONE](https://developer.android.com/reference/android/Manifest.permission_group.html#MICROPHONE)`
|

  * `[RECORD_AUDIO](https://developer.android.com/reference/android/Manifest.permission.html#RECORD_AUDIO)`

  
`[PHONE](https://developer.android.com/reference/android/Manifest.permission_group.html#PHONE)`
|

  * `[READ_PHONE_STATE](https://developer.android.com/reference/android/Manifest.permission.html#READ_PHONE_STATE)`
  * `[CALL_PHONE](https://developer.android.com/reference/android/Manifest.permission.html#CALL_PHONE)`
  * `[READ_CALL_LOG](https://developer.android.com/reference/android/Manifest.permission.html#READ_CALL_LOG)`
  * `[WRITE_CALL_LOG](https://developer.android.com/reference/android/Manifest.permission.html#WRITE_CALL_LOG)`
  * `[ADD_VOICEMAIL](https://developer.android.com/reference/android/Manifest.permission.html#ADD_VOICEMAIL)`
  * `[USE_SIP](https://developer.android.com/reference/android/Manifest.permission.html#USE_SIP)`
  * `[PROCESS_OUTGOING_CALLS](https://developer.android.com/reference/android/Manifest.permission.html#PROCESS_OUTGOING_CALLS)`

  
`[SENSORS](https://developer.android.com/reference/android/Manifest.permission_group.html#SENSORS)`
|

  * `[BODY_SENSORS](https://developer.android.com/reference/android/Manifest.permission.html#BODY_SENSORS)`

  
`[SMS](https://developer.android.com/reference/android/Manifest.permission_group.html#SMS)`
|

  * `[SEND_SMS](https://developer.android.com/reference/android/Manifest.permission.html#SEND_SMS)`
  * `[RECEIVE_SMS](https://developer.android.com/reference/android/Manifest.permission.html#RECEIVE_SMS)`
  * `[READ_SMS](https://developer.android.com/reference/android/Manifest.permission.html#READ_SMS)`
  * `[RECEIVE_WAP_PUSH](https://developer.android.com/reference/android/Manifest.permission.html#RECEIVE_WAP_PUSH)`
  * `[RECEIVE_MMS](https://developer.android.com/reference/android/Manifest.permission.html#RECEIVE_MMS)`

  
`[STORAGE](https://developer.android.com/reference/android/Manifest.permission_group.html#STORAGE)`
|

  * `[READ_EXTERNAL_STORAGE](https://developer.android.com/reference/android/Manifest.permission.html#READ_EXTERNAL_STORAGE)`
  * `[WRITE_EXTERNAL_STORAGE](https://developer.android.com/reference/android/Manifest.permission.html#WRITE_EXTERNAL_STORAGE)`

  
  


��һ���������ְɡ�������App����Ҫ���в���绰�Ĳ������������ť��ֱ�Ӳ���һ���绰���������£�

    
    
    Intent intent = new Intent(Intent.ACTION_CALL, Uri.parse("tel:10086"));
    startActivity(intent);

���������ı���汾��23�������ϣ���ôAndroid Studio�ͻᱨ��һ������

![](http://images2015.cnblogs.com/blog/852227/201605/852227-20160530130306961-900393589.png)

��ʾ����Ҫ����Ȩ������

�������ֱ�Ӷ������������Զ��޸�������startActivity����֮ǰ����һ�δ�������Ȩ�޼�飬�����ǰû�и����Ȩ�ޣ���ֱ��return����������

    
    
    if (ActivityCompat.checkSelfPermission(this, Manifest.permission.CALL_PHONE) != PackageManager.PERMISSION_GRANTED) {
          // TODO: Consider calling
          //    ActivityCompat#requestPermissions
          // here to request the missing permissions, and then overriding
          //   public void onRequestPermissionsResult(int requestCode, String[] permissions,
          //                                          int[] grantResults)
          // to handle the case where the user grants the permission. See the documentation
          // for ActivityCompat#requestPermissions for more details.
          return;
    }

������ʱ��ֱ�����У����APP������û�еõ����Ȩ�޵ģ���ΪĬ�ϰ�װ��ʱ�����Ȩ�޻ᱻ�رյ������Է���ֱ�Ӿͷ����ˡ�Ҫ����Ҫ�ڵ����ť��ʱ��ֱ�����û�����Ȩ�ޣ�����ʹ������ķ�����

    
    
    ActivityCompat.requestPermissions(this, new String[]{Manifest.permission.CALL_PHONE}, REQUESTCODE);

���ʱ������������App����õ������һ�������������û�Ȩ�ޣ�

![](http://images2015.cnblogs.com/blog/852227/201605/852227-20160530131253602-588528380.png)

**����** ���Ժ��ٵ��������ֱ������Ӧ�û�ø�Ȩ��

**�ܾ�** ���´���Ҫʱ�����µ�����ֱ���û�ѡ����������᲻�ϳ���



��ô��������ˣ�����û���ѡ�� **����ѯ��** �� **�ܾ�** ������������أ�
**�����Ժ󲻻��ٵ���������Ĭ�Ͼܾ�Ȩ������Ҳ����˵��Ӧ����Ҳ���ܻ�����Ȩ�ޣ������û���������Ӧ��������������Ȩ��**



**Ϊ�˸��õ��û����飬�������û�ѡ���˲���ѯ�ʺ;ܾ�֮�󣬴���һ��AlertDialog��ѯ���û��Ƿ���Ҫ��������Ȩ�ޣ��ǵĻ�����ת��Ӧ�����ý��档**

�����ǵ���requestPermissions������ʱ�����۳ɹ���񣬶��Ὣ����ص���Activity�е�onRequestPermissionsResult�����У����ǿ���ͨ����д�����������������

    
    
    @Override
        public void onRequestPermissionsResult(int requestCode, @NonNull String[] permissions,
                                               @NonNull int[] grantResults) {
            super.onRequestPermissionsResult(requestCode, permissions, grantResults);
            if (requestCode == REQUESTCODE) {
                if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.M) {
                    if (!shouldShowRequestPermissionRationale(Manifest.permission.CALL_PHONE)) {
                        AskForPermission();
                    }
                }
            }
        }

���ж������Ƿ�Ϊ����������ģ������ж�SDK�İ汾�Ƿ���ڻ����AndroidM(6.0)������ǣ������shouldShowRequestPermissionRationale���������Ȩ��������Ƿ����ʾ����Ϊ
**����û�ѡ���˲���ѯ�ʣ��������᲻����** ����������᷵��false����������û�г��֣��򴴽�һ��AlertDialog��ѯ���û���

    
    
    private void AskForPermission() {
            AlertDialog.Builder builder = new AlertDialog.Builder(this);
            builder.setTitle("Need Permission!");
            builder.setNegativeButton("Cancel", new DialogInterface.OnClickListener() {
                @Override
                public void onClick(DialogInterface dialog, int which) {
    
                }
            });
            builder.setPositiveButton("Settings", new DialogInterface.OnClickListener() {
                @Override
                public void onClick(DialogInterface dialog, int which) {
                    Intent intent = new Intent(Settings.ACTION_APPLICATION_DETAILS_SETTINGS);
                    intent.setData(Uri.parse("package:" + getPackageName())); // ���ݰ����򿪶�Ӧ�����ý���
                    startActivity(intent);
                }
            });
            builder.create().show();
        }

![](http://images2015.cnblogs.com/blog/852227/201605/852227-20160530150844524-1330842443.gif)



