# Android����ѧϰ֮·-�����װ�����Ӧ���Ƿ�װ�������

ʹ��Bmob��ʱ�������Ҫ�õ�֧�����ܣ�����Ҫ��Ӧ��ȥ��װһ��֧���������һ��������ǽ����������assetsĿ¼�У����û���Ҫ֧�����ȼ���Ƿ���֧�������ܵĻ�����ʾ��װ��������룺

    
    
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

���ǲ���ֱ��ִ��assets�µİ�װ��������������������Ȼ��AssetsĿ¼�������������Ŵ���һ���ļ�������ļ�����������Ǵ�assetsĿ¼�¶����İ�װ�����ݡ����������е�12��13���У�����ͨ��getExternalCacheDir()����ȡ��Ҫ��ŵ�Ŀ¼����ͨ��File.separator������һ��·���ָ�������������ļ�������Ϊ�����ļ��ľ���·����������һ���ط�Ҫ˵�����ȿ���������������

getExternalCacheDir()����ȡӦ��Ŀ¼�µ�cacheĿ¼������Ҫ��дȨ�ޣ�Ӧ��ɾ��ʱҲ��ɾ��

getExternalStorageDirectory()����ȡ���ⲿ����ĸ�Ŀ¼����Ҫ��дȨ�ޣ�Ӧ��ɾ������ɾ��

��Ϊ����������Ҫ���������Ӧ�ã�����������Ҫʹ�õ�һ�����������ʹ���˵ڶ���������ƻ����û��ⲿ�����Ŀ¼�ṹ���Ͼ��޶˵Ķ����һ���ļ����û��о���Ȼ�ǲ��õġ�

�����ж��ļ��Ƿ���ڣ������������½������ٴ���һ��byte�������������ݶ�д�����ĸ��������ϵش��������ж������ݣ�д���ļ��С�����������һ������Ҫע�⣬���Ǵ������һ����װ����ֻҪ�������ļ���assetsĿ¼�µİ�װ����һ�㲻ͬ����ô�����װ�����ǲ���ʹ�õģ���װ����ʾ��������������Ҫ����ĵط�����22�У����write��������д��

�������ֱ��ʹ�ã�

    
    
    fos.write(bytes); // �൱��fos.write(bytes, 0, bytes.length)��Ҳ���ǰ�����bytes����д�������

���������Ͳ���ȷ�ˣ�����������Ϊʲô�����Կ��������Ƕ�����һ���ֲ�����i����ȡÿ�ζ���Ĵ�С��ֻҪ���i��ֵ��Ϊ-1����ѭ��һֱ���С������������һ��ѭ����ʱ�򣬼�������ֻ��500��byte����ô����ֱ�ӵ���fos.write(bytes)�����൱�ڰ���һ��bytes���鶼д�������������ʵ��������ֻ��Ҫǰ500����

����д��ȫ�����ǿ���ͨ��Intent���������װ����д����������������

* * *

��һ�����ݾ��Ǽ��һ��Ӧ���Ƿ��Ѿ���װ�����������ڵ���΢�ŷ����ʱ������û��ֻ���û��΢�ţ���ôApp����û�κη�Ӧ���ⲻ����������ϣ�������ģ�����һ������ж�΢���Ƿ��Ѿ���װ������Ӧ�����ƣ���

�ж�һ��App�Ƿ��Ѿ���װ������StackOverFlow�п����ĺܶ෽������ֱ�ӵ�ʹ��PackageManager����ȡ����Activity��Ӧ��PackageInfo���������£�

    
    
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

ʵ�����������ǲ��еġ�ԭ����ϵͳ�е�Ӧ��Activity��Ӧ��Package�Ǻܶ�ģ�����ڶ�ʱ���ڶ���Щ��Ϣ���а�װ������׳��쳣����ֹ����һ�㴦��30�����Ҿͻ��Զ���ֹ������Ϊ����쳣����RuntimeException���������ǵ�AppҲ����Crash����ֻ�ǽ������ȷ��

��ȷ������Ӧ����������

    
    
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

ֱ�Ӹ��ݰ�����ȡ��PackageInfo������������ڣ������׳����쳣�з���false���ɡ�

