# Python--����Ӹ����Ͷ��ƹ�����

����һ���򵥵�flask����

Ч����

���Ҳ๫���������һ���Զ���ͼƬ��

![](http://images2015.cnblogs.com/blog/852227/201707/852227-20170710153019978-1067771589.png)





��ǰ׼����

  * һ�����������簢����
  * һ��Զ�����ӹ��ߣ���PUTTY���������windows��ssh
  * һ��Զ���ļ����乤�ߣ���WinSCP
  * һ�����õĹ���ͼƬ



��ʼ��

�ٰ�װPython��pip

ͨ��Զ�����ӹ������ӵ����������������õ���CentOS�����Ծ��Ѿ��Դ�Python��������װpipҲ�ܼ򵥣�

    
    
    wget https://bootstrap.pypa.io/get-pip.py
    python get-pip.py

ͨ��pip��װ΢���Flask

    
    
    pip install flask

�ڱ�дserver���룬��������н���������ͼƬ

    
    
    cd /home // �л���homeĿ¼
    touch server.py // ����server.py�ļ�
    vi server.py // �༭�����ļ�

��vi�༭��������������ݣ�

    
    
    from flask import Response, Flask
    
    app = Flask(__name__)
    
    @app.route("/image/me")
    def index():
        image = file("img_me.jpg") # �ڵ�ǰĿ¼��Ѱ���ļ�
        resp = Response(image, mimetype="image/jpeg") # ������Ӧ
        return resp
    
    
    app.run(host='0.0.0.0') # ����host�������޷�����������

���иýű���

    
    
    python server.py
     * Running on http://0.0.0.0:5000/ (Press CTRL+C to quit)

���Կ���������ʹ�õĶ˿�Ϊ5000

���ϴ�ͼƬ��������/homeĿ¼

Windows�¿���ͨ��WinSCP���в٣�ֱ����ק���߸���ճ�����ɡ�

���޸ķ���ǽ����5000�˿�

��iptables��

    
    
    vi /etc/sysconfig/iptables

�����䣺

    
    
    -A INPUT -p tcp -m tcp --dport 5000 -j ACCEPT

Ӧ�����ã�

    
    
    /etc/rc.d/init.d/iptables restart

�������ã�

    
    
    /etc/rc.d/init.d/iptables save

��ͨ�����������

������������룺xxx.xxx.xxx.xxx:5000/image/me��xxx.xxx.xxx.xxxΪ����������IP��

������������ῴ�����������ʾ����Ӧ�Ĺ���ͼ

�޶Բ��ͽ�������

���벩�ͣ����'����'-'����'���ҵ�'���Ͳ�������棨֧��HTML���룩'�����������������һ��<img>��ǩ

    
    
    <img style="border: 0px" src="http://45.77.14.161:5000/image/me">

src��ַ�����������ĵ�ַ

��ˢ����ҳ���ɿ���Ч��



