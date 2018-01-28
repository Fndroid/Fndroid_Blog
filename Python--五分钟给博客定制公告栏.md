# Python--五分钟给博客定制公告栏

这是一个简单的flask例子

效果：

在右侧公告栏中添加一个自定义图片。

![](http://images2015.cnblogs.com/blog/852227/201707/852227-20170710153019978-1067771589.png)





事前准备：

  * 一个服务器，如阿里云
  * 一个远程连接工具，如PUTTY，如果不是windows，ssh
  * 一个远程文件传输工具，如WinSCP
  * 一张做好的公告图片



开始：

①安装Python和pip

通过远程连接工具连接到服务器。我这里用的是CentOS，所以就已经自带Python环境。安装pip也很简单：

    
    
    wget https://bootstrap.pypa.io/get-pip.py
    python get-pip.py

通过pip安装微框架Flask

    
    
    pip install flask

②编写server代码，对请求进行解析，返回图片

    
    
    cd /home // 切换到home目录
    touch server.py // 创建server.py文件
    vi server.py // 编辑器打开文件

在vi编辑器中添加如下内容：

    
    
    from flask import Response, Flask
    
    app = Flask(__name__)
    
    @app.route("/image/me")
    def index():
        image = file("img_me.jpg") # 在当前目录下寻找文件
        resp = Response(image, mimetype="image/jpeg") # 构造响应
        return resp
    
    
    app.run(host='0.0.0.0') # 设置host，否则无法被外网访问

运行该脚本：

    
    
    python server.py
     * Running on http://0.0.0.0:5000/ (Press CTRL+C to quit)

可以看到，这里使用的端口为5000

③上传图片至服务器/home目录

Windows下可以通过WinSCP进行操，直接拖拽或者复制粘贴均可。

④修改防火墙，打开5000端口

打开iptables：

    
    
    vi /etc/sysconfig/iptables

添加语句：

    
    
    -A INPUT -p tcp -m tcp --dport 5000 -j ACCEPT

应用配置：

    
    
    /etc/rc.d/init.d/iptables restart

保存配置：

    
    
    /etc/rc.d/init.d/iptables save

⑤通过浏览器访问

在浏览器中输入：xxx.xxx.xxx.xxx:5000/image/me（xxx.xxx.xxx.xxx为服务器公网IP）

若操作无误，则会看到浏览器上显示出对应的公告图

⑥对博客进行设置

进入博客，点击'管理'-'设置'，找到'博客侧边栏公告（支持HTML代码）'输入框，在输入框中添加一个<img>标签

    
    
    <img style="border: 0px" src="http://45.77.14.161:5000/image/me">

src地址填入⑤中请求的地址

⑦刷新网页即可看到效果



