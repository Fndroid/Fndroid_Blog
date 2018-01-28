# Android开发学习之路-LruCache使用和源码分析

LruCache的Lru指的是LeastRecentlyUsed，也就是近期最少使用算法。也就是说，当我们进行缓存的时候，如果缓存满了，会先淘汰使用的最少的缓存对象。

为什么要用LruCache？其实使用它的原因有很多，例如我们要做一个电子商务App，如果我们不加节制的向服务器请求大量图片，那么对于服务器来说是一个不少的负担，其次，对于用户来说，每次刷新都意味着流量的大量消耗以及长时间等待，所以缓存机制几乎是每个需要联网的App必须做的。

LruCache已经存在于官方的API中，所以无需添加任何依赖即可使用，而这个缓存只是一个内存缓存，并不能进行本地缓存，也就是说，如果内存不足，缓存有可能会失效，而且当App重启的时候，缓存会重新开始生效。如果想要进行本地磁盘缓存，推荐使用DiskLruCache，虽然没包含在官方API中，但是官方推荐我们使用，本文暂不讨论。

使用方法：

使用LruCache其实非常简单，下面以一个图片缓存为例：

创建LruCache对象：

    
    
    private static class StringBitmapLruCache extends LruCache<String, Bitmap> {
            public StringBitmapLruCache() {
                // 构造方法传入当前应用可用最大内存的八分之一
                super((int) (Runtime.getRuntime().maxMemory() / 1024 / 8));
            }
    
            @Override
            // 重写sizeOf方法，并计算返回每个Bitmap对象占用的内存
            protected int sizeOf(String key, Bitmap value) {
                return value.getByteCount() / 1024;
            }
    
            @Override
            // 当缓存被移除时调用，第一个参数是表明缓存移除的原因，true表示被LruCache移除，false表示被主动remove移除，可不重写
            protected void entryRemoved(boolean evicted, String key, Bitmap oldValue, Bitmap
                    newValue) {
                super.entryRemoved(evicted, key, oldValue, newValue);
            }
    
            @Override
            // 当get方法获取不到缓存的时候调用，如果需要创建自定义默认缓存，可以在这里添加逻辑，可不重写
            protected Bitmap create(String key) {
                return super.create(key);
            }
        }
    
    
    LruCache<String, Bitmap> mLruCache = new StringBitmapLruCache();

把图片写入缓存：

    
    
    mLruCache.put(name, bitmap);

从缓存读取图片：

    
    
    mLruCache.get(name);

从缓存中删除图片：

    
    
    mLruCache.remove(name);

使用的方法很简单，一般我们直接通过get方法读取缓存，如果返回Null，再通过网络访问图片，访问之后，再把图片put到缓存中，这样下次访问就可以获取到。

至此，我们已经基本了解了LruCache的用法，我们不需要进行任何的淘汰处理，LruCache会自动帮我们完成淘汰的工作。



源码分析：

构造方法：

    
    
    public LruCache(int maxSize) {
            if (maxSize <= 0) {
                throw new IllegalArgumentException("maxSize <= 0");
            }
            this.maxSize = maxSize;
            this.map = new LinkedHashMap<K, V>(0, 0.75f, true);
        }

可以看到，构造方法中我们获取了缓存的最大值，并且创建了一个LinkedHashMap对象，这个对象就是整个LruCache的关键，淘汰最少使用的算法，其实就是通过这个类来实现的，有兴趣可以看看这个类的机制。



put方法：

    
    
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

解析：put方法中，先计算插入的对象类型的大小，调用的方法是safeSizeOf，这个方法其实只是简单的调用了我们在构造的时候重写的sizeOf方法，如果返回负数，则抛出异常。接着把我们需要缓存的对象插入LinkedHashMap中，如果缓存中有这个对象，就把size复位。如果缓存中有这个key对应的对象，则调用entryRemoved方法，这个方法默认为空，但是如果我们需要在缓存更新之后进行一些记录的话，可以通过在构造时重写这个方法来做到。接下来，调用trimToSize方法，这个方法是去检查当前的size有没有超过maxSize，这里我们看看源码

    
    
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

可以看到，这里的判断逻辑也很简单，通过不断的检查，如果超过maxSize，则从LinkedHashMap中剔除一个，直到size等于或者小于maxSize，这里同样会调用entryRemoved方法。



get方法：

    
    
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

解析：这里可以看到，当我们调用get方法的时候，直接从LinkedHashMap中get一个当前key的对象并返回，如果返回的为Null，则会调用create方法来创建一个对象，而create方法默认也是一个空方法，直接返回null，所以，如果你需要在get失败的时候创建一个默认的对象，可以在构造的时候重写create方法。如果重写了create方法，那么下面的代码会被执行，先进行LinkedHashMap的插入方法，如果已经存在，则返回存在的对象，否则返回我们创建的对象。这里可以看到，这里重复判断列表中是否已经存在相同的对象，原因是，如果create方法处理的时间过长，有可能create出来的对象已经被put到LinkedHashMap中了。



remove方法：

    
    
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

解析：这里逻辑也很清晰，跟上面的两个方法也很类似，就不唠嗑了。



其他的一些地方，看看源码就行，睡了，晚安。

