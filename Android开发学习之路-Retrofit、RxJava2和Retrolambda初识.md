# Android����ѧϰ֮·-Retrofit��RxJava2��Retrolambda��ʶ

������ҹ����У����ݵ�Boss��Ҫ������ȥ������¡�

Retrofit��RxJava2���÷��Ѿ��кܶ����¿���ѧϰ�ˣ���Ȼ�ٷ����ĵ�Ҳ���ԡ����ﲻ����˵�����ʹ�á�

��ô���ĵ����ݴ���ǣ�

  * RxJava2��Retrofit��RetrolambdaЭͬ
  * CallAdapter��Converter�÷�
  * ���ģʽ��˼��

**RxJava2��Retrofit��RetrolambdaЭͬ����**

����˵һ��Retrolambda������һ���ǹٷ��Ķ���Java8��Lumbda��Android�еĽ��������ʵ����AndroidҲ��֧��Java8���������Եģ�ֻ����Ŀǰ������һ��Ĭ��֧�ֵ�״̬���������ֱ��ʹ��Java8���ᵼ��Instant
Run�޷����е����⣬��Retrolambda����һ�����еķ��������Ǽȿ���ʹ��Lumbda���ʽ��Ҳ����Ա��뻷��������⡣����������֪��Lumbda���ʽ��ȷ��һ�������Ӱ���˴���Ŀ��Ķ��ԣ������޿ɷ��ϣ�����Ҳ���Ӽ���ˡ�����Lumbda���ʽ���˽��ͬѧ������ȥ�˽�һ���ˣ��Ͼ�����ѧһЩ�����ֲ�����԰ɡ�

�����RxJava2������RxJava1.x���Ա�ȷʵ���ø��ӵ������ˣ��������ﲻ�漰���ⲿ�֣�����ȤҲ����ȥ�˽⡣

Retrofit�Ͳ��ö�˵�ˣ��ٷ��ĵ�����˵���൱�򵥵İ���

���������ã�

��Retrolumbda

Project�е�build.gradle

    
    
     1 buildscript {
     2     repositories {
     3         jcenter()
     4     }
     5     dependencies {
     6         classpath 'com.android.tools.build:gradle:2.2.1'
     7 
     8         classpath 'me.tatarka:gradle-retrolambda:3.2.3'
     9 
    10         // NOTE: Do not place your application dependencies here; they belong
    11         // in the individual module build.gradle files
    12     }
    13 }
    14 
    15 allprojects {
    16     repositories {
    17         jcenter()
    18         mavenCentral()
    19     }
    20 }

��Ҫ�Ǽ����8�е�������18�е�maven�ֿ����ӡ�

module��build.gradle

��������

    
    
    apply plugin: 'me.tatarka.retrolambda'

��android�ڵ��м����Java8�﷨֧�֣�

    
    
    android {
        ...
        compileOptions {
            sourceCompatibility JavaVersion.VERSION_1_8
            targetCompatibility JavaVersion.VERSION_1_8
        }
    }

��RxJava2��Retrofit

��������ֻ��Ҫ��module��build.gradle���������Ϳ�����

    
    
    dependencies {
        ...
        compile 'com.squareup.retrofit2:retrofit:2.1.0'
        compile 'io.reactivex.rxjava2:rxjava:2.0.0-RC5'
        compile 'io.reactivex.rxjava2:rxandroid:2.0.0-RC1'
    }



���ڼ�������Ҫͨ���ٶ�api�е����֤��ѯ��������ѯĳ�����֤����Ϣ��

GET��http://apis.baidu.com/apistore/idservice/id?id=420984198704207896

ʹ��Retrofit֮ǰ��Ҫ����һ���ӿ��������������

    
    
    public interface IdentifyService{
        @Headers("apikey:f2de5bfe3a3220a2c31b43ea831f63f5")
        @GET("id")
        public Call<ResponseBody> getPlace(@Query("id") String id);
    }

�����Headers��ָ����ͷ����Ϊ�ٶ�api�ĵ�����Ҫapikey��

���Կ���Call�еķ�����һ��ResponseBody��Ҳ��Ĭ�ϵ�һ�����������͡�

