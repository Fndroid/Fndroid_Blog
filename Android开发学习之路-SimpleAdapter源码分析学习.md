# Android����ѧϰ֮·-SimpleAdapterԴ�����ѧϰ

�����ڿ����ϣ���ʦ�õ���SimpleAdapter��Ȼ��Ů���ڱ�������Ϊʲô���SimpleAdapter����������app���ִ��н�������Ч��������˵���壬Ȼ��Ϳ�ʼ��Դ���룬�������Adapter��ʵ�ַ�ʽ�����Ӧ�úú�ȥ�о��£��ջ��Ǻܶ�ġ�

�ȿ����캯�������캯��������һ������ԴList<Map>��һ�������ļ���Id��resource����һ���ַ��������Ƕ�ӦMap�����Key������һ�����������Ӧitem�����е�ÿ���ؼ���ID

    
    
    public SimpleAdapter(Context context, List<? extends Map<String, ?>> data,
                @LayoutRes int resource, String[] from, @IdRes int[] to) {
            mData = data;
            mResource = mDropDownResource = resource;
            mFrom = from;
            mTo = to;
            mInflater = (LayoutInflater) context.getSystemService(Context.LAYOUT_INFLATER_SERVICE);
        }
    

�ɴ˿ɵã����Adapter��ʵ���ǽ�from�������Ϊkey��Ȼ�����to����õ�ÿ��item�Ŀؼ�id����list�е�map��ÿ������ӳ�䵽item�С�

�������������˼�����Ҫ�ķ�������Щ�����������Զ���Adapter��ʱ��Ҳ��Ҫ��д�ģ�getCount�����ڼ����ж��ٸ�item��getItem�Ǹ���λ�û�ȡ��Ӧ�����ݣ�getItemId�Ǹ���λ������ȡÿ��item��id������Ҫ���Ǵ�����Դ�л�ȡ��صĲ�����

    
    
    public int getCount() {
            return mData.size();
        }
    
    
    public Object getItem(int position) {
            return mData.get(position);
        }
    
    
    public long getItemId(int position) {
            return position;
        }

�ٽ�������������Adapter����Ҫ����getView���ȿ�����

    
    
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

getView����ֱ�ӵ�����һ������createViewFromResource�ķ�������������У����ж�convertView�Ƿ�Ϊ�գ����convertView�ǻ����һ��view����Ϊ����֪����ÿ��listView����ʵ��ֻ�����صļ���item����һ��item������Ļ��ʱ�����item�ͻᱻѭ�����ã���������һ����ʾ��view��Ҳ����˵��������Ļ��view�ĸ����ǿ��ܻ�С��data��size�ģ�����ʹ�õ��Ļ���Ŀ����������view��֮ǰ�Ѿ������ˣ���ô���ǿ��Բ�������ȥӳ�䣬ֱ�ӿ����û����ã�����һ���������ܵ�������������������һ��bindView�ķ���������������У����ݻ�ȡ����view�����ͣ����ж�����Ӧ�����ӳ�䵽�����У��������view��������textview�Ļ����Ͱ�����Դ��ֵ����Ϊ����text�ȣ�����ֻ����checkable��textview��imageview�����ָ�������ǵ��������ʹ�ã������progressbar�����ģ����Ǿ�ֻ���Լ�����һ��adapter�ˡ�

