# JS--΢����������Ƶ�������ʵ��

����̫æ�ܾ�ûд�����ˣ����д�����©����ָ������л��

��������Ҫע�⣬��΢��������µĽ����������������������в��ԡ�

��˵���Ƶ���������Ҫ��ʲôʹ�ó�����

  * �Ż�ȯ�Ż��룬��Ҫ�û�����
  * �Ա���Ʒ����Ҫ�����Կ���

�����Ա������޷���΢���д򿪣������Ա��������Կ�������ʴ�������������վ��΢���ƹ��ʱ��Ҳ�����ɿ���û�ֻ��Ҫ���ƿ�����ֻ��Ա������ɿ�����Ӧ����Ʒ��

![](http://images2017.cnblogs.com/blog/852227/201709/852227-20170925223130104-820194837.gif)



����Ҫʹ�õ���һ���ǳ�ǿ��Ĺ���Clipboardjs���������ֻ��Ҫ�����Ӧ��js�����ɷ�����������Ĺ��ܡ�

����js�������Ұ�js�����˷�������ͨ��url������ʼ��ɣ�[����](https://github.com/zenorocha/clipboard.js)���������ض�Ӧ��js�ļ���

ҳ����룺

    
    
    <div class="test-area">
        <input id="taokouling" value="{{code}}" type="text">
        <button id="copyCode" class="btn" data-clipboard-target="#taokouling" data-clipboard-action="copy">
            �����Կ���
        </button>
        <script type="text/javascript" src="http://www.fndroid.cn/clipboard.min.js"></script>
        <script>
            var clipboard = new Clipboard('.btn');
            clipboard.on('success', function(e){
                console.log(e);
                document.getElementById('copyCode').innerHTML = '���Ƴɹ�';
            });
            clipboard.on('error', function(e){
                document.getElementById('copyCode').innerHTML = '����ʧ�ܣ��볤������';
            });
        </script>
    </div>

��Ȼ�������ܼ򵥣�����ȴ�ܼ��ݵ�ǰ�汾��iOS��Android΢������������˺ܶ�Ĵ���ŷ�����������Լ�¼һ�£���������������һ���Ͳ�������·�ˡ�

