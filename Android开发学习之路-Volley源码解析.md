# Android开发学习之路-Volley源码解析

从简单的StringRequest入手看看Volley的工作机制。

先简单说下Volley的用法：

**① 获取一个RequestQueue**

    
    
    mRequestQueue = Volley.newRequestQueue( this);

**② 构造一个StringRequest对象**

    
    
    mStringRequest =  new StringRequest(url, new Response.Listener<String>() {
                @Override
                public void onResponse(String response) {
                    mTextView.setText(response);
                }
            }, new Response.ErrorListener() {
                @Override
                public void onErrorResponse(VolleyError error) {
                    Log.d(TAG, "onErrorResponse: " + error.getMessage());
                }
            });

**③ 将StringRequest对象add进RequestQueue**

    
    
    mRequestQueue.add(mStringRequest);





* * *

 下面通过源码跟踪一下Volley处理请求的过程：

用过Volley都知道，请求一般是继承自Request这个抽象类，那么StringRequest自然也是。在构造方法中需要几个参数，具体的构造方法如下

    
    
    public StringRequest(int method, String url, Listener<String> listener,
                ErrorListener errorListener) {
            super(method, url, errorListener);
            mListener = listener;
        }

可以看到我们需要给它指定请求的方法、url、成功返回的回调类和错误的回调类。

接着我们在需要请求的时候，把这个对象传递给RequestQueue的 **add**
方法。这个add方法是做什么的我们其实都能猜到，就是把这个请求放到队列中。

    
    
     1 public <T> Request<T> add(Request<T> request) {
     2         // Tag the request as belonging to this queue and add it to the set of current requests.
     3         request.setRequestQueue(this);
     4         synchronized (mCurrentRequests) {
     5             mCurrentRequests.add(request);
     6         }
     7 
     8         // Process requests in the order they are added.
     9         request.setSequence(getSequenceNumber());
    10         request.addMarker("add-to-queue");
    11 
    12         // If the request is uncacheable, skip the cache queue and go straight to the network.
    13         if (!request.shouldCache()) {
    14             mNetworkQueue.add(request);
    15             return request;
    16         }
    17 
    18         // Insert request into stage if there's already a request with the same cache key in flight.
    19         synchronized (mWaitingRequests) {
    20             String cacheKey = request.getCacheKey();
    21             if (mWaitingRequests.containsKey(cacheKey)) {
    22                 // There is already a request in flight. Queue up.
    23                 Queue<Request<?>> stagedRequests = mWaitingRequests.get(cacheKey);
    24                 if (stagedRequests == null) {
    25                     stagedRequests = new LinkedList<Request<?>>();
    26                 }
    27                 stagedRequests.add(request);
    28                 mWaitingRequests.put(cacheKey, stagedRequests);
    29                 if (VolleyLog.DEBUG) {
    30                     VolleyLog.v("Request for cacheKey=%s is in flight, putting on hold.", cacheKey);
    31                 }
    32             } else {
    33                 // Insert 'null' queue for this cacheKey, indicating there is now a request in
    34                 // flight.
    35                 mWaitingRequests.put(cacheKey, null);
    36                 mCacheQueue.add(request);
    37             }
    38             return request;
    39         }
    40     }

代码可能有点长，但是核心的地方也比较简单，就是进行线程同步之后将请求放入请求的集合mNetworkQueue（在不需要缓存的情况下）。如果需要缓存请求，则同步等待序列，判断请求是否已经被发出过，根据情况返回请求。

**因为这里会根据是否需要缓存进行区别处理，下面按照不需要缓存来讲解源码。而StringRequest是会进行缓存的。**

接着我们看看RequestQueue是怎么工作的。这里其实我们也是可以知道，既然命名为Queue，肯定是会通过一个线程来不停的遍历队列中的等待者然后进行处理，跟Handler中的MessageQueue是很类似的。

我们先看RequestQueue的构造，我们一般通过下面这个方法来获得一个RequestQueue

    
    
    mRequestQueue = Volley.newRequestQueue(this);

而实际上这个构造的方法内容是下面这样的

    
    
     1 public static RequestQueue newRequestQueue(Context context, HttpStack stack) {
     2         File cacheDir = new File(context.getCacheDir(), DEFAULT_CACHE_DIR);
     3 
     4         String userAgent = "volley/0";
     5         try {
     6             String packageName = context.getPackageName();
     7             PackageInfo info = context.getPackageManager().getPackageInfo(packageName, 0);
     8             userAgent = packageName + "/" + info.versionCode;
     9         } catch (NameNotFoundException e) {
    10         }
    11 
    12         if (stack == null) {
    13             if (Build.VERSION.SDK_INT >= 9) {
    14                 stack = new HurlStack();
    15             } else {
    16                 // Prior to Gingerbread, HttpUrlConnection was unreliable.
    17                 // See: http://android-developers.blogspot.com/2011/09/androids-http-clients.html
    18                 stack = new HttpClientStack(AndroidHttpClient.newInstance(userAgent));
    19             }
    20         }
    21 
    22         Network network = new BasicNetwork(stack);
    23 
    24         RequestQueue queue = new RequestQueue(new DiskBasedCache(cacheDir), network);
    25         queue.start();
    26 
    27         return queue;
    28     }

从注释可以看到，在api等级大于9的时候，使用HttpUrlConnection来进行主要的网络请求工作，到这里已经很明显了，
**Volley底层是使用HttpUrlConnection进行的**
。我们看到这里是使用了一个HttpClientStack来包装根据userAgent得到的HttpClient（这个类在最新的源码中已经被移除了），而HttpClient实际上就是使用HttpUrlConnection来实现的。最后被包装为一个BasicNetwork对象。

接着根据得到的BasicNetwork对象和一个DiskBasedCache对象（磁盘缓存）来构造一个RequestQueue，并且调用了它的start方法来启动这个线程。

再看看RequestQueue的start方法：

    
    
     1 public void start() {
     2         stop();  // Make sure any currently running dispatchers are stopped.
     3         // Create the cache dispatcher and start it.
     4         mCacheDispatcher = new CacheDispatcher(mCacheQueue, mNetworkQueue, mCache, mDelivery);
     5         mCacheDispatcher.start();
     6 
     7         // Create network dispatchers (and corresponding threads) up to the pool size.
     8         for (int i = 0; i < mDispatchers.length; i++) {
     9             NetworkDispatcher networkDispatcher = new NetworkDispatcher(mNetworkQueue, mNetwork,
    10                     mCache, mDelivery);
    11             mDispatchers[i] = networkDispatcher;
    12             networkDispatcher.start();
    13         }
    14     }

这里我们先不顾这个mCacheDispatcher，直接看到下面的for循环，这个for循环遍历了mDispatchers，这个mDispatcher其实相当于一个线程池，这个线程池的大小默认是4。然后分别让这里面的线程运行起来（调用了它们的start方法）。这里为什么要有多个线程来处理呢？原因很简单，因为我们每一个请求都不一定会马上处理完毕，多个线程进行同时处理的话效率会提高。

我们进入NetworkDispatcher看看它的run方法：

    
    
     1 @Override
     2     public void run() {
     3         Process.setThreadPriority(Process.THREAD_PRIORITY_BACKGROUND);
     4         while (true) {
     5             long startTimeMs = SystemClock.elapsedRealtime();
     6             Request<?> request;
     7             try {
     8                 // Take a request from the queue.
     9                 request = mQueue.take();
    10             } catch (InterruptedException e) {
    11                 // We may have been interrupted because it was time to quit.
    12                 if (mQuit) {
    13                     return;
    14                 }
    15                 continue;
    16             }
    17 
    18             try {
    19                 request.addMarker("network-queue-take");
    20 
    21                 // If the request was cancelled already, do not perform the
    22                 // network request.
    23                 if (request.isCanceled()) {
    24                     request.finish("network-discard-cancelled");
    25                     continue;
    26                 }
    27 
    28                 addTrafficStatsTag(request);
    29 
    30                 // Perform the network request.
    31                 NetworkResponse networkResponse = mNetwork.performRequest(request);
    32                 request.addMarker("network-http-complete");
    33 
    34                 // If the server returned 304 AND we delivered a response already,
    35                 // we're done -- don't deliver a second identical response.
    36                 if (networkResponse.notModified && request.hasHadResponseDelivered()) {
    37                     request.finish("not-modified");
    38                     continue;
    39                 }
    40 
    41                 // Parse the response here on the worker thread.
    42                 Response<?> response = request.parseNetworkResponse(networkResponse);
    43                 request.addMarker("network-parse-complete");
    44 
    45                 // Write to cache if applicable.
    46                 // TODO: Only update cache metadata instead of entire record for 304s.
    47                 if (request.shouldCache() && response.cacheEntry != null) {
    48                     mCache.put(request.getCacheKey(), response.cacheEntry);
    49                     request.addMarker("network-cache-written");
    50                 }
    51 
    52                 // Post the response back.
    53                 request.markDelivered();
    54                 mDelivery.postResponse(request, response);
    55             } catch (VolleyError volleyError) {
    56                 volleyError.setNetworkTimeMs(SystemClock.elapsedRealtime() - startTimeMs);
    57                 parseAndDeliverNetworkError(request, volleyError);
    58             } catch (Exception e) {
    59                 VolleyLog.e(e, "Unhandled exception %s", e.toString());
    60                 VolleyError volleyError = new VolleyError(e);
    61                 volleyError.setNetworkTimeMs(SystemClock.elapsedRealtime() - startTimeMs);
    62                 mDelivery.postError(request, volleyError);
    63             }
    64         }
    65     }

第三行设置了这些线程的优先级，这个优先级比较低，目的是为了尽量减少对UI线程的影响保证流畅度。

接着第9行，调用mQueue的take方法取出队列头的一个请求进行处理，这个mQueue是什么？其实它就是我们在上面add方法中添加进去的一个请求。

我们不看这些设置状态标记的地方，直接看到第31行，如果请求没有被取消，也就是正常的情况下，我们会调用mNetwork的performRequest方法进行请求的处理。不知道你还记的这个mNetwork不，它其实就是我们上面提到的那个由HttpUrlConnection层层包装的网络请求对象。

如果请求得到了结果，我们会看到54行调用了mDelivery的postResponose方法来回传我们的请求结果。



**这里还有两个重要的地方需要再了解一下，一个是究竟postResponse是怎么传回我们的请求结果的，另一个就是performRequest是怎么去进行网络请求的。**

**先看第一个，结果的回传。**
我们先了解下这个mDelivery是怎么定义的。它其实是在RequestQueue中创建的，可以看到RequestQueue的其中一个构造方法：

    
    
    1 public RequestQueue(Cache cache, Network network, int threadPoolSize) {
    2         this(cache, network, threadPoolSize,
    3                 new ExecutorDelivery(new Handler(Looper.getMainLooper())));
    4     }

这里直接就new了一个ExecutorDelivery对象，并传入了一个不断从MainLooper中获取Message的Handler。再看看postResponse方法的内容：

    
    
    1 @Override
    2     public void postResponse(Request<?> request, Response<?> response, Runnable runnable) {
    3         request.markDelivered();
    4         request.addMarker("post-response");
    5         mResponsePoster.execute(new ResponseDeliveryRunnable(request, response, runnable));
    6     }

这里看到第5行调用了mResponsePoster的execute方法并传入了一个ResponseDeliveryRunnable对象，再看mResponsePoster的定义：

    
    
    1 public ExecutorDelivery(final Handler handler) {
    2         // Make an Executor that just wraps the handler.
    3         mResponsePoster = new Executor() {
    4             @Override
    5             public void execute(Runnable command) {
    6                 handler.post(command);
    7             }
    8         };
    9     }

