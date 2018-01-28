# Android开发学习之路-SimpleAdapter源码分析学习

今天在课堂上，老师用到了SimpleAdapter，然后女神在边上问我为什么这个SimpleAdapter不能做到我app那种带有进度条的效果，言语说不清，然后就开始看源代码，发现这个Adapter的实现方式还真的应该好好去研究下，收获还是很多的。

先看构造函数，构造函数传进来一个数据源List<Map>，一个布局文件的Id（resource），一个字符串数组是对应Map里面的Key，还有一个整型数组对应item布局中的每个控件的ID

    
    
    public SimpleAdapter(Context context, List<? extends Map<String, ?>> data,
                @LayoutRes int resource, String[] from, @IdRes int[] to) {
            mData = data;
            mResource = mDropDownResource = resource;
            mFrom = from;
            mTo = to;
            mInflater = (LayoutInflater) context.getSystemService(Context.LAYOUT_INFLATER_SERVICE);
        }
    

由此可得，这个Adapter的实现是将from数组的作为key，然后根据to数组得到每个item的控件id，把list中的map的每个数据映射到item中。

接下来，定义了几个重要的方法，这些方法在我们自定义Adapter的时候也需要重写的，getCount是用于计算有多少个item，getItem是根据位置获取对应的数据，getItemId是根据位置来获取每个item的id，这需要我们从数据源中获取相关的参数。

    
    
    public int getCount() {
            return mData.size();
        }
    
    
    public Object getItem(int position) {
            return mData.get(position);
        }
    
    
    public long getItemId(int position) {
            return position;
        }

再接下来就是整个Adapter的重要方法getView，先看代码

    
    
    public View getView(int position, View convertView, ViewGroup parent) {
            return createViewFromResource(mInflater, position, convertView, parent, mResource);
        }
    
    
    private View createViewFromResource(LayoutInflater inflater, int position, View convertView,
                ViewGroup parent, int resource) {
            View v;
            if (convertView == null) {
                v = inflater.inflate(resource, parent, false);
            } else {
                v = convertView;
            }
    
            bindView(position, v);
    
            return v;
        }



    
    
    private void bindView(int position, View view) {
            final Map dataSet = mData.get(position);
            if (dataSet == null) {
                return;
            }
    
            final ViewBinder binder = mViewBinder;
            final String[] from = mFrom;
            final int[] to = mTo;
            final int count = to.length;
    
            for (int i = 0; i < count; i++) {
                final View v = view.findViewById(to[i]);
                if (v != null) {
                    final Object data = dataSet.get(from[i]);
                    String text = data == null ? "" : data.toString();
                    if (text == null) {
                        text = "";
                    }
    
                    boolean bound = false;
                    if (binder != null) {
                        bound = binder.setViewValue(v, data, text);
                    }
    
                    if (!bound) {
                        if (v instanceof Checkable) {
                            if (data instanceof Boolean) {
                                ((Checkable) v).setChecked((Boolean) data);
                            } else if (v instanceof TextView) {
                                // Note: keep the instanceof TextView check at the bottom of these
                                // ifs since a lot of views are TextViews (e.g. CheckBoxes).
                                setViewText((TextView) v, text);
                            } else {
                                throw new IllegalStateException(v.getClass().getName() +
                                        " should be bound to a Boolean, not a " +
                                        (data == null ? "<unknown type>" : data.getClass()));
                            }
                        } else if (v instanceof TextView) {
                            // Note: keep the instanceof TextView check at the bottom of these
                            // ifs since a lot of views are TextViews (e.g. CheckBoxes).
                            setViewText((TextView) v, text);
                        } else if (v instanceof ImageView) {
                            if (data instanceof Integer) {
                                setViewImage((ImageView) v, (Integer) data);                            
                            } else {
                                setViewImage((ImageView) v, text);
                            }
                        } else {
                            throw new IllegalStateException(v.getClass().getName() + " is not a " +
                                    " view that can be bounds by this SimpleAdapter");
                        }
                    }
                }
            }
        }

getView方法直接调用了一个叫做createViewFromResource的方法，这个方法中，先判断convertView是否为空，这个convertView是缓存的一个view，因为我们知道，每个listView中其实是只有来回的几个item，当一个item滑出屏幕的时候，这个item就会被循环利用，来生成下一个显示的view，也就是说，整个屏幕中view的个数是可能会小于data的size的，这里使用到的缓存目的是如果这个view在之前已经生成了，那么我们可以不再重新去映射，直接可以拿回来用，这是一个提升性能的做法。接下来，调用一个bindView的方法，在这个方法中，根据获取到的view的类型，来判断数据应该如何映射到布局中，比如如果view的类型是textview的话，就把数据源的值设置为它的text等，这里只能是checkable、textview和imageview这三种个类和他们的子类可以使用，如果有progressbar这样的，我们就只能自己定义一个adapter了。

