# Android����ѧϰ֮·-GSONʹ���ĵã�OCRͼƬʶ��

�ڰ�׿�н���JSON������ʹ�õķ����кܶ࣬����˵�ùٷ��ṩ��JSONObject���߹ȸ��ṩ�Ŀ�Դ��GSON���Լ�һЩ��������Դ�⡣

�����õ���GSON��Ϊ�˲��Է��㣬������һ���ٶȵ�api��һ��ͼƬʶ���api�������������֮�󣬷������᷵��һ��JSON��������ͼƬʶ��Ľ��������Ҫ�����ǽ������JSON�����������ݣ������������

������ͼƬ

![](http://images2015.cnblogs.com/blog/852227/201601/852227-20160106140115231-1619723086.jpg)

��������ʹ��api�����ﲻ���ͣ���ΪҲ�����ص㣬��һ�´���

    
    
    1 String httpUrl = "http://apis.baidu.com/idl_baidu/baiduocrpay/idlocrpaid";
    2 Base64 base64 = new Base64();
    3 String httpArg = URLEncoder.encode(base64
    4        .encode(getBytes("c://IMG_20151230_205532.jpg")));
    5 httpArg = "fromdevice=pc&clientip=10.10.10.0&detecttype=LocateRecognize&languagetype=CHN_ENG&imagetype=1&image="
    6          + httpArg;
    7 String jsonResult = request(httpUrl, httpArg);
    8 System.out.println(jsonResult);

���ص�JSON��Ϊ

{"errNum":"0","errMsg":"success","querySign":"4053545886,2545695729","retData":[{"rect":{"left":"35","top":"18","width":"432","height":"114"},"word":"
Android"},{"rect":{"left":"31","top":"174","width":"373","height":"108"},"word":"0626"}]}

Ȼ������Ҫ�õ�GSON����������ʹ�õ�GSON�İ�Ϊ

![](http://images2015.cnblogs.com/blog/852227/201601/852227-20160106142629278-719988529.png)

���������������ǻ�ȡ�����ģ���������Կ����������������һ�������Ĵ���������һ�����飬������Ǿ�Ҫ����һ���࣬����ӳ������������ݣ���Ϊ����������retData��һ�����飬�����ڶ�Ӧ��������Ӧ����һ��List���������������ݣ�Ȼ����ratDate����һ��rect������4����λ�ʹ�С�Ķ�Ӧֵ�����ԣ�����Ҫ���������࣬����ӳ�䣬���£�

    
    
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

�����ඨ������Ժ󣬿��Կ�ʼ��GSON��ӳ�䣬�����ǳ��򵥣��½�һ��Gson�Ķ���Ȼ�����fromJson������Gson���ӳ��

    
    
     1 Gson gson = new Gson();
     2         Response response = gson.fromJson(jsonResult, Response.class);
     3         for (int i = 0; i < response.getRetData().size(); i++) {
     4             Rect rect = response.getRetData().get(i).getRect();
     5             System.out.println("ʶ����ַ���λ��Ϊ��left=" + rect.getLeft() + " top="
     6                     + rect.getTop() + " height=" + rect.getHeight() + " width="
     7                     + rect.getWidth());
     8             System.out.println("ʶ������ַ�Ϊ��"
     9                     + response.getRetData().get(i).getWord());
    10         }

�����ʾ��

ʶ����ַ���λ��Ϊ��left=35 top=18 height=114 width=432  
ʶ������ַ�Ϊ�� Android  
ʶ����ַ���λ��Ϊ��left=31 top=174 height=108 width=373  
ʶ������ַ�Ϊ��0626