也就是我们在这里把ResponseDeliveryRunnable对象通过Handler的post方法发送出去了。这里为什么要发送到MainLooper中？因为RequestQueue是在子线程中执行的，回调到的代码也是在子线程中的，如果在回调中修改UI，就会报错。再者，为什么要使用post方法？原因也很简单，因为我们在消息发出之后再进行回调，post方法允许我们传入一个Runnable的实现了，post成功会自动执行它的run方法，这个时候在run方法中进行结果的判断并且进行回调：

    
    
     1 private class ResponseDeliveryRunnable implements Runnable {
     2         private final Request mRequest;
     3         private final Response mResponse;
     4         private final Runnable mRunnable;
     5 
     6         public ResponseDeliveryRunnable(Request request, Response response, Runnable runnable) {
     7             mRequest = request;
     8             mResponse = response;
     9             mRunnable = runnable;
    10         }
    11 
    12         @SuppressWarnings("unchecked")
    13         @Override
    14         public void run() {
    15             // If this request has canceled, finish it and don't deliver.
    16             if (mRequest.isCanceled()) {
    17                 mRequest.finish("canceled-at-delivery");
    18                 return;
    19             }
    20 
    21             // Deliver a normal response or error, depending.
    22             if (mResponse.isSuccess()) {
    23                 mRequest.deliverResponse(mResponse.result);
    24             } else {
    25                 mRequest.deliverError(mResponse.error);
    26             }
    27 
    28             // If this is an intermediate response, add a marker, otherwise we're done
    29             // and the request can be finished.
    30             if (mResponse.intermediate) {
    31                 mRequest.addMarker("intermediate-response");
    32             } else {
    33                 mRequest.finish("done");
    34             }
    35 
    36             // If we have been provided a post-delivery runnable, run it.
    37             if (mRunnable != null) {
    38                 mRunnable.run();
    39             }
    40        }
    41     }

可以看到，23行是调用Request的deleverResponse方法将结果回调给StringRequest。接着看看StringRequest中该方法是实现：

    
    
    1 @Override
    2     protected void deliverResponse(String response) {
    3         mListener.onResponse(response);
    4     }

直接通过我们构造StringRequest时传进来的Listener的回调方法onResponse来将结果回调给Activity。deleverError也是同样的做法。



**再来看看performRequest是怎么进行网络请求的。**

