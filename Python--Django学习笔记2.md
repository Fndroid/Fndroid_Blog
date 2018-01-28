# Python--Django学习笔记2

本篇介绍Django中的Model层。

首先介绍sqlite3，这是在当前版本中Django默认使用的数据库，sqlite也是Android中所使用的数据库。

接着介绍最最最常见的MySQL数据库如何在Django中进行配置。



一、sqlite3

假设需求为创建两个表blog和comment，blog中有标题title和发表日期pub_date，comment中有blog的外键，评论内容content和点赞次数votes

①定义models，修改blog目录下的models.py：

    
    
    from django.db import models
    
    
    class Blog(models.Model):
        title = models.CharField(max_length=200)
        pub_date = models.DateTimeField('date published')
    
    
    class Comment(models.Model):
        blog = models.ForeignKey(Blog, on_delete=models.CASCADE)
        content = models.CharField(max_length=500)
        votes = models.IntegerField(default=0)

CharField、DateTimeField和IntegerField都是数据类型，这里还定义了一个外键，表示一个博客中可以有多个评论。

②将app加载至项目中

打开settings.py文件，找到INSTALLED_APP，将blog这个app添加进去：

![](http://images2015.cnblogs.com/blog/852227/201706/852227-20170609161109325-2133766248.png)

③创建迁移（变更）脚本

当models.py的内容也就是model被改变之后，需要创建迁移告诉django发生了什么变化，命令及运行提示如下：

![](http://images2015.cnblogs.com/blog/852227/201706/852227-20170609160020840-613044368.png)

这里表示创建了Blog和Comment两个model，并且迁移脚本代号为0001

④检查脚本内容

这个blog/migrations/0001_initial.py脚本一般情况下不需要去更改，但是django已经把它设计成可读，也就是开发者可以根据需求修改，这里不做修改。

一般③完成之后可以直接执行生成数据表的命令，但是为了确保其正确性，可用通过如下命令来让django生成sql代码进行检查：

![](http://images2015.cnblogs.com/blog/852227/201706/852227-20170609160607965-1425802353.png)

![](http://images2015.cnblogs.com/blog/852227/201706/852227-20170609160632012-226128736.png)

⑤执行迁移

当确认SQL无误后，便可以让django生成对应的数据表了，生成的命令如下：

![](http://images2015.cnblogs.com/blog/852227/201706/852227-20170609161216559-1837579762.png)

可以看到，这次的migrate提交了很多model的变更，原因是在项目创建之后，django会生成几个app（在INSTALLED_APP中可以看到），而这些app也有需要用到数据库，所以当第一次执行migrate命令，会有多个提交，可以看到我们的blog也成功提交了。

⑥通过shell访问

为了使得显示结果更为直观，先对models.py文件进行一些修改，给每个model重写__str__方法：

    
    
    from django.db import models
    
    
    class Blog(models.Model):
        title = models.CharField(max_length=200)
        pub_date = models.DateTimeField('date published')
    
        def __str__(self):
            return self.title
    
    
    class Comment(models.Model):
        blog = models.ForeignKey(Blog, on_delete=models.CASCADE)
        content = models.CharField(max_length=500)
        votes = models.IntegerField(default=0)
    
        def __str__(self):
            return self.content

接着通过下面的命令打开shell：

![](http://images2015.cnblogs.com/blog/852227/201706/852227-20170609162235872-1954521350.png)

在shell中，分别执行如下命令：

导入models

![](http://images2015.cnblogs.com/blog/852227/201706/852227-20170609162621840-689229037.png)

查询所有Blog

![](http://images2015.cnblogs.com/blog/852227/201706/852227-20170609162711231-68702340.png)

插入一个Blog

![](http://images2015.cnblogs.com/blog/852227/201706/852227-20170609162850809-1006903843.png)

插入和查询评论

![](http://images2015.cnblogs.com/blog/852227/201706/852227-20170609214610184-1751350863.png)

⑦通过工具查看数据库

sqlite数据库的文件结构很简单，这个数据库的文件其实已经存在与项目中，在项目根目录下的db.sqlite3就是对应的数据库，而很多时候为了调试方便，可用使用一些辅助工具进行操作数据库，这里推荐的是IDEA下的Database
Navigator

![](http://images2015.cnblogs.com/blog/852227/201706/852227-20170611204656090-1288902525.png)

安装完成重启IDEA后，可用在工具栏中发现多了一个叫DB Navigator的选项，可以创建一个连接：

![](http://images2015.cnblogs.com/blog/852227/201706/852227-20170611204815418-1101041478.png)

![](http://images2015.cnblogs.com/blog/852227/201706/852227-20170611205008887-1500012945.png)

完成以后，打开连接会显示一个编辑窗口，在这个窗口，可以通过执行SQL命令对数据库进行操作：

![](http://images2015.cnblogs.com/blog/852227/201706/852227-20170611205524918-421757897.png)

执行命令后会弹出result界面：

![](http://images2015.cnblogs.com/blog/852227/201706/852227-20170611205548028-122665176.png)



二、MySQL数据库配置

①修改settings.py

将DATABASE修改为对应的信息：

    
    
    DATABASES = {
        'default': {
            # 'ENGINE': 'django.db.backends.sqlite3',
            # 'NAME': os.path.join(BASE_DIR, 'db.sqlite3'),
            'ENGINE': 'django.db.backends.mysql',
            'USER': 'root',
            'PASSWORD': 'root',
            'NAME': 'django_blog',
        }
    }

修改完成后，在shell中执行一次migrate命令即可生成：

![](http://images2015.cnblogs.com/blog/852227/201706/852227-20170611210933997-656313070.png)

![](http://images2015.cnblogs.com/blog/852227/201706/852227-20170611210959543-78395913.png)



三、从数据表生成Model

很多时候，开发之前已经约定了数据库中的数据表了，那么django是如何从数据库中的表生成对应的model的呢，其实很简单。

①创建数据表：

![](http://images2015.cnblogs.com/blog/852227/201706/852227-20170611211909684-149311552.png)

②通过inspectdb命令生成对应的model：

![](http://images2015.cnblogs.com/blog/852227/201706/852227-20170611212000090-852298661.png)

可以看到，生成的代码中包含了BlogUser这个model，这个model的名字是可用自由更改的。

③存入models.py文件：

![](http://images2015.cnblogs.com/blog/852227/201706/852227-20170611212142200-1056405010.png)

此时blog/models.py文件的内容为：

    
    
    # This is an auto-generated Django model module.
    # You'll have to do the following manually to clean this up:
    #   * Rearrange models' order
    #   * Make sure each model has one field with primary_key=True
    #   * Make sure each ForeignKey has `on_delete` set to the desired behavior.
    #   * Remove `managed = False` lines if you wish to allow Django to create, modify, and delete the table
    # Feel free to rename the models, but don't rename db_table values or field names.
    from __future__ import unicode_literals
    
    from django.db import models
    
    
    class BlogUser(models.Model):
        name = models.CharField(max_length=255, blank=True, null=True)
        password = models.CharField(max_length=255, blank=True, null=True)
    
        class Meta:
            managed = False
            db_table = 'blog_user'

 ④测试

在数据表中插入数据：

![](http://images2015.cnblogs.com/blog/852227/201706/852227-20170611212407200-1468788561.png)

在shell中尝试读取：

![](http://images2015.cnblogs.com/blog/852227/201706/852227-20170611212437481-1878856799.png)

