# Android开发学习之路-Retrofit、RxJava2和Retrolambda初识

最近在找工作中，广州的Boss需要的找我去打打杂呗。

Retrofit和RxJava2的用法已经有很多文章可以学习了，当然官方的文档也可以。这里不打算说它如何使用。

那么本文的内容大概是：

  * RxJava2、Retrofit和Retrolambda协同
  * CallAdapter和Converter用法
  * 设计模式的思考

**RxJava2、Retrofit和Retrolambda协同开发**

首先说一下Retrolambda，它是一个非官方的对于Java8的Lumbda在Android中的解决方案，实际上Android也是支持Java8的语言特性的，只不过目前还不是一个默认支持的状态。如果我们直接使用Java8，会导致Instant
Run无法运行等问题，而Retrolambda则是一个折中的方案，我们既可以使用Lumbda表达式，也不会对编译环境造成问题。但是众所周知，Lumbda表达式的确在一定情况下影响了代码的可阅读性，但是无可否认，代码也更加简洁了。对于Lumbda表达式不了解的同学，可以去了解一下了，毕竟，多学一些东西又不会亏对吧。

其次是RxJava2，它与RxJava1.x来对比确实会变得更加的完善了，但是这里不涉及到这部分，有兴趣也可以去了解。

Retrofit就不用多说了，官方文档可以说是相当简单的啊。

环境的配置：

①Retrolumbda

Project中的build.gradle

    
    
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

主要是加入第8行的依赖和18行的maven仓库连接。

module中build.gradle

加入插件：

    
    
    apply plugin: 'me.tatarka.retrolambda'

在android节点中加入对Java8语法支持：

    
    
    android {
        ...
        compileOptions {
            sourceCompatibility JavaVersion.VERSION_1_8
            targetCompatibility JavaVersion.VERSION_1_8
        }
    }

②RxJava2和Retrofit

这两个库只需要在module中build.gradle配置依赖就可以了

    
    
    dependencies {
        ...
        compile 'com.squareup.retrofit2:retrofit:2.1.0'
        compile 'io.reactivex.rxjava2:rxjava:2.0.0-RC5'
        compile 'io.reactivex.rxjava2:rxandroid:2.0.0-RC1'
    }



现在假设我们要通过百度api中的身份证查询服务来查询某个身份证的信息：

GET：http://apis.baidu.com/apistore/idservice/id?id=420984198704207896

使用Retrofit之前需要定义一个接口来描述这个请求：

    
    
    public interface IdentifyService{
        @Headers("apikey:f2de5bfe3a3220a2c31b43ea831f63f5")
        @GET("id")
        public Call<ResponseBody> getPlace(@Query("id") String id);
    }

这里的Headers是指请求头，因为百度api的调用需要apikey。

可以看到Call中的泛型是一个ResponseBody，也是默认的一个请求结果类型。

接着在Activity中可以进行异步请求：

    
    
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

到这里就完成了，请求也得到了。（虽然得到的中文是unicode）

很多时候，返回的结果都是Json，所以可以配合Retrofit中的Converter来进行转换。

首先，通过依赖的方式把Converter的Factory类引入：

    
    
        compile 'com.squareup.retrofit2:converter-gson:2.1.0'

接着，通过得到的结果生成对应的Bean类：

![](http://images.cnblogs.com/OutliningIndicators/ContractedBlock.gif)![](http://images.cnblogs.com/OutliningIndicators/ExpandedBlockStart.gif)

    
    
    package com.fndroid.retrofitdemo;
    
    /**
     * Created by Administrator on 2016/10/28.
     */
    
    public class IDBean {
    
        /**
         * errNum : 0
         * retMsg : success
         * retData : {"address":"广西壮族自治区贵港市平南县","sex":"M","birthday":"1994-04-22"}
         */
    
        private int errNum;
        private String retMsg;
        /**
         * address : 广西壮族自治区贵港市平南县
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

接着，修改接口，使得Call中的泛型为我们生成的Bean类型：

    
    
    public interface IdentifyService{
        @Headers("apikey:f2de5bfe3a3220a2c31b43ea831f63f5")
        @GET("id")
        public Call<IDBean> getPlace(@Query("id") String id);
    }

这个时候在Activity中得到的就不再是ResponseBody，而是IDBean，所以Activity中的代码修改为：

    
    
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

第1行中在build之前调用了addCoverterFactory方法传入了一个GsonConverterFactory的实例，这个时候，通过Call异步请求就会得到对应于该Bean的对象。

这些都很简单，但是由头到尾都没看到RxJava2。