mNetwork是Network接口的对象，而这个接口只有一个实现类，就是BasicNetwork，我们看看这个BasicNetwork中的performRequest的代码：

    
    
     1 @Override
     2     public NetworkResponse performRequest(Request<?> request) throws VolleyError {
     3         long requestStart = SystemClock.elapsedRealtime();
     4         while (true) {
     5             HttpResponse httpResponse = null;
     6             byte[] responseContents = null;
     7             Map<String, String> responseHeaders = Collections.emptyMap();
     8             try {
     9                 // Gather headers.
    10                 Map<String, String> headers = new HashMap<String, String>();
    11                 addCacheHeaders(headers, request.getCacheEntry());
    12                 httpResponse = mHttpStack.performRequest(request, headers);
    13                 StatusLine statusLine = httpResponse.getStatusLine();
    14                 int statusCode = statusLine.getStatusCode();
    15 
    16                 responseHeaders = convertHeaders(httpResponse.getAllHeaders());
    17                 // Handle cache validation.
    18                 if (statusCode == HttpStatus.SC_NOT_MODIFIED) {
    19 
    20                     Entry entry = request.getCacheEntry();
    21                     if (entry == null) {
    22                         return new NetworkResponse(HttpStatus.SC_NOT_MODIFIED, null,
    23                                 responseHeaders, true,
    24                                 SystemClock.elapsedRealtime() - requestStart);
    25                     }
    26 
    27                     // A HTTP 304 response does not have all header fields. We
    28                     // have to use the header fields from the cache entry plus
    29                     // the new ones from the response.
    30                     // http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html#sec10.3.5
    31                     entry.responseHeaders.putAll(responseHeaders);
    32                     return new NetworkResponse(HttpStatus.SC_NOT_MODIFIED, entry.data,
    33                             entry.responseHeaders, true,
    34                             SystemClock.elapsedRealtime() - requestStart);
    35                 }
    36 
    37                 // Some responses such as 204s do not have content.  We must check.
    38                 if (httpResponse.getEntity() != null) {
    39                   responseContents = entityToBytes(httpResponse.getEntity());
    40                 } else {
    41                   // Add 0 byte response as a way of honestly representing a
    42                   // no-content request.
    43                   responseContents = new byte[0];
    44                 }
    45 
    46                 // if the request is slow, log it.
    47                 long requestLifetime = SystemClock.elapsedRealtime() - requestStart;
    48                 logSlowRequests(requestLifetime, request, responseContents, statusLine);
    49 
    50                 if (statusCode < 200 || statusCode > 299) {
    51                     throw new IOException();
    52                 }
    53                 return new NetworkResponse(statusCode, responseContents, responseHeaders, false,
    54                         SystemClock.elapsedRealtime() - requestStart);
    55             } catch (SocketTimeoutException e) {
    56                 attemptRetryOnException("socket", request, new TimeoutError());
    57             } catch (ConnectTimeoutException e) {
    58                 attemptRetryOnException("connection", request, new TimeoutError());
    59             } catch (MalformedURLException e) {
    60                 throw new RuntimeException("Bad URL " + request.getUrl(), e);
    61             } catch (IOException e) {
    62                 int statusCode = 0;
    63                 NetworkResponse networkResponse = null;
    64                 if (httpResponse != null) {
    65                     statusCode = httpResponse.getStatusLine().getStatusCode();
    66                 } else {
    67                     throw new NoConnectionError(e);
    68                 }
    69                 VolleyLog.e("Unexpected response code %d for %s", statusCode, request.getUrl());
    70                 if (responseContents != null) {
    71                     networkResponse = new NetworkResponse(statusCode, responseContents,
    72                             responseHeaders, false, SystemClock.elapsedRealtime() - requestStart);
    73                     if (statusCode == HttpStatus.SC_UNAUTHORIZED ||
    74                             statusCode == HttpStatus.SC_FORBIDDEN) {
    75                         attemptRetryOnException("auth",
    76                                 request, new AuthFailureError(networkResponse));
    77                     } else {
    78                         // TODO: Only throw ServerError for 5xx status codes.
    79                         throw new ServerError(networkResponse);
    80                     }
    81                 } else {
    82                     throw new NetworkError(networkResponse);
    83                 }
    84             }
    85         }

这段代码中，先10和11行代码将cache的属性设置给header，接着第9行调用mHttpStack对象的performRequest方法并传入请求对象和头部来进行请求，得到一个HttpResponse对象。

接着将HttpResponse对象中的状态吗取出，如果值为HttpStatus.SC_NOT_MODIFIED（也就是304），则表示请求得到的Response没有变化，直接显示缓存内容。

第39行表示请求成功并且获取到请求内容，将内容取出并作为一个NetworkResponse对象的属性并返回给NetworkDispatcher，接着将其转，接着就是上面介绍的回调给主线程了。

用过HttpClient的都知道，其实这里得到的HttpResponse就是由HttpClient返回的，我们直接看第12行调用的performRequest的源码：

    
    
     1 @Override
     2     public HttpResponse performRequest(Request<?> request, Map<String, String> additionalHeaders)
     3             throws IOException, AuthFailureError {
     4         HttpUriRequest httpRequest = createHttpRequest(request, additionalHeaders);
     5         addHeaders(httpRequest, additionalHeaders);
     6         addHeaders(httpRequest, request.getHeaders());
     7         onPrepareRequest(httpRequest);
     8         HttpParams httpParams = httpRequest.getParams();
     9         int timeoutMs = request.getTimeoutMs();
    10         // TODO: Reevaluate this connection timeout based on more wide-scale
    11         // data collection and possibly different for wifi vs. 3G.
    12         HttpConnectionParams.setConnectionTimeout(httpParams, 5000);
    13         HttpConnectionParams.setSoTimeout(httpParams, timeoutMs);
    14         return mClient.execute(httpRequest);
    15     }

 这里14行的mClient其实就是一个HttpClient对象。



说到这里，是不是感觉很混乱呢？这里我做了一个图，可以参考看看

![](http://images2015.cnblogs.com/blog/852227/201606/852227-20160622212817266-252965213.png)

我们这里简单再解析下：

**①
在RequestQueue创建的时候，会生成多个NetwrokDispatcher，接着这些NetwrokDispatcher会不断的从请求队列中读取请求，如果有就使用包装好的请求类来执行performRequest，接着将结果通过postResponse方法传包装好并通过post方法发送到MainLooper。**

**②
在MainLooper中，判断Response是否有内容，通过deliverResponse将结果回调给RequestQueue，RequestQueue通过我们构造时传入的Listener中的回调方法对结果进行回调。**

**③
在我们需要请求的时候，构建一个StringRequest（设置好对应的回调接口和实现回调方法）并将其add到MessageQueue中即可自动完成请求。**



我们应该掌握什么？

**① 子线程中应该如何将结果回调给主线程**

**② 如果自己要设计一个类似的框架，知道如何进行设计保证低耦合和便于维护**

**③ 通过学习其他的Request子类定义我们自己的请求类**

**④ 阅读源代码的技巧，比如查看直接子类，方法和参数定义查看等等**



