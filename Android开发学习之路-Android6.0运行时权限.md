# Android开发学习之路-Android6.0运行时权限

在Android6.0以后开始，对于部分敏感的"危险"权限，需要在应用运行时向用户申请，只有用户允许的情况下这个权限才会被授予给应用。这对于用户来说，无疑是一个提升安全性的做法。那么对于开发者，应该怎么做呢？



Android6.0规定的危险权限有下面这些：

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

  
  


从一个例子入手吧。假如在App中需要进行拨打电话的操作，当点击按钮，直接拨出一个电话，代码如下：

    
    
    Intent intent = new Intent(Intent.ACTION_CALL, Uri.parse("tel:10086"));
    startActivity(intent);

但是如果你的编译版本是23或者以上，那么Android Studio就会报出一个错误

![](http://images2015.cnblogs.com/blog/852227/201605/852227-20160530130306961-900393589.png)

提示我们要进行权限请求。

如果我们直接对这个错误进行自动修复，会在startActivity方法之前增加一段代码用于权限检查，如果当前没有赋予该权限，则直接return结束方法：

    
    
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

如果这个时候直接运行，你的APP照样是没有得到这个权限的，因为默认安装的时候，这个权限会被关闭到，所以方法直接就返回了。要是需要在点击按钮的时候，直接向用户请求权限，可以使用下面的方法：

    
    
    ActivityCompat.requestPermissions(this, new String[]{Manifest.permission.CALL_PHONE}, REQUESTCODE);

这个时候，再重新运行App，会得到下面的一个界面来请求用户权限：

![](http://images2015.cnblogs.com/blog/852227/201605/852227-20160530131253602-588528380.png)

**允许** ：以后不再弹出请求框，直接允许应用获得该权限

**拒绝** ：下次需要时会重新弹出，直到用户选择允许，否则会不断出现



那么问题就来了，如果用户勾选了 **不再询问** 并 **拒绝** 了请求会怎样呢？
**答案是以后不会再弹出，并且默认拒绝权限请求，也就是说，应用再也不能获得这个权限，除非用户主动进入应用设置中重新授权。**



**为了更好的用户体验，可以在用户选择了不再询问和拒绝之后，创建一个AlertDialog来询问用户是否需要重新授予权限，是的话，跳转到应用设置界面。**

当我们调用requestPermissions方法的时候，无论成功与否，都会将结果回调给Activity中的onRequestPermissionsResult方法中，我们可以通过重写这个方法来处理结果：

    
    
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

先判断请求是否为我们所申请的，接着判断SDK的版本是否高于或等于AndroidM(6.0)，如果是，则调用shouldShowRequestPermissionRationale方法来检查权限申请框是否会显示，因为
**如果用户选择了不再询问，则申请框会不出现** ，这个方法会返回false，如果申请框没有出现，则创建一个AlertDialog来询问用户：

    
    
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
                    intent.setData(Uri.parse("package:" + getPackageName())); // 根据包名打开对应的设置界面
                    startActivity(intent);
                }
            });
            builder.create().show();
        }

![](http://images2015.cnblogs.com/blog/852227/201605/852227-20160530150844524-1330842443.gif)



