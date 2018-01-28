# Android����ѧϰ֮·-RecyclerView��Item�Զ��嶯����DefaultItemAnimatorԴ�����

���ǹ���RecyclerView�ĵڶ�ƪ��˵��������Զ���Item������������ע�⣬���Ĳ����������ľ���ʵ�ַ�����ֻ�Ǹ��ߴ�����ȥ�Զ��嶯�������ȥ�ο�Դ���롣

����֪����RecyclerViewĬ�ϻ�ʹ��DefaultItemAnimator���������������Ҫ�Զ��嶯������ôӦ�úúõĶ���������Դ���룬������������ѧϰ��ô�Զ��壬��ҪѧϰAndroid�����ģʽ��

��Ū����һ���£�DefaultItemAnimator�̳���SimpleItemAnimator��SimpleItemAnimator�̳���RecyclerView.ItemAnimator�����������Ҫ�Զ��嶯������򵥵ķ����Ǽ̳�SimpleItemAnimator����Σ����������������֣��ֱ���Add��Remove��Move�Լ�Change����������ֻ�о�Remove����һ������

�����ȿ�SimpleItemAnimator�е�Դ�룬��SimpleItemAnimator���м�����Ҫ�ķ�����

    
    
     1 @Override
     2     public boolean animateDisappearance(@NonNull ViewHolder viewHolder,
     3             @NonNull ItemHolderInfo preLayoutInfo, @Nullable ItemHolderInfo postLayoutInfo) {
     4         int oldLeft = preLayoutInfo.left;
     5         int oldTop = preLayoutInfo.top;
     6         View disappearingItemView = viewHolder.itemView;
     7         int newLeft = postLayoutInfo == null ? disappearingItemView.getLeft() : postLayoutInfo.left;
     8         int newTop = postLayoutInfo == null ? disappearingItemView.getTop() : postLayoutInfo.top;
     9         if (!viewHolder.isRemoved() && (oldLeft != newLeft || oldTop != newTop)) {
    10             disappearingItemView.layout(newLeft, newTop,
    11                     newLeft + disappearingItemView.getWidth(),
    12                     newTop + disappearingItemView.getHeight());
    13             if (DEBUG) {
    14                 Log.d(TAG, "DISAPPEARING: " + viewHolder + " with view " + disappearingItemView);
    15             }
    16             return animateMove(viewHolder, oldLeft, oldTop, newLeft, newTop);
    17         } else {
    18             if (DEBUG) {
    19                 Log.d(TAG, "REMOVED: " + viewHolder + " with view " + disappearingItemView);
    20             }
    21             return animateRemove(viewHolder);
    22         }
    23     }

�����������������дRecyclerView.ItemAnimator�ģ��ӿ��в����ֱ���ViewHolder��prelayoutInfo�Լ�postLayoutInfo����һ��������ָItem��ViewHolder������ͨ����������itemView����ȡ����View���ڶ���������ָItemɾ��ǰ��λ����Ϣ����������ָ�µ�λ����Ϣ���ٽ��������ж�ViewHolder�Ƿ��Ѿ����Ƴ��Լ�λ���Ƿ����仯��Ȼ���ڵ���animateRemove������󷽷����������Ҫ�Զ��嶯��������Ҫȥʵ�������ص�˼�룩��

    
    
    1 public final void dispatchRemoveStarting(ViewHolder item) {
    2         onRemoveStarting(item);
    3     }
    
    
    1 public void onRemoveStarting(ViewHolder item) {
    2     }

������dispatchRemoveStaring����һ��final������Ҳ���ǲ��ܱ���д�����������Ҫ����һЩ��Remove��ʼ��ʱ����߼������Ǿ���Ҫ��animateRemove�����е���������������������ִ��һ��onRemoveStaring�������������������������д�������߼�Ӧ��д��onRemoveStaring�У������ǵ���dispatchRemoveStaring��ʱ��onRemoveStaring�ͻᱻִ�С�

����ֻ˵�����������ǣ��������������ľͲ�ֻ��������������

**���ԣ������Ǽ̳���SimpleItemAnimator��ʱ����Ҫʵ�������һЩ������һ����������Щ��**

**��
animateRemove��Add��Move��Change������Щ�������ڶ���������ʱ��ص���һ�����������������б��¼ÿ��Item�Ķ����Լ�����**

**�� endAnimation��endAnimations���ֱ�����һ��Item���Ƕ��Item��Ҫ����ֹͣ��ʱ��ص�**

**�� isRunning�������Ҫ˳��������ʱ�򣬱���Ҫ��д����������ܶ�ʱ�������������ص�ʱ�򻬶����پ�����������߼�����**

**��
run'PendingAnimations����������Ҫ��һ����������ΪanimateDisappearence�ȷ������ص���animateRemove�ȷ������ص�ֵ��������������Ǹ�����Щֵ��ȷ���Ƿ���׼���õĶ�����Ҫ���ţ�����У��ͻ�ص�������������������������Ҫ����ÿһ��������Remove��Add��Move�Լ�Change���Ķ���**

