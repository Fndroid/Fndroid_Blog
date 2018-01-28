# Android����ѧϰ֮·-����Exception

Exception��Java���Ǳ�ʾ�쳣��һ���ࡣ����Throwable�����ࡣ

��Exception������RuntimeException��һ��������쳣�࣬�ڴ����в���Ҫ�Դ������throw��������JVM���׳���

�ܶ�ʱ�����ǵ�ĳЩ���⣬����ͨ��һ��try/catch�򵥵Ľ����������ǰ����Ҫ�˽�Exception��



�ܶ��˶���Exception����⣬����������IDE����ʾ����Ȼ��ȫһ��try/catch�������ˣ����ῼ�ǵ�Ϊʲô���������Լ���д�Ŀ⽻�����ˣ�����Ҳ��Ը��ʹ�á�

����Ҫ���׵��ǣ�����RuntimeException���������Exception������Ҫͨ��throw�׳��ġ���������õķ�������ͨ��throw�׳��쳣������Ҫ�ڵ��õķ�����ʹ��try/catch�����в��������ͨ���ڷ���βʹ��throws���쳣�����׳���

�ⶼ��������ġ�



����Exception��Ҫ�������㣺

  1. �쳣���񣬿��Խ���������ж�
  2. ֻҪ��Exception���Ϳ��Ա�try/catch����õ�������RuntimeException



����˵����һ�㣬ʲô�������жϣ�����˵������if/else����жϲ��ˡ�

�ٸ����ӣ�FingerprintManager����࣬����API23�����룬���������ֻ�ָ��ģ��ģ���ô��API22��һ�µ�ƽ̨���ǲ����������ġ����ǣ�����ĳЩ���̣���ָ��ģ�����뵽API22���µ�ƽ̨�У�������ֲ�˹ٷ�����Щ�࣬Ҳ����˵��ĳЩ����Rom����MIUI����API22�������ǿ��ܴ���FingerprintManager�����ģ������ǿ��Բ���ָ��ģ��ġ������͵����ˣ�������Ҫ�ж�Rom���Ƿ����������ˡ���ô�жϣ���Ȼ������if/else��䡣

�����ܼ򵥣�ֱ�ӽ��з��䣬������ʱ���û������࣬���׳�ClassNotFoundException���������£�

    
    
    1         try {
    2             Class.forName("android.hardware.fingerprint.FingerprintManager");
    3         } catch (ClassNotFoundException e) {
    4             e.printStackTrace();
    5             Log.d(TAG, "doSomething: û�и�api");
    6         }



�����ǵڶ��㣬RuntimeException�ǿ��Ա�����ġ�

��ΪRuntimeException������throw���׳�������IDE������������ʹ��try/catch����������쳣����ʵ���ϣ����ǿ����Լ�������ӡ�

���ӣ���Android�����У�����Ҫ��ĳ��View���ñ�����ɫ�����ǿ��Ե���setBackgroundColor�������������ã������������һ��int��Ϊ������ʵ�������int�������һ����ɫ��ARGBֵ�����ɫ0xFFFFFFFF��������Ҫ��дһ������������һ��View�ı�����ɫ������һ��������ʾ��ɫ��ֵ��������ɫ��id��R.color.xxx)����������

    
    
    private void setViewBackground(View view, int color)

Ҫ��ôд��

���ȣ�����ͨ�����أ���Ϊ��ɫ��ֵ��id����һ��int������������ͬ���Ͳ������أ�������һ���������Եö��ࡣ

���ʱ�򣬾Ϳ���ͨ���쳣�Ĳ�����������

    
    
    1     private void setViewBackground(View view, int color) {
    2         try {
    3             view.setBackgroundColor(ContextCompat.getColor(this, color));
    4         } catch (Resources.NotFoundException e) {
    5             e.printStackTrace();
    6             view.setBackgroundColor(color);
    7         }
    8     }

���Կ���������ֱ�ӽ����int��Ϊһ��id��ʹ�ã�ͨ��ContextCompat��getColor����ȡid��Ӧ����ɫֵ�����ʱ�򣬼��贫���ֱ�Ӿ���һ����ɫ����ô����Դ�ļ�R�У����Ҳ������id�ģ��������׳�һ��RuntimeException���������������Resources.NotFoundException����������쳣�������������������id��ֱ�����ø�View���ɡ�

��Ȼ���������Ҳ���Ƕ���ȷ����Ϊ�п��ܴ������ɫֵ�պ���ĳ����ɫ��idһ������ô�ͻ���ֵò�����Ҫ��ɫ�����⡣������ӣ�ֻ��˵��RuntimeException���Ա����Ǵ�����ʹIDE����ʾ��



