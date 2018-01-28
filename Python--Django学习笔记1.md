# Python--Djangoѧϰ�ʼ�1

Django��The Web framework for perfectionists with deadlines

Django��һ��Python���Կ����ĸ߼�Web��ܣ�����MVC�ܹ���ͨ�����ÿ��Կ��ٹ���WebӦ�ó���



�ٰ�װ

    
    
    pip install django

��֤��װ�Ƿ�ɹ���

![](http://images2015.cnblogs.com/blog/852227/201706/852227-20170609102930543-1019738869.png)



�ڴ�����Ŀ

ѡ���Ӧ��Ź��̵�Ŀ¼�����ն˻�cmd��ʹ�����������һ��django��Ŀ��

![](http://images2015.cnblogs.com/blog/852227/201706/852227-20170609104742137-1479053105.png)

�����ɹ�����IDE�д򿪸���Ŀ��Ŀ¼�ṹ���£�

![](http://images2015.cnblogs.com/blog/852227/201706/852227-20170609103603168-1494228684.png)

�ļ���Ӧ���ã�

  * __init__.py��ָ�����ļ���ΪPython�еİ���package��
  * settings.py��django�������ļ�
  * urls.py��django��URL���������ļ�
  * wsgi.py������WSGI�ķ�������ڵ�
  * manage.py��django�������ļ�



��������Ŀ

���

![](http://images2015.cnblogs.com/blog/852227/201706/852227-20170609104716309-872601509.png)

ִ�к����������ʾ��

![](http://images2015.cnblogs.com/blog/852227/201706/852227-20170609104942934-1408681972.png)

������ʾ��13��Ϊ�ύ��Ǩ�ƣ������Ǩ����ʱ���ú��ԣ��������ϸ�����������ڶ�����ʾ�ˣ���������127.0.0.1:8000�����ַ�����ڴ���������з��ʲ��ԣ�

![](http://images2015.cnblogs.com/blog/852227/201706/852227-20170609105224450-357080664.png)

���￴���Ѿ����ʳɹ��ˣ����ҵõ�������ʾ��

  * ������һ��app������Ϊ��python mannage.py startapp [app_label]
  * ��Ŀ��DEBUG�����˲���URL��δ����

ע�⣺���Ҫ���ö�Ӧ�����ж˿ڣ�����ͨ���������������϶˿ں�ʵ�֣�����8888�˿���������������Ϊ�� **python manage.py
runserver 8888**



�ܴ���һ��app

�����app��Ӧ������Ŀ�е�ĳ��ģ�飬һ����Ŀ�п����ж��ģ�飬��һ��ģ��Ҳ�������ڶ����Ŀ��

����һ��blogģ�飬������Ҫ����һ���µ��նˣ���Ϊ�ոյ��ն������з��������������£�

![](http://images2015.cnblogs.com/blog/852227/201706/852227-20170609110048668-1845406304.png)

![](http://images2015.cnblogs.com/blog/852227/201706/852227-20170609110328653-79178715.png)

ִ����ɺ���Ŀ·���¶���һ��blog�ļ��У������Զ�������һ��db.sqlite3���ļ�����˵���db.sqlite3�ļ�����ʵ����һ�����ݿ��ļ�������djangoĬ�����õ����ݿ⣬��settings.py�ļ��п����ҵ����¶��壺

![](http://images2015.cnblogs.com/blog/852227/201706/852227-20170609110230950-2011465423.png)

�ⲿ�ֵ������ǿ��Ըĵģ��������ϸ�����ⲿ�֡�

�����ٿ�blog�ļ��У�

  * mirgations�ļ��У�����Ǩ������
  * admin.py��model��ע���ļ�
  * models.py��model�㴴���ļ�
  * tests.py�������ļ�
  * views.py��view�㴴���ļ�



��Hello World

����ʹ�ô�����app��������Ӧ�����ҷ���"Hello world"��

�޸�views.py�ļ�������index������

    
    
    from django.http import HttpResponse
    
    
    def index(request):
        return HttpResponse("Hello world from blog index")

������blog�ļ������洴��һ��urls.py�ļ������������´��룺

    
    
    from django.conf.urls import url
    from . import views
    
    
    urlpatterns = [
        url(r'^$', views.index, name='index'),
    ]

������������blog���ģ���е�urlƥ�����url�����ĵ�һ��������������ʽ������ƥ�䣬��������Ϊ�գ��ڶ�����������ƥ��ɹ���ִ�еķ�����Ҳ���Ƿ��ɸ�view��

��������Ŀ·����django_blog�ļ����µ�urls.py���������app��urlƥ�����

    
    
    from django.conf.urls import url, include
    from django.contrib import admin
    
    urlpatterns = [
        url(r'^blog/', include('blog.urls')),
        url(r'^admin/', admin.site.urls),
    ]

�����ͨ��include������ģ���е�urls.py�еĹ������룬������Ƶ�˼������ԣ����Ƿ������Ƕ�ģ���·�����޸ģ�����ʹ��ģ��֮����϶ȼ��ͣ����ڸ��á�

����������������·��127.0.0.1:8000/blog��

![](http://images2015.cnblogs.com/blog/852227/201706/852227-20170609112325825-646155582.png)



�ڶ�ƪ������model��ص����ݡ�



