# Android����ѧϰ֮·-��ע�����򻯴��룬��������findViewById

������Ҫ�Ǽ�¼ע���ʹ�õ�ѧϰ�ʼǣ����д����������

��ͨ��������£�������Activity����һ��View������Ҫ������View��ʵ����Ҫͨ��findViewById���������Ȼ������������ص���һ��Object���ͣ����ǻ���Ҫ����ǿ�Ƶ�����ת�����������źܶ��˶��������������ǵ�һ���������кܶ���ؼ���ʱ��ÿһ���ؼ���Ҫ������������������ʵ�Ǻܷ���ģ��ر���ǿ������ת������ʹ����Alt+Enter���ఴ���ζ����ˡ�������Ҫ�õ���ͨ��ע��ķ�ʽ������һ�����ӵĲ��裬�����Ǳ�д����Ӧ�Ĵ���֮�󣬻�ȡʵ���ķ����ͱ����������������

    
    
    @ViewInject(R.id.buy)
    private Button buy;

ͨ�����������м򵥵Ĵ��룬���ܰ�id����Ӧ��Viewʵ�����ˣ���������һ��һ������ѧϰ���ʹ��ע�⡣

**��˵ԭ�����Ƕ�Ҫ֪������Java�У�ͨ�����䣬���ǿ���֪��ÿһ�������ϸ��Ϣ��������ʲô�ֶΣ���ʲô�����������ȵȣ�����ͨ��ע��ͷ�����ϣ�ʹ�÷���������еķ�����Ȼ���ȡע��Ĳ��������з�����ִ�С��򵥵�˵��������ʵ���ǻ��ǻ����findViewById������������ǣ�����������Էŵ���������ִ���ˣ�����ֻ��Ҫ�����������������������ˡ�**

��˵�·��䣬��������������Ƕ�̬�Ĳ���һ���࣬��ȡ����ֶΣ�ִ����ķ�������ȡ������ֵȵȣ�����������һ����õ�������⼸��������

getMethod����ȡ���е�public����

getDeclaredMethod����ȡ���е����з���

getField����ȡ���е�public�ֶΣ����ԣ�

getDeclaredField����ȡ���е������ֶ�

���������������вο�Class���Դ��



��˵ע�⣬���ǿ���������ע���ȡ��@Override����������д�����еķ�����ʱ�����ע��ᱻ�������Զ����ɳ��������ע��ֻ�Ǳ�����������д�˸��෽����

��ô���Ǿ���������Զ���������Ҫ��ע���أ���ʵ�ܼ򵥣�ֱ�ӿ����룺

    
    
    @Target(ElementType.FIELD)
    @Retention(RetentionPolicy.RUNTIME)
    public @interface  ViewInject{
        int value();
    }

@Target����˼������ע�����õ�Ŀ�꣬������ElementType.FIELD��Ҳ�����������ֶε�

ElementType�����������¼��֣�

   1.CONSTRUCTOR:��������������  
2.FIELD:���������ֶ�  
3.LOCAL_VARIABLE:���������ֲ�����  
4.METHOD:������������  
5.PACKAGE:����������  
6.PARAMETER:������������  
7.TYPE:���������ࡢ�ӿ�(����ע������) ��enum����

@Retention����˼��ע������м���

  RetentionPolicy�����������¼���

   1.SOURCE:��Դ�ļ�����Ч����Դ�ļ�������  
2.CLASS:��class�ļ�����Ч����class������  
3.RUNTIME:������ʱ��Ч��������ʱ������

@interface���Ǳ����������һ��ע�⣬@�Ų���©�����������˽ӿ���

������ע���˽�ø��࣬���Բο���http://www.cnblogs.com/gmq-sh/p/4798194.html
��ƪ���ģ�����ϸ������������Ҫ�Ĺ��ܣ����ϵ����ݾ��㹻��

�������ע�⣬�������ֱ����ʹ�ã���û���κ�Ч���ģ���Ϊע��ֻ��һ�δ��룬��û�й��������ǵĿؼ�����������Ҫ��дһ����������������

    
    
    public class AnnotateUtils {
        public static void injectViews(Activity activity) {
            Class<? extends Activity> object = activity.getClass(); // ��ȡactivity��Class
            Field[] fields = object.getDeclaredFields(); // ͨ��Class��ȡactivity�������ֶ�
            for (Field field : fields) { // ���������ֶ�
                // ��ȡ�ֶε�ע�⣬���û��ViewInjectע�⣬�򷵻�null
                ViewInject viewInject = field.getAnnotation(ViewInject.class); 
                if (viewInject != null) {
                    int viewId = viewInject.value(); // ��ȡ�ֶ�ע��Ĳ�������������Ǵ���ȥ�ؼ�Id
                    if (viewId != -1) {
                        try {
                            // ��ȡ���е�findViewById����������Ϊint
                            Method method = object.getMethod("findViewById", int.class);
                            // ִ�и÷���������һ��Object���͵�Viewʵ��
                            Object resView = method.invoke(activity, viewId);
                            field.setAccessible(true);
                            // ���ֶε�ֵ����Ϊ��View��ʵ��
                            field.set(activity, resView);
                        } catch (NoSuchMethodException e) {
                            e.printStackTrace();
                        } catch (IllegalAccessException e) {
                            e.printStackTrace();
                        } catch (InvocationTargetException e) {
                            e.printStackTrace();
                        }
                    }
                }
            }
        }
    }

�ڹ������У����ǻ�ȡ����activity��Class�����Ż�����е������ֶΣ������ֶΣ������ViewInjectע�⣬���ȡע����е�ֵ��ͨ����ȡ��ִ�����еķ�����findViewById������ö�ӦView��ʵ��������ʵ����ֵ����ǰ���ֶΣ��������̾�����ˡ�

��������Ҫʹ��ע���ʱ������ֻ��Ҫ�ڶ���View��ʱ����View���ֶ�����Ӷ�Ӧ��ע�⣬��Id���룬Ȼ����onCreate�����е����������������ķ������ɡ�

    
    
    @ViewInject(R.id.buy)
    private Button buy;
    @ViewInject(R.id.money)
    private TextView money;
    @ViewInject(R.id.tv_power)
    private TextView power;
    @ViewInject(R.id.tv_life)
    private TextView life;
    @ViewInject(R.id.tv_dex)
    private TextView dex;
    
    
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        AnnotateUtils.injectViews(this);
    }



����м���˵����

�����ע���е�ֵ����value����ô�ڽ���ע����ʱ����Ҫ������Ӧ��ֵ�����֣�����������ע�����������¶��壺

    
    
    @Target(ElementType.FIELD)
    @Retention(RetentionPolicy.RUNTIME)
    public @interface  ViewInject{
        int id();
    }

��ô��ע���ʱ����Ҫ������

    
    
    @ViewInject(id = R.id.buy)
    private Button buy;

��Ϊvalue���������Ĭ�ϵģ�������Ƕ���Ϊvalue����ôע���ʱ�����ʡ��

��ע�⻹���԰�����ע�벼�֣����ü����ȣ��������Ȥ�����Լ���̽����ȥ

��ʹ��ע���ʱ�򣬾�ĩ���ܼ�";"



