# Android开发学习之路-Android Studio开发小技巧

上一次发过了一个介绍Studio的，这里再发一个补充下。

我们都知道，Android Studio的功能是非常强大的，也是很智能的。如果有人告诉你学Android开发要用命令行，你可以告诉他Android
Studio是谷歌提供给开发者提升效率的。试想想你脑袋里有一堆想法，但是开发编码效率太低导致想法都丢失了或者兴趣消失了多可怕对不。好了废话不多说，这里介绍几个比较好用的技巧和快捷键，提升我们的编码效率。

**1.完成一句代码**

如果我们调用了一个方法，并给方法传递了参数，这个时候如果我们要结束这个语句，就会用鼠标或者方向键移动光标到行末，输入 **；**
然后回车结束。这样子其实效率是很低的，为什么？因为我们的手在键盘上，要去摸鼠标和方向键的距离都是比较远的。所以这里介绍一个组合快捷键用来完成依据代码：
**Ctrl+Shift+Enter**

![](http://images2015.cnblogs.com/blog/852227/201606/852227-20160621084800881-1072985686.gif)



这里可以看到，我们调用了方法之后，直接使用快捷键，Studio会直接跳到最后并加了一个分号，如果我们再按一下快捷键，会跳转到下一行。如果你觉得这个快捷键有点长（我是这么觉得的），可以通过自定义快捷键来简化这个功能。下面给下具体的步骤：

① 打开Settings

② 选择Keymap

③ 点击搜索框右边的按钮

![](http://images2015.cnblogs.com/blog/852227/201606/852227-20160620161603865-1061122012.png)

④ 按下你需要修改的对应快捷键，也就是 **Ctrl+Shift+Enter** ，会搜索到对应的快捷键，双击对应的快捷键设置，点击按钮之后会出现下图

![](http://images2015.cnblogs.com/blog/852227/201606/852227-20160620161843506-361661793.png)

⑤ 选择第一个选项来增加一个快捷键，会出现下图

![](http://images2015.cnblogs.com/blog/852227/201606/852227-20160620161926162-219609043.png)

可以直接在这个界面按下你想要的 **快捷键组合**
，可以看到如果已经存在的快捷键，那么下面会出现一个黄色叹号警告，我们可以多尝试，选择最方便自己的就可以了。我设置的是Alt+M，M是移动的意思，所以就记住了。



**2.打开文档**

上一次介绍Studio的通过设置让鼠标悬停在代码上会打开文档，但是默认是不会的。文档是我们学习的最重要的一个对象，在遇到问题的时候第一要想到的应该是文档。打开文档的快捷键默认是：
**Ctrl+Q** ，我们要知道，这不仅仅可以允许我们在代码中打开文档，也可以在代码补全的时候打开对应补全的文档，可以看下面这个图

![](http://images2015.cnblogs.com/blog/852227/201606/852227-20160621084941287-69157448.gif)

这里在补全的时候如果我们不知道isEmpty方法的用处，可以先将光标移动到该补全的位置，然后按下快捷键，就可以代开对应的文档。



**3.提示方法参数**

有时候我们写着写着就忘记了这个方法要传入一个什么类型的参数，这个时候，可以使用快捷键： **Ctrl+P** ，效果如下

![](http://images2015.cnblogs.com/blog/852227/201606/852227-20160621085218819-294571434.gif)

当光标在方法中的时候，按下快捷键，Studio会提示我们这个方法需要的参数，这个也是比较常用的。



**4.复制代码**

选中你的代码，然后按下快捷键： **Ctrl+D** ，总比按Ctrl+C和Ctrl+V快吧。

![](http://images2015.cnblogs.com/blog/852227/201606/852227-20160621085938115-126772981.gif)



**5.快速打开文件**

当我们需要打开项目中的某个文件的时候，可以通过快捷键 **Ctrl+Shift+N** 打开搜索框，然后输入文件名回车即可。按下快捷键会得到如下搜索框：

![](http://images2015.cnblogs.com/blog/852227/201606/852227-20160621090507240-53329641.png)



6.跳转到代码声明

快捷键： **Ctrl+B** ，这个就不多说了。



**7.重写父类方法**

快捷键： **Ctrl+O**

![](http://images2015.cnblogs.com/blog/852227/201606/852227-20160621090958928-966096659.gif)

可以看到，打开了选择框之后我们可以直接输入需要重写的方法名来进行查找，匹配上的直接回车就好。



**8.安全重命名**

好处就是不用自己手动进行每个地方的修改。只需要将光标移动到要重命名的地方，按下快捷键： **Shift+F6**
，这个时候会提示你输入一个新的名字，然后系统会进行搜索，找到所有同名的变量或者方法，然后提示给用户，界面会是下面这样：

![](http://images2015.cnblogs.com/blog/852227/201606/852227-20160621093924584-1691989008.png)

这个地方会显示所有使用了这个变量或方法的地方，如果我们需要直接重命名，可以点击左下角的 **Do Refactor**
按钮进行确认，如果有不需要重命名的地方，可以选中该行，右击，选择Exclude进行剔除。



**9.查找使用方法或类的地方**

当项目比较大的而且对Activity进行过重构的时候，或者对别的代码进行维护的时候，很多时候我们需要知道一个方法究竟在哪里被调用了，这个时候可以通过快捷键:
**Alt+F7** 来查找到所有使用了这个方法的地方。



**10.打开最近使用文件列表**

快捷键： **C trl+E**，这个也不多说，谷歌官方的演示视频会经常看到有工程师用这个快捷键。



接着介绍一些除了快捷键以外的技巧。

1.如果要判断一个字符串是不是空你会像下面这样做吗？

![](http://images2015.cnblogs.com/blog/852227/201606/852227-20160621095533537-1012578742.gif)

2.如果你要逆序遍历一个ArrayList你会像下面这样做吗？

![](http://images2015.cnblogs.com/blog/852227/201606/852227-20160621101017287-1463528935.gif)

3.如果要根据一个Json数据来写一个对应的bean，你会怎么写？这里可以使用一个比较方便的插件来做，名字叫 GSONFormat
，我们使用这个插件可以通过一段Json生成一个对应的类，怎么下载这个插件的话可以自己百度一下。

比如有这么一段Json数据：

    
    
    {
        "errNum": "0",
        "errMsg": "success",
        "querySign": "3845925467,2370020290",
        "retData": [
            {
                "rect": {
                    "left": "0",
                    "top": "0",
                    "width": "33",
                    "height": "31"
                },
                "word": "  8"
            }
        ]
    }

插件界面：

![](http://images2015.cnblogs.com/blog/852227/201606/852227-20160621123328647-1940719108.png)

使用插件可以直接生成一个对应的类代码如下：

    
    
    public class MyBean {
    
        /**
         * errNum : 0
         * errMsg : success
         * querySign : 3845925467,2370020290
         * retData : [{"rect":{"left":"0","top":"0","width":"33","height":"31"},"word":"  8"}]
         */
    
        private String errNum;
        private String errMsg;
        private String querySign;
        /**
         * rect : {"left":"0","top":"0","width":"33","height":"31"}
         * word :   8
         */
    
        private List<RetDataBean> retData;
    
        public String getErrNum() { return errNum;}
    
        public void setErrNum(String errNum) { this.errNum = errNum;}
    
        public String getErrMsg() { return errMsg;}
    
        public void setErrMsg(String errMsg) { this.errMsg = errMsg;}
    
        public String getQuerySign() { return querySign;}
    
        public void setQuerySign(String querySign) { this.querySign = querySign;}
    
        public List<RetDataBean> getRetData() { return retData;}
    
        public void setRetData(List<RetDataBean> retData) { this.retData = retData;}
    
        public static class RetDataBean {
            /**
             * left : 0
             * top : 0
             * width : 33
             * height : 31
             */
    
            private RectBean rect;
            private String word;
    
            public RectBean getRect() { return rect;}
    
            public void setRect(RectBean rect) { this.rect = rect;}
    
            public String getWord() { return word;}
    
            public void setWord(String word) { this.word = word;}
    
            public static class RectBean {
                private String left;
                private String top;
                private String width;
                private String height;
    
                public String getLeft() { return left;}
    
                public void setLeft(String left) { this.left = left;}
    
                public String getTop() { return top;}
    
                public void setTop(String top) { this.top = top;}
    
                public String getWidth() { return width;}
    
                public void setWidth(String width) { this.width = width;}
    
                public String getHeight() { return height;}
    
                public void setHeight(String height) { this.height = height;}
            }
        }
    }

这就节省了很多时间了。



好了，这次先说这么多，想到了再补充下吧。

