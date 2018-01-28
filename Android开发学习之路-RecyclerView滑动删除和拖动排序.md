# Android����ѧϰ֮·-RecyclerView����ɾ�����϶�����

[Android����ѧϰ֮·-
RecyclerViewʹ�ó�̽](http://www.cnblogs.com/Fndroid/p/5245250.html)

[Android����ѧϰ֮·-
RecyclerView��Item�Զ��嶯����DefaultItemAnimatorԴ�����](http://www.cnblogs.com/Fndroid/p/5252001.html)

[Android����ѧϰ֮·-����ˢ����ô����](http://www.cnblogs.com/Fndroid/p/5275862.html)

��ƪ�ǽ���������ƪ֮���һ����RecyclerView�Ľ��ܣ������˵���䣬����㻹��ʹ��ListView�Ļ������Է�����ListView�ˡ�RecyclerView�Զ������ǻ���Item��ͼ��ViewHolder�������������Զ�����ֶ����Ķ����ͷָ��ߣ��������Ƕ�Item����һЩ���Ʋ��������⣬��ΪDesign����Ƴ���󷽱����Ǳ�д����Material����App����ListView�ǲ����������ģ����绬�����໥Э����ֻ��RecyclerView��������

* * *

�ȿ���ƪ���ݵ�Ч��ͼ��

![](http://images2015.cnblogs.com/blog/852227/201607/852227-20160710111314655-1686855606.gif)

Ч��������Ҫ�������֣�

  * **�����������һ��Item֮����Խ����϶��������ط�**
  * **�����һ�������ɾ��ĳ��Item**
  * **������ʱ�����һ������Ķ���������֮����������¶���**

* * *



**�����׺��ѣ���Ƭ����Ч��  **

��Material Design�У�����ĳ�������3d��ģʽ�����еģ�Ҳ������ԭ���Ļ�����������һ��Z������ʾ����ĸ߶ȡ�

![](http://images2015.cnblogs.com/blog/852227/201607/852227-20160710132328077-381472148.png)

�����ǵ��һ����Ƭ��ʱ��Ӧ�ø����û�һЩ���������û�֪���Լ��ڲ��������Ƭ��Ҳ���Ǵ�������������Ч��ͼ�Ĵ����������ȳ���ˮ���ƣ����ŵ���Ƭ�����ƶ���ʱ���ȸ������ٿ�ʼ�ƶ���������䣬Ч�����£�������Ķ�������Ҫ��ϸ����

![](http://images2015.cnblogs.com/blog/852227/201607/852227-20160710133347999-930299373.gif)

ʵ�֣�

ˮ����Ч��ʹ��ϵͳ�ṩ�ģ���ΪDemo��ʹ�õ���һ��CardView���������ݣ�����ֻ��Ҫ��CardView�����������Լ��ɣ�

    
    
    android:clickable="true"
    android:foreground="?android:attr/selectableItemBackground"



������³��Ķ���Ҳ���ѣ��ı�View��translationZ���Լ��ɣ�

    
    
    private void pickUpAnimation(View view) {
        ObjectAnimator animator = ObjectAnimator.ofFloat(view, "translationZ", 1f, 10f);
        animator.setInterpolator(new DecelerateInterpolator());
        animator.setDuration(300);
        animator.start();
    }





**�ڻ�����Swipe�����ƶ���Move��Item**

Swipe��ָ������������һ���ɾ����Move�������Ƕ�Item���ƶ����ı��б������Ҫʵ�����������ܣ���Ҫ�õ�һ����ItemTouchHelper�������ǹ���������֮�󣬿��Ե�������attachToRecyclerView�����������ĳ��RecyclerView�У���RecyclerView����ĳЩ�������������ƶ���ʱ������������ʱ����Ļص����ص���Ӧ�ķ�������������Щ�����ж����ݼ����в������ɡ�

    
    
    new ItemTouchHelper(new ItemTouchHelper.Callback() {  
        //ʡ�Դ���
    }).attachToRecyclerView(mRecyclerView);



���ž�����д�ӿ�Callback�ķ����ˣ�������Ҫ��д���м������ֱ��ǣ�

  * isItemViewSwipeEnable : Item�Ƿ���Ի���
  * isLongPressDragEnable ��Item�Ƿ���Գ��� 
  * getMovementFlags �� ��ȡ�ƶ���־
  * onMove �� ��һ��Item�������Item���ʱ�ص���Ҳ�������ݼ�������˳��ı�
  * onMoved �� ��onMove����true��ʱ��ص�
  * onSwiped �� ��ĳ��Item�������뿪��Ļ֮��ص�
  * setSelectedChange �� ĳ��Item������ѡ�лᱻ�ص�����ĳ���������ƶ���Item���ͷ�ʱҲ����

    
    
    new ItemTouchHelper(new ItemTouchHelper.Callback() {
        private RecyclerView.ViewHolder vh;
    
        @Override
        public boolean isItemViewSwipeEnabled() {
            return true;
        }
    
        @Override
        public boolean isLongPressDragEnabled() {
            return true;
        }
    
        @Override
        public int getMovementFlags(RecyclerView recyclerView, RecyclerView.ViewHolder
                viewHolder) {
            // ��ק�ı�ǣ������������������ĸ�����
            int dragFlags = ItemTouchHelper.UP | ItemTouchHelper.DOWN | ItemTouchHelper.LEFT |
                    ItemTouchHelper.RIGHT;
            // �����ı�ǣ������������һ���
            int swipeFlags = ItemTouchHelper.START | ItemTouchHelper.END;
            return makeMovementFlags(dragFlags, swipeFlags);
        }
    
        /*
            �����������ĳ��Item���϶����ƶ���ʱ��ص������������������Ŷ�������viewHolder��Ϊ��ʱΪѡ��״̬
            ����Ϊ�ͷ�״̬
         */
        @Override
        public void onSelectedChanged(RecyclerView.ViewHolder viewHolder, int actionState) {
            super.onSelectedChanged(viewHolder, actionState);
            if (viewHolder != null) {
                vh = viewHolder;
                pickUpAnimation(viewHolder.itemView);
            } else {
                if (vh != null) {
                    putDownAnimation(vh.itemView);
                }
            }
        }
    
    
        @Override
        public boolean onMove(RecyclerView recyclerView, RecyclerView.ViewHolder viewHolder,
                              RecyclerView.ViewHolder target) {
            // �ƶ�ʱ�����б��ж�Ӧ��λ�ò�����true
            Collections.swap(newsList, viewHolder.getAdapterPosition(), target
                    .getAdapterPosition());
            return true;
        }
    
        /*
            ��onMove����trueʱ����
         */
        @Override
        public void onMoved(RecyclerView recyclerView, RecyclerView.ViewHolder viewHolder, int
                fromPos, RecyclerView.ViewHolder target, int toPos, int x, int y) {
            super.onMoved(recyclerView, viewHolder, fromPos, target, toPos, x, y);
            // �ƶ���ɺ�ˢ���б�
            mAdapter.notifyItemMoved(viewHolder.getAdapterPosition(), target
                    .getAdapterPosition());
        }
    
        @Override
        public void onSwiped(RecyclerView.ViewHolder viewHolder, int direction) {
            // �����ݼ��е������Ƴ�
            newsList.remove(viewHolder.getAdapterPosition());
            // ˢ���б�
            mAdapter.notifyItemRemoved(viewHolder.getAdapterPosition());
        }
    }).attachToRecyclerView(mRecyclerView);



����ֱ���������룬������ע�ͣ�Ӧ�ñȽϼ��ˡ�



�������������������ָ������л��

