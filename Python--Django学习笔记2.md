# Python--Djangoѧϰ�ʼ�2

��ƪ����Django�е�Model�㡣

���Ƚ���sqlite3�������ڵ�ǰ�汾��DjangoĬ��ʹ�õ����ݿ⣬sqliteҲ��Android����ʹ�õ����ݿ⡣

���Ž������������MySQL���ݿ������Django�н������á�



һ��sqlite3

��������Ϊ����������blog��comment��blog���б���title�ͷ�������pub_date��comment����blog���������������content�͵��޴���votes

�ٶ���models���޸�blogĿ¼�µ�models.py��

    
    
    from django.db import models
    
    
    class Blog(models.Model):
        title = models.CharField(max_length=200)
        pub_date = models.DateTimeField('date published')
    
    
    class Comment(models.Model):
        blog = models.ForeignKey(Blog, on_delete=models.CASCADE)
        content = models.CharField(max_length=500)
        votes = models.IntegerField(default=0)

CharField��DateTimeField��IntegerField�����������ͣ����ﻹ������һ���������ʾһ�������п����ж�����ۡ�

�ڽ�app��������Ŀ��

��settings.py�ļ����ҵ�INSTALLED_APP����blog���app��ӽ�ȥ��

![](http://images2015.cnblogs.com/blog/852227/201706/852227-20170609161109325-2133766248.png)

�۴���Ǩ�ƣ�������ű�

��models.py������Ҳ����model���ı�֮����Ҫ����Ǩ�Ƹ���django������ʲô�仯�����������ʾ���£�

![](http://images2015.cnblogs.com/blog/852227/201706/852227-20170609160020840-613044368.png)

�����ʾ������Blog��Comment����model������Ǩ�ƽű�����Ϊ0001

�ܼ��ű�����

���blog/migrations/0001_initial.py�ű�һ������²���Ҫȥ���ģ�����django�Ѿ�������Ƴɿɶ���Ҳ���ǿ����߿��Ը��������޸ģ����ﲻ���޸ġ�

һ������֮�����ֱ��ִ���������ݱ���������Ϊ��ȷ������ȷ�ԣ�����ͨ��������������django����sql������м�飺

![](http://images2015.cnblogs.com/blog/852227/201706/852227-20170609160607965-1425802353.png)

![](http://images2015.cnblogs.com/blog/852227/201706/852227-20170609160632012-226128736.png)

��ִ��Ǩ��

��ȷ��SQL����󣬱������django���ɶ�Ӧ�����ݱ��ˣ����ɵ��������£�

![](http://images2015.cnblogs.com/blog/852227/201706/852227-20170609161216559-1837579762.png)

���Կ�������ε�migrate�ύ�˺ܶ�model�ı����ԭ��������Ŀ����֮��django�����ɼ���app����INSTALLED_APP�п��Կ�����������ЩappҲ����Ҫ�õ����ݿ⣬���Ե���һ��ִ��migrate������ж���ύ�����Կ������ǵ�blogҲ�ɹ��ύ�ˡ�

��ͨ��shell����

Ϊ��ʹ����ʾ�����Ϊֱ�ۣ��ȶ�models.py�ļ�����һЩ�޸ģ���ÿ��model��д__str__������

    
    
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

����ͨ������������shell��

![](http://images2015.cnblogs.com/blog/852227/201706/852227-20170609162235872-1954521350.png)

��shell�У��ֱ�ִ���������

����models

![](http://images2015.cnblogs.com/blog/852227/201706/852227-20170609162621840-689229037.png)

��ѯ����Blog

![](http://images2015.cnblogs.com/blog/852227/201706/852227-20170609162711231-68702340.png)

����һ��Blog

![](http://images2015.cnblogs.com/blog/852227/201706/852227-20170609162850809-1006903843.png)

����Ͳ�ѯ����

![](http://images2015.cnblogs.com/blog/852227/201706/852227-20170609214610184-1751350863.png)

��ͨ�����߲鿴���ݿ�

sqlite���ݿ���ļ��ṹ�ܼ򵥣�������ݿ���ļ���ʵ�Ѿ���������Ŀ�У�����Ŀ��Ŀ¼�µ�db.sqlite3���Ƕ�Ӧ�����ݿ⣬���ܶ�ʱ��Ϊ�˵��Է��㣬����ʹ��һЩ�������߽��в������ݿ⣬�����Ƽ�����IDEA�µ�Database
Navigator

![](http://images2015.cnblogs.com/blog/852227/201706/852227-20170611204656090-1288902525.png)

��װ�������IDEA�󣬿����ڹ������з��ֶ���һ����DB Navigator��ѡ����Դ���һ�����ӣ�

![](http://images2015.cnblogs.com/blog/852227/201706/852227-20170611204815418-1101041478.png)

![](http://images2015.cnblogs.com/blog/852227/201706/852227-20170611205008887-1500012945.png)

����Ժ󣬴����ӻ���ʾһ���༭���ڣ���������ڣ�����ͨ��ִ��SQL��������ݿ���в�����

![](http://images2015.cnblogs.com/blog/852227/201706/852227-20170611205524918-421757897.png)

ִ�������ᵯ��result���棺

![](http://images2015.cnblogs.com/blog/852227/201706/852227-20170611205548028-122665176.png)



����MySQL���ݿ�����

���޸�settings.py

��DATABASE�޸�Ϊ��Ӧ����Ϣ��

    
    
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

�޸���ɺ���shell��ִ��һ��migrate��������ɣ�

![](http://images2015.cnblogs.com/blog/852227/201706/852227-20170611210933997-656313070.png)

![](http://images2015.cnblogs.com/blog/852227/201706/852227-20170611210959543-78395913.png)



���������ݱ�����Model

�ܶ�ʱ�򣬿���֮ǰ�Ѿ�Լ�������ݿ��е����ݱ��ˣ���ôdjango����δ����ݿ��еı����ɶ�Ӧ��model���أ���ʵ�ܼ򵥡�

�ٴ������ݱ�

![](http://images2015.cnblogs.com/blog/852227/201706/852227-20170611211909684-149311552.png)

��ͨ��inspectdb�������ɶ�Ӧ��model��

![](http://images2015.cnblogs.com/blog/852227/201706/852227-20170611212000090-852298661.png)

���Կ��������ɵĴ����а�����BlogUser���model�����model�������ǿ������ɸ��ĵġ�

�۴���models.py�ļ���

![](http://images2015.cnblogs.com/blog/852227/201706/852227-20170611212142200-1056405010.png)

��ʱblog/models.py�ļ�������Ϊ��

    
    
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

 �ܲ���

�����ݱ��в������ݣ�

![](http://images2015.cnblogs.com/blog/852227/201706/852227-20170611212407200-1468788561.png)

��shell�г��Զ�ȡ��

![](http://images2015.cnblogs.com/blog/852227/201706/852227-20170611212437481-1878856799.png)

