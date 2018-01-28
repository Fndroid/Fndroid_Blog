# Python--Django学习笔记1

Django：The Web framework for perfectionists with deadlines

Django是一个Python语言开发的高级Web框架，采用MVC架构，通过配置可以快速构建Web应用程序。



①安装

    
    
    pip install django

验证安装是否成功：

![](http://images2015.cnblogs.com/blog/852227/201706/852227-20170609102930543-1019738869.png)



②创建项目

选择对应存放工程的目录，在终端或cmd中使用如下命令创建一个django项目：

![](http://images2015.cnblogs.com/blog/852227/201706/852227-20170609104742137-1479053105.png)

创建成功后，在IDE中打开该项目，目录结构如下：

![](http://images2015.cnblogs.com/blog/852227/201706/852227-20170609103603168-1494228684.png)

文件对应作用：

  * __init__.py：指明该文件夹为Python中的包（package）
  * settings.py：django的配置文件
  * urls.py：django的URL声明配置文件
  * wsgi.py：兼容WSGI的服务器入口点
  * manage.py：django的启动文件



③启动项目

命令：

![](http://images2015.cnblogs.com/blog/852227/201706/852227-20170609104716309-872601509.png)

执行后会有如下提示：

![](http://images2015.cnblogs.com/blog/852227/201706/852227-20170609104942934-1408681972.png)

这里提示有13个为提交的迁移，这里的迁移暂时可用忽略，后面会详细讲到。倒数第二行提示了，服务开启在127.0.0.1:8000这个地址，现在打开浏览器进行访问测试：

![](http://images2015.cnblogs.com/blog/852227/201706/852227-20170609105224450-357080664.png)

这里看到已经访问成功了，并且得到两个提示：

  * 创建第一个app的命令为：python mannage.py startapp [app_label]
  * 项目的DEBUG被打开了并且URL暂未配置

注意：如果要设置对应的运行端口，可用通过在启动命令后加上端口号实现，如在8888端口启动服务，则命令为： **python manage.py
runserver 8888**



④创建一个app

这里的app对应的是项目中的某个模块，一个项目中可以有多个模块，而一个模块也可以属于多个项目。

创建一个blog模块，这里需要开启一个新的终端，因为刚刚的终端在运行服务器，命令如下：

![](http://images2015.cnblogs.com/blog/852227/201706/852227-20170609110048668-1845406304.png)

![](http://images2015.cnblogs.com/blog/852227/201706/852227-20170609110328653-79178715.png)

执行完成后，项目路径下多了一个blog文件夹，并且自动创建了一个db.sqlite3的文件，先说这个db.sqlite3文件，其实这是一个数据库文件，这是django默认配置的数据库，打开settings.py文件中可以找到如下定义：

![](http://images2015.cnblogs.com/blog/852227/201706/852227-20170609110230950-2011465423.png)

这部分的内容是可以改的，后面会详细介绍这部分。

接着再看blog文件夹：

  * mirgations文件夹：生成迁移数据
  * admin.py：model的注册文件
  * models.py：model层创建文件
  * tests.py：测试文件
  * views.py：view层创建文件



⑤Hello World

这里使用创建的app，进行响应，并且返回"Hello world"。

修改views.py文件，创建index方法：

    
    
    from django.http import HttpResponse
    
    
    def index(request):
        return HttpResponse("Hello world from blog index")

接着在blog文件夹下面创建一个urls.py文件，并填入如下代码：

    
    
    from django.conf.urls import url
    from . import views
    
    
    urlpatterns = [
        url(r'^$', views.index, name='index'),
    ]

这里声明了在blog这个模块中的url匹配规则，url方法的第一个参数是正则表达式，用于匹配，这里配置为空，第二个参数则是匹配成果后执行的方法，也就是分派给view。

接着在项目路径下django_blog文件夹下的urls.py中配置这个app的url匹配规则：

    
    
    from django.conf.urls import url, include
    from django.contrib import admin
    
    urlpatterns = [
        url(r'^blog/', include('blog.urls')),
        url(r'^admin/', admin.site.urls),
    ]

这里的通过include方法将模块中的urls.py中的规则引入，这里设计的思想很明显，就是方便我们对模块根路径的修改，并且使得模块之间耦合度极低，利于复用。

重启服务器，访问路径127.0.0.1:8000/blog：

![](http://images2015.cnblogs.com/blog/852227/201706/852227-20170609112325825-646155582.png)



第二篇将介绍model相关的内容。



