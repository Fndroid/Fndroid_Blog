# Android����ѧϰ֮·-Git�ļ���̳̣�

Git��һ������汾�����ߣ�Ҳ�����������ǵ�һ����Ŀӵ�ж���汾���������ǿ��������������޸����ǵĴ��룬����������⣬���Ի��˵�ĳһ���ύ�㡣����㻹����һ�Ѷ�ע����������Ĵ��룬��ô���Գ���һ��Git�ˣ��Ͼ��������ѡ�

���õ�Git������ʵ���࣬����������̾����ˡ�

�� ����Git�İ�װ�Ͼ��򵥣���������Ͳ�˵�ˡ�ûǮ��mac������......

�򵥵����̣� **��ʼ��- >�޸��ļ�->������ݴ���->�ύ�޸�->����**

��װ��Ϻ���Ҫ�������û��������䣬���������Ҽ�ѡ��Git Bash Here�����һ��С�ڴ���ͼ��

![](http://images2015.cnblogs.com/blog/852227/201608/852227-20160812204904843-683040232.png)

������������������ü��ɣ�

    
    
    $ git config --global user.name "XXX"
    
    
    $ git config --global user.email "xxx@yyy.com"

�� ��ʼ����git init

�Ƚ��빤�̵�Ŀ¼�������Demo����D:\GitDemoProjectĿ¼�£���Ŀ��ʱΪ�գ�Ҳ������Studioֱ�ӽ�һ��HelloWorld��

����Ŀ��Ŀ¼�Ҽ���ѡ��Git Bash Here������ڡ�

���ǵ����������������������롣

�������룺git init Ȼ��س�������������ʾ��

    
    
    $ git init
    Initialized empty Git repository in D:/GitDemoProject/.git/

�������ǵĳ�ʼ��������ˣ���Ŀ�»���һ��.git�ļ��С�

�� ����һ���ļ�

�����ϤLinux������ֱ�������Bash��touchһ���ļ�����������ֱ���Ҽ�����һ��test.txt�ļ������룺Hello Git

�������һ�����git status����Bash�����õ�������ʾ��

    
    
    $ git status
    On branch master
    
    Initial commit
    
    Untracked files:
      (use "git add <file>..." to include in what will be committed)
    
            test.txt
    
    nothing added to commit but untracked files present (use "git add" to track)

���һ����ʾ���ǣ�û���ļ����������״̬�����Ƿ������½���δ�����ļ���

�� ������ݴ���

�����½����ļ��������ݴ���֮��ģ���Щ�ļ����ᱻGit���ƣ�Ҳ����Ŀǰ���ǵ�Gitʵ���ϻ�û�б����κι�������ļ������ݡ�������Ҫ��Git֪��������ļ�����Ҫ����ǵģ���������κ��޸ģ������ҡ�

������ݴ��������git add �ļ���

    
    
    $ git add test.txt

���ʱ���ٲ鿴һ��״̬��

    
    
    $ git status
    On branch master
    
    Initial commit
    
    Changes to be committed:
      (use "git rm --cached <file>..." to unstage)
    
            new file:   test.txt

���ǵ��ļ�״̬������Untracked�ˡ�

�� �ύ�޸�

���git commit -m "xxx"

xxxָ��������ύ����Ϣ���ü�����˵�һ�仰��������ύ�����ݿ��԰������ǿ��ٵĽ��а汾�л���

    
    
    $ git commit -m "first commit"
    [master (root-commit) 5b59dc7] first commit
     1 file changed, 1 insertion(+)
     create mode 100644 test.txt

���ʱ�����Ǿ������һ���ύ�ˡ�ĿǰΪ֮��������Ŀ�ĵ�ǰ�汾�Ѿ���Git���������ˣ�������Ҫ��ʱ����Խ��л��ˡ�

�����ٿ���״̬��

    
    
    $ git status
    On branch master
    nothing to commit, working directory clean

��ʾ����û�ж��������ύ�����ʱ��ֻҪ�����ٵ��ļ������ı䣬���״̬�Żᷢ���ı䡣

��������һ�£�

���Ǵ�test.txt�ļ����޸�һ����������Ϊ��Hello Android

�������²鿴״̬��

    
    
    $ git status
    On branch master
    Changes not staged for commit:
      (use "git add <file>..." to update what will be committed)
      (use "git checkout -- <file>..." to discard changes in working directory)
    
            modified:   test.txt
    
    no changes added to commit (use "git add" and/or "git commit -a")

�������Ǳ���֪�����ļ������˸ı䣨modified������Ҫ����������ݴ��������ύ��

�����Ȳ�Ҫ�ż����������½�һ���ļ���Ϊreadme.txt������Ҫ����κ����ݡ�

���ʱ����ִ��һ��git status�鿴״̬��

    
    
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

���Կ������½����ļ�״̬ΪUntracked�����ʱ��������Ҫ�Ȱ��������ļ�������ݴ������ſ��Խ����ύ��ֱ�ӽ��������ļ�������ݴ�����������git
add .

    
    
    $ git add .

��������ǰ�Ŀ¼�����е��ļ���������ݴ�����

���Ų鿴״̬��

    
    
    $ git status
    On branch master
    Changes to be committed:
      (use "git reset HEAD <file>..." to unstage)
    
            new file:   readme.txt
            modified:   test.txt

���ʱ��Ϳ��Խ��еڶ��ε��ύ�ˣ�����ֱ�����룺git commit���ע�ⲻ����-m "xxx"�����ڻ���ת��������ʾ�ĵط���

![](http://images2015.cnblogs.com/blog/852227/201608/852227-20160812212628656-76985971.png)

�������Ҫһ��vim��֪ʶ�ˣ�����ļ���ʵ����������ʾ�ļ��ύ�����ݵģ�������#��ͷ���ж��ᱻ���ԣ��������ǿ�����һ������д���ǵ��ύ��Ϣ�����ﲻ���Ļ�����ֱ����-
m�����档

�޸��겢����û�ص�ԭ���ĵط���

    
    
    $ git commit
    [master ea017b6] create readme.txt and modified the test.txt
     2 files changed, 1 insertion(+), 1 deletion(-)
     create mode 100644 readme.txt

�� ����

�����������ڷ��֣��ڶ��ε��ύ��������ģ�������Ҫ�ص���һ���ύ�ĵط���Ҫ��ô���أ�

�������ǿ���ʹ��git reset������������Ҫ��������һ����ʾ���˵��ֵ����������ȷ�ϻ��˵��ĵص㡣�����ֵ������ͨ��git log���鿴��

    
    
    $ git log
    commit ea017b63e4043e7750efd2eb39f39d5668b69ec4
    Author: Fndroid <1603808949@qq.com>
    Date:   Fri Aug 12 21:25:03 2016 +0800
    
        create readme.txt and modified the test.txt
    
    commit 5b59dc7a5aa1338b06f107dea6492842a5c3286d
    Author: Fndroid <1603808949@qq.com>
    Date:   Fri Aug 12 21:02:30 2016 +0800
    
        first commit

���Կ����������ύ�����Σ�ÿ�ε�commit������һ��������ʱ����ϣֵ�����ǿ��Ը��������ϣֵ���˵���һ�ε��ύ�㣺

    
    
    $ git reset --hard 5b59dc7a5aa1338b06f107dea6492842a5c3286d
    HEAD is now at 5b59dc7 first commit

���ʱ�򣬻ص������Ŀ��Ŀ¼�п��Կ��������Ǵ�����readme.txt�ļ��Ѿ������ˣ����ң�test.txt�ļ������ݱ���ԭΪHello Git

![](http://images2015.cnblogs.com/blog/852227/201608/852227-20160812213937171-1007744446.png)

�� **���˲������������˵��Ժ�������ύ�����ԣ��������**

�������һ�������Ѿ������ˣ������Ѿ���������Ŀ���������̵Ķ���汾�ˡ�

* * *



**��֧**

���������������У����ǵ���Ŀ�Ĳ�ͬ�汾�ǽ�����һ��ֱ���ϵģ�������ˣ���ô�ڻ��˵�ĺ����е��ύ���ᶪʧ��ʵ���ϻ��������������������ԣ���֧�ͳ����ˡ�

���ǿ���ͨ��git branch����鿴��ǰ���ڵķ�֧��

    
    
    $ git branch
    * master

*��ͷ�ľ������ڵķ�֧������Ĭ�ϵ������ֻ��һ��master��֧��

**��һ�������£�������Ŀ���ȶ��棬��Ӧ��������һ����֧�����������¹��ܺ�bug�޸��������´���һЩ��֧���У���������ɲ�ͨ�����Ե�ʱ���ٺ�master��֧�ϲ����ƽ����������Ľ��ǡ�**

�����ǵ�Demo�У����赱ǰ��test.txt�ļ��Ѿ����ȶ��棨��Ҫ�����ˣ���������Ҫ����һЩ�¹��ܣ������д���仰��ȥ���ѣ�����ô���ǿ�����������

�� ����һ���µķ�֧��git checkout -b ��֧��

    
    
    $ git checkout -b feature
    Switched to a new branch 'feature'

���ﴴ����һ������feature���·�֧��git�Զ��������л��������֧���ˡ�

�� ����¹���

������test.txt�ļ�����Ӽ��仰�����룩���������ɣ������ҵģ�

![](http://images2015.cnblogs.com/blog/852227/201608/852227-20160812215601484-942765511.png)

�� add��commit

�������µķ�֧�У���master��֧�Ĳ���Ҳ��һ���ģ������ύһ����θ��£�������Σ����ǿ��Խ���һЩ��

��add֮ǰ�����ǿ�ͨ��git diff����鿴��ǰ���ĸı䣺

    
    
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

ȷ������֮�󣬾Ϳ���add��commit�ˣ� **����ʹ��diff��status�����ϰ���Ǻܺõ�** ��

    
    
    $ git add test.txt
    
    
    $ git commit -m "add a new feature"
    [feature 60a91d1] add a new feature
     1 file changed, 3 insertions(+), 1 deletion(-)

�� �л���֧

�������ʱ��������鿴һ��master��֧�����ݣ�����Ҫ���з�֧�л�������ǳ��򵥣�

    
    
    $ git checkout master
    Switched to branch 'master'

���ʱ�򣬿��Բ鿴һ�����ǵ�test.txt�ļ������ݣ�

![](http://images2015.cnblogs.com/blog/852227/201608/852227-20160812220336843-1394753746.png)

���Կ������л�����֮���ļ�������Ҳ����ԭ�ˣ��ǲ��Ǻ����档

�� �޸�����֧

������裬��ͬ�¶�����֧�������޸ģ�����ļ������ݱ���Ϊ"Hello Android"�����ύ�ˡ�

    
    
    $ git add .
    
    
    $ git commit -m "modified test.txt"
    [master 9f55797] modified test.txt
     1 file changed, 1 insertion(+), 1 deletion(-)

�� �ϲ���֧

�������Ǿ������ǵ�feature��֧�޸�û����Ҳͨ���˲��ԣ���ô���ǾͿ��԰�feature��master��֧���кϲ���

�� **ע��ϲ�֮ǰҪ�Ȼص�master��֧����master��ִ֧�кϲ�������������Тۣ���ʡ���ˡ����ǡ�**

����ִ�кϲ�����git merge ��֧����

    
    
    $ git merge feature
    Auto-merging test.txt
    CONFLICT (content): Merge conflict in test.txt
    Automatic merge failed; fix conflicts and then commit the result.

���Կ�������һ����ͻ�����ˣ�CONFLICT������Ϊ���ǹ���ִ���ˢۣ�ʹ��test.txt�ļ������˸ı䡣

**������ǵ�master��֧�������½���֧feature֮��û�н����κθ��ģ�����Ͳ����г�ͻ������һ�㲻����ô���룬����Ҫ������ν�������ͻ��**

�� �����ͻ

���ʱ�����Ǵ򿪳�ͻ��Ӧ���ļ�test.txt

![](http://images2015.cnblogs.com/blog/852227/201608/852227-20160812221356125-1248060734.png)

���Կ������ݷ����仯�ˣ���ͬ��֧������ʹ��"===="�ָ��������ʱ�����Ǿ�Ҫ�ж���Ҫ��Щ�����ˣ��������Ǿ���feature��֧����������ȷ�ģ��Ϳ���ֱ��������ļ��и��ģ��Ѷ���ķ��ź��ַ�ɾ�����ɣ�

![](http://images2015.cnblogs.com/blog/852227/201608/852227-20160812221601312-1568066244.png)

���ʱ���ٽ���һ��add��commit������

    
    
    $ git add .
    
    
    $ git commit -m "fix conflict"
    [master d918a21] fix conflict

���Կ�����ͻ���޸��ˣ����������ºϲ����ɣ�

    
    
    $ git merge feature
    Already up-to-date.

�� ɾ�������֧

�������Ǻϲ���feature��֧�����ʱ��feature��֧�Ѿ��Ƕ�����ˣ����ǿ��԰�ȫ�İ���ɾ����

    
    
    $ git branch -d feature
    Deleted branch feature (was 60a91d1).

�������֧������Ҳ���������ˣ����ڼ򵥵Ĵ�������Ѿ�û�������ˡ�



ʵ���ϣ�ÿһ�������������һЩ�������÷������Ҫ�˽������صģ�����ʹ��help�������鿴add����ľ����÷���

    
    
    $ git --help add
    Launching default browser to display HTML ...

���Զ������������ʾ��Ӧ����İ����ĵ���

![](http://images2015.cnblogs.com/blog/852227/201608/852227-20160812223059671-1479545452.png)

�������Ҫ�Լ��Ķ��ˡ�



��л֧�֡�



