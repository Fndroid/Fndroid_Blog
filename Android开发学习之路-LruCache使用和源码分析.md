# Android����ѧϰ֮·-LruCacheʹ�ú�Դ�����

LruCache��Lruָ����LeastRecentlyUsed��Ҳ���ǽ�������ʹ���㷨��Ҳ����˵�������ǽ��л����ʱ������������ˣ�������̭ʹ�õ����ٵĻ������

ΪʲôҪ��LruCache����ʵʹ������ԭ���кܶ࣬��������Ҫ��һ����������App��������ǲ��ӽ��Ƶ���������������ͼƬ����ô���ڷ�������˵��һ�����ٵĸ�������Σ������û���˵��ÿ��ˢ�¶���ζ�������Ĵ��������Լ���ʱ��ȴ������Ի�����Ƽ�����ÿ����Ҫ������App�������ġ�

LruCache�Ѿ������ڹٷ���API�У�������������κ���������ʹ�ã����������ֻ��һ���ڴ滺�棬�����ܽ��б��ػ��棬Ҳ����˵������ڴ治�㣬�����п��ܻ�ʧЧ�����ҵ�App������ʱ�򣬻�������¿�ʼ��Ч�������Ҫ���б��ش��̻��棬�Ƽ�ʹ��DiskLruCache����Ȼû�����ڹٷ�API�У����ǹٷ��Ƽ�����ʹ�ã������ݲ����ۡ�

ʹ�÷�����

ʹ��LruCache��ʵ�ǳ��򵥣�������һ��ͼƬ����Ϊ����

����LruCache����

    
    
    private static class StringBitmapLruCache extends LruCache<String, Bitmap> {
            public StringBitmapLruCache() {
                // ���췽�����뵱ǰӦ�ÿ�������ڴ�İ˷�֮һ
                super((int) (Runtime.getRuntime().maxMemory() / 1024 / 8));
            }
    
            @Override
            // ��дsizeOf�����������㷵��ÿ��Bitmap����ռ�õ��ڴ�
            protected int sizeOf(String key, Bitmap value) {
                return value.getByteCount() / 1024;
            }
    
            @Override
            // �����汻�Ƴ�ʱ���ã���һ�������Ǳ��������Ƴ���ԭ��true��ʾ��LruCache�Ƴ���false��ʾ������remove�Ƴ����ɲ���д
            protected void entryRemoved(boolean evicted, String key, Bitmap oldValue, Bitmap
                    newValue) {
                super.entryRemoved(evicted, key, oldValue, newValue);
            }
    
            @Override
            // ��get������ȡ���������ʱ����ã������Ҫ�����Զ���Ĭ�ϻ��棬��������������߼����ɲ���д
            protected Bitmap create(String key) {
                return super.create(key);
            }
        }
    
    
    LruCache<String, Bitmap> mLruCache = new StringBitmapLruCache();

��ͼƬд�뻺�棺

    
    
    mLruCache.put(name, bitmap);

�ӻ����ȡͼƬ��

    
    
    mLruCache.get(name);

�ӻ�����ɾ��ͼƬ��

    
    
    mLruCache.remove(name);

ʹ�õķ����ܼ򵥣�һ������ֱ��ͨ��get������ȡ���棬�������Null����ͨ���������ͼƬ������֮���ٰ�ͼƬput�������У������´η��ʾͿ��Ի�ȡ����

���ˣ������Ѿ������˽���LruCache���÷������ǲ���Ҫ�����κε���̭����LruCache���Զ������������̭�Ĺ�����



Դ�������

���췽����

    
    
    public LruCache(int maxSize) {
            if (maxSize <= 0) {
                throw new IllegalArgumentException("maxSize <= 0");
            }
            this.maxSize = maxSize;
            this.map = new LinkedHashMap<K, V>(0, 0.75f, true);
        }

���Կ��������췽�������ǻ�ȡ�˻�������ֵ�����Ҵ�����һ��LinkedHashMap������������������LruCache�Ĺؼ�����̭����ʹ�õ��㷨����ʵ����ͨ���������ʵ�ֵģ�����Ȥ���Կ��������Ļ��ơ�



put������

    
    
    public final V put(K key, V value) {
            if (key == null || value == null) {
                throw new NullPointerException("key == null || value == null");
            }
    
            V previous;
            synchronized (this) {
                putCount++;
                size += safeSizeOf(key, value);
                previous = map.put(key, value);
                if (previous != null) {
                    size -= safeSizeOf(key, previous);
                }
            }
    
            if (previous != null) {
                entryRemoved(false, key, previous, value);
            }
    
            trimToSize(maxSize);
            return previous;
        }

������put�����У��ȼ������Ķ������͵Ĵ�С�����õķ�����safeSizeOf�����������ʵֻ�Ǽ򵥵ĵ����������ڹ����ʱ����д��sizeOf������������ظ��������׳��쳣�����Ű�������Ҫ����Ķ������LinkedHashMap�У������������������󣬾Ͱ�size��λ����������������key��Ӧ�Ķ��������entryRemoved�������������Ĭ��Ϊ�գ��������������Ҫ�ڻ������֮�����һЩ��¼�Ļ�������ͨ���ڹ���ʱ��д���������������������������trimToSize���������������ȥ��鵱ǰ��size��û�г���maxSize���������ǿ���Դ��

    
    
    public void trimToSize(int maxSize) {
            while (true) {
                K key;
                V value;
                synchronized (this) {
                    if (size < 0 || (map.isEmpty() && size != 0)) {
                        throw new IllegalStateException(getClass().getName()
                                + ".sizeOf() is reporting inconsistent results!");
                    }
    
                    if (size <= maxSize || map.isEmpty()) {
                        break;
                    }
    
                    Map.Entry<K, V> toEvict = map.entrySet().iterator().next();
                    key = toEvict.getKey();
                    value = toEvict.getValue();
                    map.remove(key);
                    size -= safeSizeOf(key, value);
                    evictionCount++;
                }
    
                entryRemoved(true, key, value, null);
            }
        }

���Կ�����������ж��߼�Ҳ�ܼ򵥣�ͨ�����ϵļ�飬�������maxSize�����LinkedHashMap���޳�һ����ֱ��size���ڻ���С��maxSize������ͬ�������entryRemoved������



get������

    
    
    public final V get(K key) {
            if (key == null) {
                throw new NullPointerException("key == null");
            }
    
            V mapValue;
            synchronized (this) {
                mapValue = map.get(key);
                if (mapValue != null) {
                    hitCount++;
                    return mapValue;
                }
                missCount++;
            }
    
            /*
             * Attempt to create a value. This may take a long time, and the map
             * may be different when create() returns. If a conflicting value was
             * added to the map while create() was working, we leave that value in
             * the map and release the created value.
             */
    
            V createdValue = create(key);
            if (createdValue == null) {
                return null;
            }
    
            synchronized (this) {
                createCount++;
                mapValue = map.put(key, createdValue);
    
                if (mapValue != null) {
                    // There was a conflict so undo that last put
                    map.put(key, mapValue);
                } else {
                    size += safeSizeOf(key, createdValue);
                }
            }
    
            if (mapValue != null) {
                entryRemoved(false, key, createdValue, mapValue);
                return mapValue;
            } else {
                trimToSize(maxSize);
                return createdValue;
            }
        }

������������Կ����������ǵ���get������ʱ��ֱ�Ӵ�LinkedHashMap��getһ����ǰkey�Ķ��󲢷��أ�������ص�ΪNull��������create����������һ�����󣬶�create����Ĭ��Ҳ��һ���շ�����ֱ�ӷ���null�����ԣ��������Ҫ��getʧ�ܵ�ʱ�򴴽�һ��Ĭ�ϵĶ��󣬿����ڹ����ʱ����дcreate�����������д��create��������ô����Ĵ���ᱻִ�У��Ƚ���LinkedHashMap�Ĳ��뷽��������Ѿ����ڣ��򷵻ش��ڵĶ��󣬷��򷵻����Ǵ����Ķ���������Կ����������ظ��ж��б����Ƿ��Ѿ�������ͬ�Ķ���ԭ���ǣ����create���������ʱ��������п���create�����Ķ����Ѿ���put��LinkedHashMap���ˡ�



remove������

    
    
    public final V remove(K key) {
            if (key == null) {
                throw new NullPointerException("key == null");
            }
    
            V previous;
            synchronized (this) {
                previous = map.remove(key);
                if (previous != null) {
                    size -= safeSizeOf(key, previous);
                }
            }
    
            if (previous != null) {
                entryRemoved(false, key, previous, null);
            }
    
            return previous;
        }

�����������߼�Ҳ�����������������������Ҳ�����ƣ��Ͳ�����ˡ�



������һЩ�ط�������Դ����У�˯�ˣ�����

