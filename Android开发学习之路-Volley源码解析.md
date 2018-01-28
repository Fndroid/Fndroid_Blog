# Android����ѧϰ֮·-VolleyԴ�����

�Ӽ򵥵�StringRequest���ֿ���Volley�Ĺ������ơ�

�ȼ�˵��Volley���÷���

**�� ��ȡһ��RequestQueue**

    
    
    mRequestQueue = Volley.newRequestQueue( this);

**�� ����һ��StringRequest����**

    
    
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

**�� ��StringRequest����add��RequestQueue**

    
    
    mRequestQueue.add(mStringRequest);





* * *

 ����ͨ��Դ�����һ��Volley��������Ĺ��̣�

�ù�Volley��֪��������һ���Ǽ̳���Request��������࣬��ôStringRequest��ȻҲ�ǡ��ڹ��췽������Ҫ��������������Ĺ��췽������

    
    
    public StringRequest(int method, String url, Listener<String> listener,
                ErrorListener errorListener) {
            super(method, url, errorListener);
            mListener = listener;
        }

���Կ���������Ҫ����ָ������ķ�����url���ɹ����صĻص���ʹ���Ļص��ࡣ

������������Ҫ�����ʱ�򣬰�������󴫵ݸ�RequestQueue�� **add**
���������add��������ʲô��������ʵ���ܲµ������ǰ��������ŵ������С�

    
    
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

��������е㳤�����Ǻ��ĵĵط�Ҳ�Ƚϼ򵥣����ǽ����߳�ͬ��֮�������������ļ���mNetworkQueue���ڲ���Ҫ���������£��������Ҫ����������ͬ���ȴ����У��ж������Ƿ��Ѿ��������������������������

**��Ϊ���������Ƿ���Ҫ����������������水�ղ���Ҫ����������Դ�롣��StringRequest�ǻ���л���ġ�**

�������ǿ���RequestQueue����ô�����ġ�������ʵ����Ҳ�ǿ���֪������Ȼ����ΪQueue���϶��ǻ�ͨ��һ���߳�����ͣ�ı��������еĵȴ���Ȼ����д�����Handler�е�MessageQueue�Ǻ����Ƶġ�

�����ȿ�RequestQueue�Ĺ��죬����һ��ͨ������������������һ��RequestQueue

    
    
    mRequestQueue = Volley.newRequestQueue(this);

��ʵ�����������ķ�������������������

    
    
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

��ע�Ϳ��Կ�������api�ȼ�����9��ʱ��ʹ��HttpUrlConnection��������Ҫ���������������������Ѿ��������ˣ�
**Volley�ײ���ʹ��HttpUrlConnection���е�**
�����ǿ���������ʹ����һ��HttpClientStack����װ����userAgent�õ���HttpClient������������µ�Դ�����Ѿ����Ƴ��ˣ�����HttpClientʵ���Ͼ���ʹ��HttpUrlConnection��ʵ�ֵġ���󱻰�װΪһ��BasicNetwork����

���Ÿ��ݵõ���BasicNetwork�����һ��DiskBasedCache���󣨴��̻��棩������һ��RequestQueue�����ҵ���������start��������������̡߳�

�ٿ���RequestQueue��start������

    
    
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

���������Ȳ������mCacheDispatcher��ֱ�ӿ��������forѭ�������forѭ��������mDispatchers�����mDispatcher��ʵ�൱��һ���̳߳أ�����̳߳صĴ�СĬ����4��Ȼ��ֱ�����������߳��������������������ǵ�start������������ΪʲôҪ�ж���߳��������أ�ԭ��ܼ򵥣���Ϊ����ÿһ�����󶼲�һ�������ϴ�����ϣ�����߳̽���ͬʱ����Ļ�Ч�ʻ���ߡ�

���ǽ���NetworkDispatcher��������run������

    
    
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

��������������Щ�̵߳����ȼ���������ȼ��Ƚϵͣ�Ŀ����Ϊ�˾������ٶ�UI�̵߳�Ӱ�챣֤�����ȡ�

���ŵ�9�У�����mQueue��take����ȡ������ͷ��һ��������д������mQueue��ʲô����ʵ����������������add��������ӽ�ȥ��һ������

���ǲ�����Щ����״̬��ǵĵط���ֱ�ӿ�����31�У��������û�б�ȡ����Ҳ��������������£����ǻ����mNetwork��performRequest������������Ĵ�����֪���㻹�ǵ����mNetwork��������ʵ�������������ᵽ���Ǹ���HttpUrlConnection����װ�������������

�������õ��˽�������ǻῴ��54�е�����mDelivery��postResponose�������ش����ǵ���������



**���ﻹ��������Ҫ�ĵط���Ҫ���˽�һ�£�һ���Ǿ���postResponse����ô�������ǵ��������ģ���һ������performRequest����ôȥ������������ġ�**

