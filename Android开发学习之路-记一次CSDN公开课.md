# Android����ѧϰ֮·-��һ��CSDN������

�����CSDN������[Android�¼��������ѵ��������](http://edu.csdn.net/huiyiCourse/detail/170)����ʦ����һ�������Ҿ��ò���ȷ��

ԭ���������ģ� **����¼�����ͨ���¼������ͻص����ַ���ʵ��** ��

��һ����֮���ҵı����������ģ�

![](http://images2015.cnblogs.com/blog/852227/201607/852227-20160720211024732-2073452208.jpg)

�����ѧ�Ŀ��Ķ���һ�������⻹�ּ����ͻص������ʱ��������������⣬�ţ�������ʦ�����ˡ�

��ʦ��˵�ˣ��ǲ�һ���ģ����ǵ��ĵ㣨���һ�㣩�ὲ��

�ã�����������׼���ܽ����˴��Сʱ����������ҵı����������ģ�

![](http://images2015.cnblogs.com/blog/852227/201607/852227-20160720211417044-903576331.jpg)

�ϻ�˵���ˣ�����С��Ц����Ҫ���֡�

* * *

���Ƕ�֪���������¼���ʵֻ��һ�֣����ǻص���������˲�����ʲô�ǻص������Կ����ҵ�һƪ�����еĿ�ͷ�Ǹ����ӣ�[�ص�ѧϰ�ʼ�](http://www.cnblogs.com/Fndroid/p/5308713.html)

������ButtonΪ����Button��View�����������ӹ���ɣ���������ֱ�ӿ�View�е�Դ�룺

    
    
        public void setOnClickListener(@Nullable OnClickListener l) {
            if (!isClickable()) {
                setClickable(true);
            }
            getListenerInfo().mOnClickListener = l;
        }

����ܼ򵥣�ֻ�ǰ�һ��OnClickListener�ľ������ౣ���������ѡ������ǵ����ť��ʱ��ϵͳ��Ȼ��ص�onTouchEvent�������������View����������������ģ�

    
    
     1     public boolean onTouchEvent(MotionEvent event) {
     2         final float x = event.getX();
     3         final float y = event.getY();
     4         final int viewFlags = mViewFlags;
     5         final int action = event.getAction();
     6 
     7        // ����ʡ��
     8 
     9         if (((viewFlags & CLICKABLE) == CLICKABLE ||
    10                 (viewFlags & LONG_CLICKABLE) == LONG_CLICKABLE) ||
    11                 (viewFlags & CONTEXT_CLICKABLE) == CONTEXT_CLICKABLE) {
    12             switch (action) {
    13                 case MotionEvent.ACTION_UP:
    14                     boolean prepressed = (mPrivateFlags & PFLAG_PREPRESSED) != 0;
    15                     if ((mPrivateFlags & PFLAG_PRESSED) != 0 || prepressed) {
    16                         boolean focusTaken = false;
    17                         if (isFocusable() && isFocusableInTouchMode() && !isFocused()) {
    18                             focusTaken = requestFocus();
    19                         }
    20 
    21                         if (prepressed) {
    22                             setPressed(true, x, y);
    23                        }
    24 
    25                         if (!mHasPerformedLongPress && !mIgnoreNextUpEvent) {
    26                             if (!focusTaken) {
    27 
    28                                 if (mPerformClick == null) {
    29                                     mPerformClick = new PerformClick();
    30                                 }
    31                                 if (!post(mPerformClick)) {
    32                                     performClick();
    33                                 }
    34                             }
    35                         }
    36 
    37                         if (mUnsetPressedState == null) {
    38                             mUnsetPressedState = new UnsetPressedState();
    39                         }
    40 
    41                         if (prepressed) {
    42                             postDelayed(mUnsetPressedState,
    43                                     ViewConfiguration.getPressedStateDuration());
    44                         } else if (!post(mUnsetPressedState)) {
    45                             mUnsetPressedState.run();
    46                         }
    47 
    48                         removeTapCallback();
    49                     }
    50                     mIgnoreNextUpEvent = false;
    51                     break;
    52 
    53                 case MotionEvent.ACTION_DOWN:
    54                    // ����ʡ��
    55                     break;
    56 
    57                 case MotionEvent.ACTION_CANCEL:
    58                     // ����ʡ��
    59                     break;
    60 
    61                 case MotionEvent.ACTION_MOVE:
    62                    // ����ʡ��
    63                     break;
    64             }
    65 
    66             return true;
    67         }
    68 
    69         return false;
    70     }

������ɾ���˺ܶ���룬����֪��һ���������һ��ACTION_DOWN�����ɸ�ACTION_MOVE��һ��ACTION_UP��ɵģ�Ҳ����˵���������ɣ���ִ��ACTION_UP�еĴ��롣�ڵ�32���У�������һ����������performClick�������������ֱ��ִ�е���¼��ģ����ǿ�������������ڲ���

    
    
     1     public boolean performClick() {
     2         final boolean result;
     3         final ListenerInfo li = mListenerInfo;
     4         if (li != null && li.mOnClickListener != null) {
     5             playSoundEffect(SoundEffectConstants.CLICK);
     6             li.mOnClickListener.onClick(this);
     7             result = true;
     8         } else {
     9             result = false;
    10         }
    11 
    12         sendAccessibilityEvent(AccessibilityEvent.TYPE_VIEW_CLICKED);
    13         return result;
    14     }

��6���У����������Ǵ��������Ǹ��ӿڵ�ʵ�����е�onClick����������ǻص�����Ϊ�ⲻ�������ĵ��á�

* * *

�ܽ᣺������ʲô�����¼�������ͨ���ص���ʵ�ֵġ����ǵ��֮���¼���ϵͳ�����еײ�ؼ����ϴ����ؼ���Ȼ��ص���View��onTouchEvent��������������ص����������õĽӿ���onClick�������ҹ�Ȼ֪���ص��ͼ�����ͬ���������Ǿ��Բ��������ַ�����



�ҿ�������˵�����ʦ�ġ����Android���塷��ȷʵ����ô����ģ������ﲹ��˵һ�°ɡ��鱾��Ϊ�����ͻص�ʱ�����¼��������������������ȥ����[�ٷ��ĵ�](https://developer.android.com/guide/topics/ui/ui-
events.html)����ô��ʵ���ĵ��о���˵�����������£�

![](http://images2015.cnblogs.com/blog/852227/201607/852227-20160722122122826-214768099.png)

 ���룺

���������������ֵĸ���View�У�������Ѿ�ע�⵽�������еĻص���������ȥ��UI�¼��ǳ����á���Щ�������ڶ�������Ӧ������ʱ����Android��ܵ��á����磬��һ��View������Button�������������View��onTouchEvent���������ᱻִ�С�Ȼ����Ϊ�˽ػ���Щ�¼��������Ҫ�̳�����ಢ��д��Щ������Ϊ�˴�����Щ�¼����̳����е�View�����ǲ�ʵ�ʵġ�
**�����ΪʲôView���а���һϵ��Ƕ����һЩ������������׶���Ļص��Ľӿڡ���Щ�ӿڣ�����Ϊ�¼�������** �����������UI�в����û���Ϊ��ƾ֤��

����˵�����¼���������ʵ�����ɻص�������ɵĽӿڡ�Ҳ�ͻ���������ɻص�ʵ�ֵġ�ΪʲôҪ�����أ����ɺܼ򵥣���Ϊ���Ǵ���Ĳ���һ�����ڻ�ж�������View�У�



��ô��������˵�Ļص���������Щ�����ĵ����²����У��о��˼������õĻص���

![](http://images2015.cnblogs.com/blog/852227/201607/852227-20160722123340263-1525578070.png)



* * *

���������һ�θ����¡����˾��������ʦ��ô�����������ġ��Ҿ�˵˵�ҵĿ����ɣ����������ȥ�������Android���塷����ᷢ�֣��Ȿ��Ĵ󲿷����ݾ��Ƿ����API���ܣ�˵��Android������Ȩ�����Ҿ����������Ʋ��ϡ�¥������Ҳ˵���ˣ�ֻҪ��ȥȺ����һ�ʣ���Ҿ��ܷ������Ǹ�Ц�ģ���ʵ������Ҳ�����ˣ���֪����΢������QQȺ�������ˣ��õ��Ĵ𰸶��ǣ����ǻص�����������Ϊʲô����д���Ҿ��ðɣ����ǻ�����Ӳ�Ǿ�����û����ģ��Ƚϸ���û���ã���Ҳ���Ѹ���˵����

