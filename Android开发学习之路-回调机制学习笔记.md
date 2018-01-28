# Android����ѧϰ֮·-�ص�����ѧϰ�ʼ�

��֪������ѧJava��ʱ��û��������������ô�ģ�����һֱ��֪��ʲô��"�ص�"������ʲô�ã��ٶ�һ��ѣ���̫���ӿ������ף��ð����ұ�������������Լ����ķ���������������ˣ���Ҷ�������ʶһ�������Ҫ�Ļ��ơ�

�����Ҫ����Android��������ô�ص��϶��ǻᾭ���õ��ģ�Ϊʲô������������˵����ţ��ʵ����ط��Ѿ������뿪��......

�ȴӼ򵥵Ŀ�ʼ��ʲô�ǻص���

**Java�������ǵ��ýӿڵķ�����������һ��ǰ�ᣬ���Ǳ����ʱ��ӿڵĶ��������һ��������࣬���������ʵ���˽ӿڡ������ǵ��ýӿڵķ�����ʱ�򣬽ӿڵľ��������еľ��巽���ᱻ���ã�����ǻص���**
�ţ���˵ʲô�������ϴ���ɡ�

    
    
    public interface A {
        void printClassName();
    }
    
    
    public class B implements A {
        public void printClassName() {
            System.out.println("This is class B!");
        }
    }
    
    
    public static void main(String[] args) {
            A a = new B();
            a.printClassName();
    }

����Ľ�����£�

![](http://images2015.cnblogs.com/blog/852227/201603/852227-20160322211945370-1331081274.png)

������Ĵ�����Կ������������������ж����˽ӿ�A��Ҳ�����˽ӿ�A�ķ���������Ҫע�⣬����ʵ������ʱ����ʵnew����һ��ʵ����A�ӿڵľ����࣬���ǵ���A�е�printClassName������ʱ��Java�ͻ�ȥ�ص�B�еĸ÷��������������ŵ���ʲô�����������ڵ��õ�ʱ�򣬿�����ȫ����A�����������ʵ��A�еķ����ģ�ֻ��Ҫ��������������ɣ���ôʵ�֣����ǲ��ܣ���������������ˣ�������ʵ�ǽ����һ�ֺܺõķ������������Ҫ�޸�Ϊ����ֻ࣬��Ҫ��new���������ĵ��ͺá�

�ǲ��Ǻܼ򵥣���������㶼������ô��ϲ�㣬���Ѿ�����ɹ�ֻ��һ��֮ң�ˡ�

˵��Androd����ʵ�������кܶ�ط��õ�������Ƶģ�����Ҳ�ǴӼ򵥵�˵��

�ܶ�ʱ�����Ƕ�ĳ���ؼ�ʵ�ֵ���¼��ļ���������������������

    
    
    Button btn = new Button(this);
    btn.setOnClickListener(new View.OnClickListener() {
        @Override
        public void onClick(View v) {
            // doSth
        }
    });

�ܶ��˶����ã�������ֱ�����˰���������������㣬Ϊʲô����Ҫnewһ��OnClickListener��ȥ�أ�����㲻֪����֤���㻹����ȫ�˽�ص�����������������˵�ģ����ǿ��Ե��ýӿڵķ��������ǽӿ���Ҫһ�������ʵ�֣���������Ӧ��֪����setOnClickListener�еĲ���Ӧ����һ���ӿڣ����ţ�����һ�𿴿�Դ��

    
    
    public interface OnClickListener {
            /**
             * Called when a view has been clicked.
             *
             * @param v The view that was clicked.
             */
            void onClick(View v);
    }

��Ȼ���ӿ��л���һ��onClick��������Ȼ�ǻص��������ٿ���������������ﱻ������

�ȿ�setOnClickListener����

    
    
    public void setOnClickListener(@Nullable OnClickListener l) {
            if (!isClickable()) {
                setClickable(true);
            }
            getListenerInfo().mOnClickListener = l;
    }

�ؼ��ǰ�OnClickListener�Ķ���ֵ����ListenerInfo��mOnClickListener�������ٿ���ȥ

    
    
    public boolean performClick() {
            final boolean result;
            final ListenerInfo li = mListenerInfo;
            if (li != null && li.mOnClickListener != null) {
                playSoundEffect(SoundEffectConstants.CLICK);
                li.mOnClickListener.onClick(this);
                result = true;
            } else {
                result = false;
            }
    
            sendAccessibilityEvent(AccessibilityEvent.TYPE_VIEW_CLICKED);
            return result;
        }

���ǿ������������performClick�ķ����е�����onClick��������������һ������callOnClick�ķ���Ҳ�����onClick���������ȥ����Դ���˵�������ﲻ��ϸ����ô��������������ʲôʱ��ִ�е��أ���ʵ���Ƕ�������Ļ���յ�����źŵ�ʱ�򣬰�����һ��һ������ϴ��ݸ�framework����framework�����á������˽�ص����Ҿ��õ�������㹻�ˣ������¾��е�ƫ�ˡ�

Ҫ���׵�һ����ǣ���������У����õ��ǽӿڵķ�����������Ӧ�������˰ɣ�Ϊʲô����Ҫnewһ��OnClickListener�������أ������ñ�����֪�������ýӿڷ�����ʱ��ȥ�ص��������õ�OnClickListener����Ķ�Ӧ������

����������㶼���ˣ��ܺã�������˵��һЩ������һЩ��

��������֪�����������֮������ʲô���أ�һ����˵���õñȽ϶࣬����ͨ���Լ�MVPģʽ

ͨ�ţ���ʵ���ǲ�ͬ���֮���ͨ�ţ�������Fragment��Activity��Ҳ������Adapter��Activity�����ﲻ��ϸ˵̫�࣬���Կ���֮ǰ�Ĳ��ģ�http://www.cnblogs.com/Fndroid/p/5187444.html

��������˵����MVP�����ģʽ��ĺ���Ҫ�����ڱȽϴ����Ŀ��˵���������Զ�����ܰ������ǽ�����ظ���Activity��Ҳ�ܱ�������ά������չ������Ȥ���Բο����������Ŀ��https://github.com/bboyfeiyu/the-
tech-frontier-app