**�ȿ���һ��������Ļش���**
�������˽������mDelivery����ô����ġ�����ʵ����RequestQueue�д����ģ����Կ���RequestQueue������һ�����췽����

    
    
    1 public RequestQueue(Cache cache, Network network, int threadPoolSize) {
    2         this(cache, network, threadPoolSize,
    3                 new ExecutorDelivery(new Handler(Looper.getMainLooper())));
    4     }

����ֱ�Ӿ�new��һ��ExecutorDelivery���󣬲�������һ�����ϴ�MainLooper�л�ȡMessage��Handler���ٿ���postResponse���������ݣ�

    
    
    1 @Override
    2     public void postResponse(Request<?> request, Response<?> response, Runnable runnable) {
    3         request.markDelivered();
    4         request.addMarker("post-response");
    5         mResponsePoster.execute(new ResponseDeliveryRunnable(request, response, runnable));
    6     }

���￴����5�е�����mResponsePoster��execute������������һ��ResponseDeliveryRunnable�����ٿ�mResponsePoster�Ķ��壺

    
    
    1 public ExecutorDelivery(final Handler handler) {
    2         // Make an Executor that just wraps the handler.
    3         mResponsePoster = new Executor() {
    4             @Override
    5             public void execute(Runnable command) {
    6                 handler.post(command);
    7             }
    8         };
    9     }

Ҳ���������������ResponseDeliveryRunnable����ͨ��Handler��post�������ͳ�ȥ�ˡ�����ΪʲôҪ���͵�MainLooper�У���ΪRequestQueue�������߳���ִ�еģ��ص����Ĵ���Ҳ�������߳��еģ�����ڻص����޸�UI���ͻᱨ�����ߣ�ΪʲôҪʹ��post������ԭ��Ҳ�ܼ򵥣���Ϊ��������Ϣ����֮���ٽ��лص���post�����������Ǵ���һ��Runnable��ʵ���ˣ�post�ɹ����Զ�ִ������run���������ʱ����run�����н��н�����жϲ��ҽ��лص���

    
    
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

���Կ�����23���ǵ���Request��deleverResponse����������ص���StringRequest�����ſ���StringRequest�и÷�����ʵ�֣�

    
    
    1 @Override
    2     protected void deliverResponse(String response) {
    3         mListener.onResponse(response);
    4     }

ֱ��ͨ�����ǹ���StringRequestʱ��������Listener�Ļص�����onResponse��������ص���Activity��deleverErrorҲ��ͬ����������



**��������performRequest����ô������������ġ�**

mNetwork��Network�ӿڵĶ��󣬶�����ӿ�ֻ��һ��ʵ���࣬����BasicNetwork�����ǿ������BasicNetwork�е�performRequest�Ĵ��룺

    
    
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

��δ����У���10��11�д��뽫cache���������ø�header�����ŵ�9�е���mHttpStack�����performRequest������������������ͷ�����������󣬵õ�һ��HttpResponse����

���Ž�HttpResponse�����е�״̬��ȡ�������ֵΪHttpStatus.SC_NOT_MODIFIED��Ҳ����304�������ʾ����õ���Responseû�б仯��ֱ����ʾ�������ݡ�

��39�б�ʾ����ɹ����һ�ȡ���������ݣ�������ȡ������Ϊһ��NetworkResponse��������Բ����ظ�NetworkDispatcher�����Ž���ת�����ž���������ܵĻص������߳��ˡ�

�ù�HttpClient�Ķ�֪������ʵ����õ���HttpResponse������HttpClient���صģ�����ֱ�ӿ���12�е��õ�performRequest��Դ�룺

    
    
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

 ����14�е�mClient��ʵ����һ��HttpClient����



˵������ǲ��Ǹо��ܻ����أ�����������һ��ͼ�����Բο�����

![](http://images2015.cnblogs.com/blog/852227/201606/852227-20160622212817266-252965213.png)

����������ٽ����£�

**��
��RequestQueue������ʱ�򣬻����ɶ��NetwrokDispatcher��������ЩNetwrokDispatcher�᲻�ϵĴ���������ж�ȡ��������о�ʹ�ð�װ�õ���������ִ��performRequest�����Ž����ͨ��postResponse��������װ�ò�ͨ��post�������͵�MainLooper��**

**��
��MainLooper�У��ж�Response�Ƿ������ݣ�ͨ��deliverResponse������ص���RequestQueue��RequestQueueͨ�����ǹ���ʱ�����Listener�еĻص������Խ�����лص���**

**��
��������Ҫ�����ʱ�򣬹���һ��StringRequest�����úö�Ӧ�Ļص��ӿں�ʵ�ֻص�������������add��MessageQueue�м����Զ��������**



����Ӧ������ʲô��

**�� ���߳���Ӧ����ν�����ص������߳�**

**�� ����Լ�Ҫ���һ�����ƵĿ�ܣ�֪����ν�����Ʊ�֤����Ϻͱ���ά��**

**�� ͨ��ѧϰ������Request���ඨ�������Լ���������**

**�� �Ķ�Դ����ļ��ɣ�����鿴ֱ�����࣬�����Ͳ�������鿴�ȵ�**



