# Android开发学习之路-让注解帮你简化代码，彻底抛弃findViewById

本文主要是记录注解的使用的学习笔记，如有错误请提出。

在通常的情况下，我们在Activity中有一个View，我们要获得这个View的实例是要通过findViewById这个方法，然后这个方法返回的是一个Object类型，我们还需要进行强制的类型转换，但是相信很多人都遇到过，当我们的一个布局中有很多个控件的时候，每一个控件都要进行上面的这个操作其实是很烦躁的，特别是强制类型转换，即使是用Alt+Enter，多按几次都累了。而今天要用的是通过注解的方式来简化这一个复杂的步骤，在我们编写好相应的代码之后，获取实例的方法就变成像下面这样简单了

    
    
    @ViewInject(R.id.buy)
    private Button buy;

通过上面这两行简单的代码，就能把id所对应的View实例化了，下面我们一步一步的来学习如何使用注解。

**先说原理，我们都要知道，在Java中，通过反射，我们可以知道每一个类的详细信息，比如有什么字段，有什么方法，类名等等，我们通过注解和反射配合，使用反射调用类中的方法，然后读取注解的参数来进行方法的执行。简单的说，就是其实我们还是会调用findViewById这个方法，但是，这个方法可以放到工具类中执行了，我们只需要像上面那样给出参数就行了。**

先说下反射，反射就是允许我们动态的操作一个类，获取类的字段，执行类的方法，获取类的名字等等，在这里我们一般会用到下面的这几个方法：

getMethod：获取类中的public方法

getDeclaredMethod：获取类中的所有方法

getField：获取类中的public字段（属性）

getDeclaredField：获取类中的所有字段

还有其他的请自行参考Class类的源码



再说注解，我们看到的最多的注解获取是@Override，当我们重写父类中的方法的时候，这个注解会被编译器自动生成出来，这个注解只是表明方法是重写了父类方法。

那么我们究竟该如何自定义我们想要的注解呢？其实很简单，直接看代码：

    
    
    @Target(ElementType.FIELD)
    @Retention(RetentionPolicy.RUNTIME)
    public @interface  ViewInject{
        int value();
    }

@Target的意思是我们注解作用的目标，这里是ElementType.FIELD，也就是作用于字段的

ElementType的类型有以下几种：

   1.CONSTRUCTOR:用于描述构造器  
2.FIELD:用于描述字段  
3.LOCAL_VARIABLE:用于描述局部变量  
4.METHOD:用于描述方法  
5.PACKAGE:用于描述包  
6.PARAMETER:用于描述参数  
7.TYPE:用于描述类、接口(包括注解类型) 或enum声明

@Retention的意思是注解的运行级别

  RetentionPolicy的类型有以下几种

   1.SOURCE:在源文件中有效（即源文件保留）  
2.CLASS:在class文件中有效（即class保留）  
3.RUNTIME:在运行时有效（即运行时保留）

@interface则是表明这个类是一个注解，@号不能漏掉，否则变成了接口了

如果想对注解了解得更多，可以参考：http://www.cnblogs.com/gmq-sh/p/4798194.html
这篇博文，很详细，对于我们想要的功能，以上的内容就足够了

定义好了注解，如果我们直接来使用，是没有任何效果的，因为注解只是一段代码，并没有关联上我们的控件，所以我们要编写一个工具类来做关联

    
    
    public class AnnotateUtils {
        public static void injectViews(Activity activity) {
            Class<? extends Activity> object = activity.getClass(); // 获取activity的Class
            Field[] fields = object.getDeclaredFields(); // 通过Class获取activity的所有字段
            for (Field field : fields) { // 遍历所有字段
                // 获取字段的注解，如果没有ViewInject注解，则返回null
                ViewInject viewInject = field.getAnnotation(ViewInject.class); 
                if (viewInject != null) {
                    int viewId = viewInject.value(); // 获取字段注解的参数，这就是我们传进去控件Id
                    if (viewId != -1) {
                        try {
                            // 获取类中的findViewById方法，参数为int
                            Method method = object.getMethod("findViewById", int.class);
                            // 执行该方法，返回一个Object类型的View实例
                            Object resView = method.invoke(activity, viewId);
                            field.setAccessible(true);
                            // 把字段的值设置为该View的实例
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

在工具类中，我们获取到了activity的Class，接着获得类中的所有字段，遍历字段，如果有ViewInject注解，则获取注解的中的值，通过获取并执行类中的方法（findViewById）来获得对应View的实例，最后把实例赋值给当前的字段，整个过程就完成了。

当我们需要使用注解的时候，我们只需要在定义View的时候，在View的字段上添加对应的注解，把Id传入，然后在onCreate方法中调用上面这个工具类的方法即可。

    
    
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



最后，有几点说明：

①如果注解中的值不是value，那么在进行注解是时候，需要给出对应的值的名字，假如我们在注解中做了如下定义：

    
    
    @Target(ElementType.FIELD)
    @Retention(RetentionPolicy.RUNTIME)
    public @interface  ViewInject{
        int id();
    }

那么在注解的时候，需要这样：

    
    
    @ViewInject(id = R.id.buy)
    private Button buy;

因为value这个名字是默认的，如果我们定义为value，那么注解的时候可以省略

②注解还可以帮我们注入布局，设置监听等，如果有兴趣，可以继续探讨下去

③使用注解的时候，句末不能加";"



