# Android开发学习之路-Git的极简教程？

Git是一个代码版本管理工具，也就是允许我们的一个项目拥有多个版本，这样我们可以随心所欲的修改我们的代码，如果出现问题，可以回退到某一个提交点。如果你还在用一堆堆注释来更新你的代码，那么可以尝试一下Git了，毕竟，它不难。

常用的Git操作其实不多，明白这个过程就行了。

※ 关于Git的安装毕竟简单，所以这里就不说了。没钱买mac，所以......

简单的流程： **初始化- >修改文件->添加至暂存区->提交修改->回退**

安装完毕后，需要先设置用户名和邮箱，在桌面中右键选择Git Bash Here，会打开一个小黑窗如图：

![](http://images2015.cnblogs.com/blog/852227/201608/852227-20160812204904843-683040232.png)

输入如下命令进行设置即可：

    
    
    $ git config --global user.name "XXX"
    
    
    $ git config --global user.email "xxx@yyy.com"

① 初始化：git init

先进入工程的目录，这里的Demo是在D:\GitDemoProject目录下，项目暂时为空，也可以用Studio直接建一个HelloWorld。

在项目根目录右键，选择Git Bash Here打开命令窗口。

我们的所有命令都是在这个窗口输入。

接着输入：git init 然后回车，会有如下提示：

    
    
    $ git init
    Initialized empty Git repository in D:/GitDemoProject/.git/

这样我们的初始化就完成了，项目下会多出一个.git文件夹。

② 创建一个文件

如果熟悉Linux，可以直接用这个Bash来touch一个文件，我们这里直接右键创建一个test.txt文件并输入：Hello Git

这里介绍一个命令：git status，在Bash输入会得到如下提示：

    
    
    $ git status
    On branch master
    
    Initial commit
    
    Untracked files:
      (use "git add <file>..." to include in what will be committed)
    
            test.txt
    
    nothing added to commit but untracked files present (use "git add" to track)

最后一行提示我们，没有文件添加至跟踪状态，但是发现了新建的未跟踪文件。

③ 添加至暂存区

上面新建的文件就是在暂存区之外的，这些文件不会被Git控制，也就是目前我们的Git实际上还没有保存任何关于这个文件的数据。我们需要让Git知道，这个文件是需要被标记的，如果它有任何修改，告诉我。

添加至暂存区的命令：git add 文件名

    
    
    $ git add test.txt

这个时候再查看一下状态：

    
    
    $ git status
    On branch master
    
    Initial commit
    
    Changes to be committed:
      (use "git rm --cached <file>..." to unstage)
    
            new file:   test.txt

我们的文件状态不再是Untracked了。

④ 提交修改

命令：git commit -m "xxx"

xxx指的是这次提交的消息，用简短明了的一句话描述这词提交的内容可以帮助我们快速的进行版本切换。

    
    
    $ git commit -m "first commit"
    [master (root-commit) 5b59dc7] first commit
     1 file changed, 1 insertion(+)
     create mode 100644 test.txt

这个时候，我们就完成了一次提交了。目前为之，整个项目的当前版本已经被Git保存起来了，在有需要的时候可以进行回退。

现在再看看状态：

    
    
    $ git status
    On branch master
    nothing to commit, working directory clean

提示我们没有东西可以提交，这个时候只要被跟踪的文件发生改变，这个状态才会发生改变。

我们来试一下！

我们打开test.txt文件并修改一下它的内容为：Hello Android

接着重新查看状态：

    
    
    $ git status
    On branch master
    Changes not staged for commit:
      (use "git add <file>..." to update what will be committed)
      (use "git checkout -- <file>..." to discard changes in working directory)
    
            modified:   test.txt
    
    no changes added to commit (use "git add" and/or "git commit -a")

现在我们被告知，有文件发生了改变（modified），需要重新添加至暂存区才能提交。

但是先不要着急，我们再新建一个文件名为readme.txt，不需要添加任何内容。

这个时候再执行一次git status查看状态：

    
    
    $ git status
    On branch master
    Changes not staged for commit:
      (use "git add <file>..." to update what will be committed)
      (use "git checkout -- <file>..." to discard changes in working directory)
    
            modified:   test.txt
    
    Untracked files:
      (use "git add <file>..." to include in what will be committed)
    
            readme.txt
    
    no changes added to commit (use "git add" and/or "git commit -a")

可以看到，新建的文件状态为Untracked，这个时候，我们需要先把这两个文件添加至暂存区，才可以进行提交，直接将这两个文件添加至暂存区的命令是git
add .

    
    
    $ git add .

这个命令是把目录下所有的文件都添加至暂存区。

接着查看状态：

    
    
    $ git status
    On branch master
    Changes to be committed:
      (use "git reset HEAD <file>..." to unstage)
    
            new file:   readme.txt
            modified:   test.txt

这个时候就可以进行第二次的提交了，我们直接输入：git commit命令，注意不输入-m "xxx"，窗口会跳转到如下所示的地方：

![](http://images2015.cnblogs.com/blog/852227/201608/852227-20160812212628656-76985971.png)

这里就需要一点vim的知识了，这个文件其实就是用来表示文件提交的内容的，所有以#开头的行都会被忽略，所以我们可以另开一行来编写我们的提交信息。这里不懂的话可以直接用-
m来代替。

修改完并保存好会回到原来的地方：

    
    
    $ git commit
    [master ea017b6] create readme.txt and modified the test.txt
     2 files changed, 1 insertion(+), 1 deletion(-)
     create mode 100644 readme.txt

⑤ 回退

假设我们现在发现，第二次的提交是有问题的，我们需要回到第一次提交的地方，要怎么做呢？

这里我们可以使用git reset命令，这个命令需要我们填入一个表示回退点的值，这样才能确认回退到的地点。而这个值，可以通过git log来查看：

    
    
    $ git log
    commit ea017b63e4043e7750efd2eb39f39d5668b69ec4
    Author: Fndroid <1603808949@qq.com>
    Date:   Fri Aug 12 21:25:03 2016 +0800
    
        create readme.txt and modified the test.txt
    
    commit 5b59dc7a5aa1338b06f107dea6492842a5c3286d
    Author: Fndroid <1603808949@qq.com>
    Date:   Fri Aug 12 21:02:30 2016 +0800
    
        first commit

可以看到，我们提交了两次，每次的commit后面有一串长长的时间点哈希值，我们可以根据这个哈希值回退到那一次的提交点：

    
    
    $ git reset --hard 5b59dc7a5aa1338b06f107dea6492842a5c3286d
    HEAD is now at 5b59dc7 first commit

这个时候，回到你的项目根目录中可以看到，我们创建的readme.txt文件已经不见了，而且，test.txt文件的内容被还原为Hello Git

![](http://images2015.cnblogs.com/blog/852227/201608/852227-20160812213937171-1007744446.png)

※ **回退操作会抛弃会退点以后的所有提交，所以，请谨慎。**

到这里，整一个流程已经走完了，我们已经可以自如的控制这个工程的多个版本了。

* * *



**分支**

在上面的这个内容中，我们的项目的不同版本是建立在一条直线上的，如果回退，那么在回退点的后所有的提交都会丢失，实际上还做不到随心所欲，所以，分支就出来了。

我们可以通过git branch命令查看当前所在的分支：

    
    
    $ git branch
    * master

*开头的就是所在的分支，这里默认的情况下只有一个master分支。

**而一般的情况下，我们项目的稳定版，都应该是在这一个分支，而其他的新功能和bug修复，可以新创建一些分支进行，当代码完成并通过测试的时候，再和master分支合并来推进整个开发的进城。**

在我们的Demo中，假设当前的test.txt文件已经是稳定版（不要惊呆了），我们需要增加一些新功能（就随便写几句话进去而已），那么我们可以这样做：

① 创建一个新的分支：git checkout -b 分支名

    
    
    $ git checkout -b feature
    Switched to a new branch 'feature'

这里创建了一个叫做feature的新分支，git自动帮我们切换到这个分支上了。

② 添加新功能

我们在test.txt文件中添加几句话（代码），这个随意吧，例如我的：

![](http://images2015.cnblogs.com/blog/852227/201608/852227-20160812215601484-942765511.png)

③ add和commit

这里在新的分支中，和master分支的操作也是一样的，我们提交一下这次更新，但是这次，我们可以谨慎一些。

在add之前，我们可通过git diff命令查看当前做的改变：

    
    
    $ git diff test.txt
    diff --git a/test.txt b/test.txt
    index e51ca0d..8612dc2 100644
    --- a/test.txt
    +++ b/test.txt
    @@ -1 +1,3 @@
    -Hello Git
    \ No newline at end of file
    +Hello Git
    +
    +I am a good new feature.
    \ No newline at end of file

确认无误之后，就可以add和commit了， **养成使用diff和status命令的习惯是很好的** 。

    
    
    $ git add test.txt
    
    
    $ git commit -m "add a new feature"
    [feature 60a91d1] add a new feature
     1 file changed, 3 insertions(+), 1 deletion(-)

③ 切换分支

假设这个时候，我们想查看一下master分支的内容，就需要进行分支切换，代码非常简单：

    
    
    $ git checkout master
    Switched to branch 'master'

这个时候，可以查看一下我们的test.txt文件的内容：

![](http://images2015.cnblogs.com/blog/852227/201608/852227-20160812220336843-1394753746.png)

可以看到，切换回来之后，文件的内容也被还原了，是不是很神奇。

④ 修改主分支

这里假设，有同事对主分支进行了修改，这个文件的内容被改为"Hello Android"并且提交了。

    
    
    $ git add .
    
    
    $ git commit -m "modified test.txt"
    [master 9f55797] modified test.txt
     1 file changed, 1 insertion(+), 1 deletion(-)

⑤ 合并分支

假设我们觉得我们的feature分支修改没问题也通过了测试，那么我们就可以把feature和master分支进行合并了

※ **注意合并之前要先回到master分支，在master分支执行合并命令，这里由于有③，就省略了。谨记。**

我们执行合并命令git merge 分支名：

    
    
    $ git merge feature
    Auto-merging test.txt
    CONFLICT (content): Merge conflict in test.txt
    Automatic merge failed; fix conflicts and then commit the result.

可以看到，有一个冲突发生了（CONFLICT），因为我们故意执行了③，使得test.txt文件发生了改变。

**如果我们的master分支在我们新建分支feature之后没有进行任何更改，这里就不会有冲突，但是一般不会这么理想，我们要介绍如何解决这个冲突。**

⑥ 解决冲突

这个时候我们打开冲突对应的文件test.txt

![](http://images2015.cnblogs.com/blog/852227/201608/852227-20160812221356125-1248060734.png)

可以看到内容发生变化了，不同分支的内容使用"===="分隔开，这个时候我们就要判断需要哪些内容了，假设我们觉得feature分支的内容是正确的，就可以直接在这个文件中更改，把多余的符号和字符删除即可：

![](http://images2015.cnblogs.com/blog/852227/201608/852227-20160812221601312-1568066244.png)

这个时候再进行一次add和commit操作：

    
    
    $ git add .
    
    
    $ git commit -m "fix conflict"
    [master d918a21] fix conflict

可以看到冲突被修复了，这里再重新合并即可：

    
    
    $ git merge feature
    Already up-to-date.

⑦ 删除多余分支

由于我们合并了feature分支，这个时候，feature分支已经是多余的了，我们可以安全的把它删除。

    
    
    $ git branch -d feature
    Deleted branch feature (was 60a91d1).

到这里，分支的内容也基本掌握了，对于简单的代码管理，已经没有问题了。



实际上，每一个命令，都有其他一些参数和用法，如果要了解更多相关的，可以使用help命令，例如查看add命令的具体用法：

    
    
    $ git --help add
    Launching default browser to display HTML ...

会自动打开浏览器并显示对应命令的帮助文档：

![](http://images2015.cnblogs.com/blog/852227/201608/852227-20160812223059671-1479545452.png)

这个就需要自己阅读了。



感谢支持。



