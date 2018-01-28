# JS--微信浏览器复制到剪贴板实现

由于太忙很久没写博客了，如有错误遗漏，请指出，感谢！

首先这里要注意，是微信浏览器下的解决方案，其他浏览器请自行测试。

先说复制到剪贴板主要有什么使用场景：

  * 优惠券优惠码，需要用户复制
  * 淘宝商品，需要复制淘口令

由于淘宝链接无法在微信中打开，所以淘宝开发了淘口令方便物质传播，而导购网站在微信推广的时候也是生成口令，用户只需要复制口令，打开手机淘宝，即可看到对应的商品：

![](http://images2017.cnblogs.com/blog/852227/201709/852227-20170925223130104-820194837.gif)



这里要使用的是一个非常强大的工具Clipboardjs，这个工具只需要引入对应的js，即可方便的完成上面的功能。

引入js，这里我把js放在了服务器，通过url请求访问即可，[官网](https://github.com/zenorocha/clipboard.js)方法是下载对应的js文件。

页面代码：

    
    
    <div class="test-area">
        <input id="taokouling" value="{{code}}" type="text">
        <button id="copyCode" class="btn" data-clipboard-target="#taokouling" data-clipboard-action="copy">
            复制淘口令
        </button>
        <script type="text/javascript" src="http://www.fndroid.cn/clipboard.min.js"></script>
        <script>
            var clipboard = new Clipboard('.btn');
            clipboard.on('success', function(e){
                console.log(e);
                document.getElementById('copyCode').innerHTML = '复制成功';
            });
            clipboard.on('error', function(e){
                document.getElementById('copyCode').innerHTML = '复制失败，请长按复制';
            });
        </script>
    </div>

虽然这个代码很简单，但是却能兼容当前版本的iOS和Android微信浏览器，查了很多的代码才发现这个，所以记录一下，后面的人如果需求一样就不用走弯路了。

