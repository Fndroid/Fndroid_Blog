# Android开发学习之路-插件安装、检查应用是否安装解决方案

使用Bmob的时候，如果需要用到支付功能，就需要让应用去安装一个支付插件。而一般的做法是将插件放置在assets目录中，当用户需要支付，先检查是否能支付，不能的话，提示安装插件。代码：

    
    
     1 public class InstallHelper {
     2     private static final String TAG = "InstallHelper";
     3     private Context mContext;
     4 
     5     InstallHelper(Context context) {
     6         mContext = context;
     7     }
     8 
     9     void installAssetApk(String fileName) {
    10         try {
    11             InputStream is = this.mContext.getAssets().open(fileName);
    12             File file = new File(mContext.getExternalCacheDir()+ File.separator +
    13                     "demo.apk");
    14             if (file.exists()) {
    15                 file.delete();
    16             }
    17             file.createNewFile();
    18             FileOutputStream fos = new FileOutputStream(file);
    19             byte[] bytes = new byte[1024];
    20             int i;
    21             while ((i = is.read(bytes)) > 0) {
    22                 fos.write(bytes, 0, i);
    23             }
    24             fos.close();
    25             is.close();
    26             Intent intent = new Intent(Intent.ACTION_VIEW);
    27             intent.addFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
    28             intent.setDataAndType(Uri.parse("file://" + file), "application/vnd.android" + "" +
    29                     ".package-archive");
    30             mContext.startActivity(intent);
    31         } catch (IOException e) {
    32             e.printStackTrace();
    33         }
    34     }
    35 }

我们不能直接执行assets下的安装包，所以这里的做法是先获得Assets目录的输入流。接着创建一个文件，这个文件用来存放我们从assets目录下读出的安装包内容。上述代码中的12和13行中，我们通过getExternalCacheDir()来获取主要存放的目录，再通过File.separator来插入一个路径分隔符，最后填上文件名来作为整个文件的绝对路径。这里有一个地方要说明，先看下面两个方法：

getExternalCacheDir()：获取应用目录下的cache目录，不需要读写权限，应用删除时也会删除

getExternalStorageDirectory()：获取主外部储存的根目录，需要读写权限，应用删除不会删除

因为这个插件不需要共享给其他应用，所以我们需要使用第一个方法。如果使用了第二个，则会破坏掉用户外部储存的目录结构，毕竟无端的多出了一个文件，用户感觉当然是不好的。

接着判断文件是否存在，若存在则重新建立。再创建一个byte数组来进行数据读写操作的辅助，不断地从输入流中读入数据，写到文件中。但是这里有一个问题要注意，我们传输的是一个安装包，只要最后传输的文件和assets目录下的安装包有一点不同，那么这个安装包都是不能使用的（安装会提示解析出错）。这里要留意的地方就是22行，这个write方法不能写错。

如果我们直接使用：

    
    
    fos.write(bytes); // 相当于fos.write(bytes, 0, bytes.length)，也就是把整个bytes数组写入输出流

则这个传输就不正确了，这里先想想为什么？可以看到，我们定义了一个局部变量i来获取每次读入的大小，只要这个i的值不为-1，则循环一直进行。但是试想最后一次循环的时候，假设数据只有500个byte，那么我们直接调用fos.write(bytes)则是相当于把整一个bytes数组都写进输出流，但是实际上我们只需要前500个。

当读写完全后，我们可以通过Intent来打开这个安装包，写法就是上面那样。

* * *

另一个内容就是检查一个应用是否已经安装，例如我们在调用微信分享的时候，如果用户手机中没有微信，那么App将会没任何反应，这不会是是我们希望看到的，所以一般会先判断微信是否已经安装（其他应用类似）。

判断一个App是否已经安装，我在StackOverFlow中看到的很多方法都是直接的使用PackageManager来获取所有Activity对应的PackageInfo，代码如下：

    
    
        private boolean isWechatInstall() {
            List<PackageInfo> installedPackages = getPackageManager().getInstalledPackages
                    (PackageManager.GET_ACTIVITIES);
            for (PackageInfo p : installedPackages) {
                if (p.packageName.equals("com.tencent.mm")) {
                    return true;
                }
            }
            return false;
        }

实际上这样做是不行的。原因是系统中的应用Activity对应的Package是很多的，如果在短时间内对这些信息进行包装，则会抛出异常并终止处理（一般处理30个左右就会自动终止），因为这个异常不是RuntimeException，所以我们的App也不会Crash掉，只是结果不正确。

正确的做法应该是这样：

    
    
        private boolean isAppInstalled(String packageName) {
            PackageManager pm = getPackageManager();
            boolean installed;
            try {
                pm.getPackageInfo(packageName, PackageManager
                        .GET_ACTIVITIES);
                installed = true;
            } catch (PackageManager.NameNotFoundException e) {
                installed = false;
            }
            return installed;
        }

直接根据包名获取其PackageInfo对象，如果不存在，则在抛出的异常中返回false即可。