������Activity�п��Խ����첽����

    
    
            Retrofit ret = new Retrofit.Builder().baseUrl(URL).build();
            IdentifyService identifyService = ret.create(IdentifyService.class);
            Call<ResponseBody> place = identifyService.getPlace("420984198704207896");
            place.enqueue(new Callback<ResponseBody>() {
                @Override
                public void onResponse(Call<ResponseBody> call, Response<ResponseBody> response) {
                    try {
                        mTextView.setText(response.body().string());
                    } catch (IOException e) {
                        e.printStackTrace();
                    }
                }
    
                @Override
                public void onFailure(Call<ResponseBody> call, Throwable t) {
                    t.printStackTrace();
                }
            });

�����������ˣ�����Ҳ�õ��ˡ�����Ȼ�õ���������unicode��

�ܶ�ʱ�򣬷��صĽ������Json�����Կ������Retrofit�е�Converter������ת����

���ȣ�ͨ�������ķ�ʽ��Converter��Factory�����룺

    
    
        compile 'com.squareup.retrofit2:converter-gson:2.1.0'

���ţ�ͨ���õ��Ľ�����ɶ�Ӧ��Bean�ࣺ

![](http://images.cnblogs.com/OutliningIndicators/ContractedBlock.gif)![](http://images.cnblogs.com/OutliningIndicators/ExpandedBlockStart.gif)

    
    
    package com.fndroid.retrofitdemo;
    
    /**
     * Created by Administrator on 2016/10/28.
     */
    
    public class IDBean {
    
        /**
         * errNum : 0
         * retMsg : success
         * retData : {"address":"����׳�������������ƽ����","sex":"M","birthday":"1994-04-22"}
         */
    
        private int errNum;
        private String retMsg;
        /**
         * address : ����׳�������������ƽ����
         * sex : M
         * birthday : 1994-04-22
         */
    
        private RetDataBean retData;
    
        public int getErrNum() {
            return errNum;
        }
    
        public void setErrNum(int errNum) {
            this.errNum = errNum;
        }
    
        public String getRetMsg() {
            return retMsg;
        }
    
        public void setRetMsg(String retMsg) {
            this.retMsg = retMsg;
        }
    
        public RetDataBean getRetData() {
            return retData;
        }
    
        public void setRetData(RetDataBean retData) {
            this.retData = retData;
        }
    
        public static class RetDataBean {
            private String address;
            private String sex;
            private String birthday;
    
            public String getAddress() {
                return address;
            }
    
            public void setAddress(String address) {
                this.address = address;
            }
    
            public String getSex() {
                return sex;
            }
    
            public void setSex(String sex) {
                this.sex = sex;
            }
    
            public String getBirthday() {
                return birthday;
            }
    
            public void setBirthday(String birthday) {
                this.birthday = birthday;
            }
    
            @Override
            public String toString() {
                return "RetDataBean{" +
                        "address='" + address + '\'' +
                        ", sex='" + sex + '\'' +
                        ", birthday='" + birthday + '\'' +
                        '}';
            }
        }
    
        @Override
        public String toString() {
            return "IDBean{" +
                    "errNum=" + errNum +
                    ", retMsg='" + retMsg + '\'' +
                    ", retData=" + retData +
                    '}';
        }
    }

View Code

���ţ��޸Ľӿڣ�ʹ��Call�еķ���Ϊ�������ɵ�Bean���ͣ�

    
    
    public interface IdentifyService{
        @Headers("apikey:f2de5bfe3a3220a2c31b43ea831f63f5")
        @GET("id")
        public Call<IDBean> getPlace(@Query("id") String id);
    }

���ʱ����Activity�еõ��ľͲ�����ResponseBody������IDBean������Activity�еĴ����޸�Ϊ��

    
    
     1         Retrofit ret = new Retrofit.Builder().baseUrl(URL).addConverterFactory
     2                 (GsonConverterFactory.create()).build();
     3         IdentifyService identifyService = ret.create(IdentifyService.class);
     4         Call<IDBean> place = identifyService.getPlace("420984198704207896");
     5         place.enqueue(new Callback<IDBean>() {
     6             @Override
     7             public void onResponse(Call<IDBean> call, Response<IDBean> response) {
     8                 mTextView.setText(response.body().toString());
     9             }
    10 
    11             @Override
    12             public void onFailure(Call<IDBean> call, Throwable t) {
    13                 t.printStackTrace();
    14             }
    15         });

��1������build֮ǰ������addCoverterFactory����������һ��GsonConverterFactory��ʵ�������ʱ��ͨ��Call�첽����ͻ�õ���Ӧ�ڸ�Bean�Ķ���

��Щ���ܼ򵥣�������ͷ��β��û����RxJava2��

