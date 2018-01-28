# Android开发学习之路-GSON使用心得（OCR图片识别）

在安卓中解析JSON串可以使用的方法有很多，比如说用官方提供的JSONObject或者谷歌提供的开源库GSON，以及一些第三方开源库。

这里用的是GSON，为了测试方便，借助了一个百度的api，一个图片识别的api，向服务器请求之后，服务器会返回一个JSON串来描述图片识别的结果，我们要做的是解析这个JSON获得里面的数据，并且书出来。

这里是图片

![](http://images2015.cnblogs.com/blog/852227/201601/852227-20160106140115231-1619723086.jpg)

接下来是使用api，这里不解释，因为也不是重点，贴一下代码

    
    
    1 String httpUrl = "http://apis.baidu.com/idl_baidu/baiduocrpay/idlocrpaid";
    2 Base64 base64 = new Base64();
    3 String httpArg = URLEncoder.encode(base64
    4        .encode(getBytes("c://IMG_20151230_205532.jpg")));
    5 httpArg = "fromdevice=pc&clientip=10.10.10.0&detecttype=LocateRecognize&languagetype=CHN_ENG&imagetype=1&image="
    6          + httpArg;
    7 String jsonResult = request(httpUrl, httpArg);
    8 System.out.println(jsonResult);

返回的JSON串为

{"errNum":"0","errMsg":"success","querySign":"4053545886,2545695729","retData":[{"rect":{"left":"35","top":"18","width":"432","height":"114"},"word":"
Android"},{"rect":{"left":"31","top":"174","width":"373","height":"108"},"word":"0626"}]}

然后这里要用到GSON来解析它，使用到GSON的包为

![](http://images2015.cnblogs.com/blog/852227/201601/852227-20160106142629278-719988529.png)

接下来，分析我们获取回来的，在这里可以看到，这个串返回是一个单独的串，而不是一个数组，因此我们就要建立一个类，用来映射这个串的内容，因为这个串里面的retData是一个数组，所以在对应的类里面应该有一个List来放这个数组的数据，然后在ratDate中有一个rect里面有4个定位和大小的对应值，所以，这里要构造三个类，用于映射，如下：

    
    
     1 public class Response {
     2     private String errNum;
     3     private String errMsg;
     4     private String querySign;
     5     private List<RetData> retData;
     6 
     7     public String getErrNum() {
     8         return errNum;
     9     }
    10 
    11     public void setErrNum(String errNum) {
    12         this.errNum = errNum;
    13     }
    14 
    15     public String getErrMsg() {
    16         return errMsg;
    17     }
    18 
    19     public void setErrMsg(String errMsg) {
    20         this.errMsg = errMsg;
    21     }
    22 
    23     public String getQuerySign() {
    24         return querySign;
    25     }
    26 
    27     public void setQuerySign(String querySign) {
    28         this.querySign = querySign;
    29     }
    30 
    31     public List<RetData> getRetData() {
    32         return retData;
    33     }
    34 
    35     public void setRetData(List<RetData> retData) {
    36         this.retData = retData;
    37     }
    38 
    39 }
    
    
     1 public class RetData {
     2     private Rect rect;
     3     private String word;
     4 
     5     public Rect getRect() {
     6         return rect;
     7     }
     8 
     9     public void setRect(Rect rect) {
    10         this.rect = rect;
    11     }
    12 
    13     public String getWord() {
    14         return word;
    15     }
    16 
    17     public void setWord(String word) {
    18         this.word = word;
    19     }
    20 
    21 }
    
    
     1 public class Rect {
     2     private String left;
     3     private String top;
     4     private String width;
     5     private String height;
     6 
     7     public String getLeft() {
     8         return left;
     9     }
    10 
    11     public void setLeft(String left) {
    12         this.left = left;
    13     }
    14 
    15     public String getTop() {
    16         return top;
    17     }
    18 
    19     public void setTop(String top) {
    20         this.top = top;
    21     }
    22 
    23     public String getWidth() {
    24         return width;
    25     }
    26 
    27     public void setWidth(String width) {
    28         this.width = width;
    29     }
    30 
    31     public String getHeight() {
    32         return height;
    33     }
    34 
    35     public void setHeight(String height) {
    36         this.height = height;
    37     }
    38 
    39 }

接收类定义好了以后，可以开始用GSON来映射，方法非常简单，新建一个Gson的对象，然后调用fromJson方法让Gson完成映射

    
    
     1 Gson gson = new Gson();
     2         Response response = gson.fromJson(jsonResult, Response.class);
     3         for (int i = 0; i < response.getRetData().size(); i++) {
     4             Rect rect = response.getRetData().get(i).getRect();
     5             System.out.println("识别出字符的位置为：left=" + rect.getLeft() + " top="
     6                     + rect.getTop() + " height=" + rect.getHeight() + " width="
     7                     + rect.getWidth());
     8             System.out.println("识别出的字符为："
     9                     + response.getRetData().get(i).getWord());
    10         }

结果显示：

识别出字符的位置为：left=35 top=18 height=114 width=432  
识别出的字符为： Android  
识别出字符的位置为：left=31 top=174 height=108 width=373  
识别出的字符为：0626