**���ԣ����ǵ�һ�㲽����ǣ�**

**�ٴ���һ��SimpleItemAnimator������**

**�ڴ���ÿ�������Ķ����б�**

**����дanimateRemove�ȷ��������������ж�����������Щ�����ᱻ�ص���������м�¼������trueʹ��
**run'PendingAnimations��ʼִ��****

**����дrun'PendingAnimations�����۵ķ�������true��ʱ�򣬾���Ϊ��Ҫִ�ж�����������Ҫ�Ѷ���ִ�е��߼�д��������**

**����дisRunning���ṩ��������״̬��һ���Ƿ��ض����б��Ƿ�Ϊ��**

**���������Ҫ����дendAnimation��endAnimations��onRemoveFinish�ȷ���**

����Ĳ������ˣ��������ǻ����������ô�������������ż���Ϊ�˷������ǣ��ȸ���ʵ�Ѿ��ṩ��DefaultItemAnimator�����ǿ��Բο�һЩ����Դ�룬û���˽��ı�Դ���е���������Ҫ�������㹻�����ģ�

DefaultItemAnimator�ж�����һЩArrayList����Ŷ�������Ϣ�����£�

    
    
    private ArrayList<ViewHolder> mPendingRemovals = new ArrayList<>();
    private ArrayList<ViewHolder> mRemoveAnimations = new ArrayList<>();
    
    
    1 @Override
    2     public boolean animateRemove(final ViewHolder holder) {
    3         resetAnimation(holder);
    4         mPendingRemovals.add(holder);
    5         return true;
    6     }

���������Կ���animatorRemove����ֱ���ǰ�viewholder�����б��У�Ȼ�󷵻�true

    
    
     1 @Override
     2     public void runPendingAnimations() {
     3         boolean removalsPending = !mPendingRemovals.isEmpty();
     4         boolean movesPending = !mPendingMoves.isEmpty();
     5         boolean changesPending = !mPendingChanges.isEmpty();
     6         boolean additionsPending = !mPendingAdditions.isEmpty();
     7         if (!removalsPending && !movesPending && !additionsPending && !changesPending) {
     8             // nothing to animate
     9             return;
    10         }
    11         // First, remove stuff
    12         for (ViewHolder holder : mPendingRemovals) {
    13             animateRemoveImpl(holder);
    14         }
    15         mPendingRemovals.clear();
    16         // Next, move stuff
    17         ......
    18         // Next, change stuff, to run in parallel with move animations
    19         ......
    20         // Next, add stuff
    21         ......
    22     }            

�����������������֪����runPendingAnimations��ִ�У��ɿ���������������б����˶����б�����ÿ��Item��ִ����animatorRemoveImpl���������������ķ�����ʱ��ʡ�ԣ�����Ȥ�Ŀ��������Ķ���

    
    
     1 private void animateRemoveImpl(final ViewHolder holder) {
     2         final View view = holder.itemView;
     3         final ViewPropertyAnimatorCompat animation = ViewCompat.animate(view);
     4         mRemoveAnimations.add(holder);
     5         animation.setDuration(getRemoveDuration())
     6                 .alpha(0).setListener(new VpaListenerAdapter() {
     7             @Override
     8             public void onAnimationStart(View view) {
     9                 dispatchRemoveStarting(holder);
    10             }
    11 
    12             @Override
    13             public void onAnimationEnd(View view) {
    14                 animation.setListener(null);
    15                 ViewCompat.setAlpha(view, 1);
    16                 dispatchRemoveFinished(holder);
    17                 mRemoveAnimations.remove(holder);
    18                 dispatchFinishedWhenDone();
    19             }
    20         }).start();
    21     }

���������Կ���animatorRemoveImpl������ʵ�������������ľ����߼���������ô�����ڱ��ķ�Χ�У�������ִ���˶���֮��Ҳ�����ڶ�����Listener�е�onAnimatorEnd�е�����dispatchRemoveFinish�����ǵ��������������ִ��onRemoveFinish������onRemoveFinish�����ǿ��Թ���������д�ġ�Ȼ���item�Ƴ������б�

    
    
     1 @Override
     2     public boolean isRunning() {
     3         return (!mPendingAdditions.isEmpty() ||
     4                 !mPendingChanges.isEmpty() ||
     5                 !mPendingMoves.isEmpty() ||
     6                 !mPendingRemovals.isEmpty() ||
     7                 !mMoveAnimations.isEmpty() ||
     8                 !mRemoveAnimations.isEmpty() ||
     9                 !mAddAnimations.isEmpty() ||
    10                 !mChangeAnimations.isEmpty() ||
    11                 !mMovesList.isEmpty() ||
    12                 !mAdditionsList.isEmpty() ||
    13                 !mChangesList.isEmpty());
    14     }

������isRunning������ʵ���Ǹ��ݶ����б��Ƿ�Ϊ�������ؽ��

��������һЩ���������Լ��Ķ�Դ���롣



