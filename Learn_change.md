#  ***Git*** 使用教程 #

|No.|Contect|
|:---:|:----------------:|
|[1](#jump_1)|Git使用环境搭建与优化|
|[2](#jump_2)|Git本地基本操作命令|
|[3](#jump_3)|Git远程库连接|
|[4](#jump_4)|Git协同代码操作|
|[5](#jump_5)|GitHub、码云、Gitlib使用|
|[6](#jump_6)|自定义Git|

***

<span id="jump_1"></span>
## ***Git*** 使用环境搭建与优化  ##

### ***Linux*** 上安装 ***Git*** ###

1. 首先，尝试输入 ***git*** ,看系统有没有安装 ***Git***

2. Ubuntu ：sudo apt-get install git

3. CentOS ：最新版本 ***Git*** 需要进行编译安装
    
    1. 安装依赖环境 
        
        - 安装 ***openssl***
            
            ```Bash
            wget http://www.openssl.org/src/5.0/perl-5.22.1.tar.gz
            mkdir -p /usr/local/webserver/openssl
            tar -zxvf openssl-1.0.2.tar.gz
            cd openssl-1.0.2
            ./configure --prefix=/usr/lcoal/webserver/openssl
            make && make install
            ```

        - 安装 ***perl***

            ```Bash
            wget http://www.cpan.org/src/5.0/perl-5.22.1.tar.gz
            mkdir -p /usr/local/webserver/perl
            tar -zxvf perl-5.22.1.tar.gz
            cd perl-5.22.1
            ./configure --perfix=/usr/local/webserver/perl
            make && make install
            ```

        - 安装 ***zlib***

            ```Bash
            wget http://nchc.dl.sourceforge.net/project/libpng/zlib/1.2.8/zlib
            mkdir -p /usr/lcoal/webserver/zlib
            zlib-1.2.8.tar.gz
            tar -xzvf zlib-1.2.8.tar.gz
            cd zlib-1.2.8
            ./configure --prefix=/usr/local/webserver/zlib
            make && make install
            ```

        - 安装 ***xmlto***

            ```Bash
            wget https://fedorahosted.org/releases/x/m/xmlto/xmlto-0.0.28.tar.gz
            mkdir -p /usr/local/webserver/xmlto
            tar -xzvf xmlto-0.0.28.tar.gz
            cd xmlto-0.0.28
            ./configure --prefix=/usr/local/webserver/xmlto
            make && make install
            ```

        - 安装 ***asciidoc***

            ```Bash
            wget http://sourceforge.net/directory/?q=asciidoc-8.6.9.tar.gz
            mkdir -p /usr/local/webserver/asciidoc
            tar -xzvf asciidoc-8.6.9.tar.gz
            cd asciidoc-8.6.9
            ./configure --prefix=/usr/local/webserver/asciidoc
            make && make install
            ```

    2. 安装 ***Git***

        ```Bash
        wget https://www.kernel.org/pub/software/scm/git/git-2.7.0.tar.gz
        mkdir -p /usr/local/webserver/git
        tar -xzvf git-2.7.0.tar.gz 
        cd git-2.7.0
        make configure
        ./configure --prefix=/usr/local/webserver/git
        make all doc
        make install install-doc install-man install-html
        ```

### ***Mac OS*** 上安装 ***Git*** ###

1. 安装homebrew，然后通过homebrew安装Git，具体方法请参考homebrew的文档：[http://brew.sh/](http://brew.sh/)。

2. 就是直接从AppStore安装Xcode，Xcode集成了Git，不过默认没有安装，你需要运行Xcode，选择菜单“Xcode”->“Preferences”，在弹出窗口中找到“Downloads”，选择“Command Line Tools”，点“Install”就可以完成安装了。

### ***Windows*** 上安装 ***Git*** ###

1. [msysgit](https://git-for-windows.github.io) 是Windows版的git 从 [https://git-for-windows.github.io](https://git-for-windows.github.io)下载。

2. 安装完成后，在开始菜单找到Git->Git Bash,并在命令行里输入

    ```Bash
    git config --global user.name "Your Name"
    git config --global user.email "email@example.com"
    ```

<span id="jump_2"></span>
## ***Git*** 本地基本操作命令  ##

###创建版本库 ###

>版本库又名仓库，英文repository,可以简单理解成一个目录。在这个目录里边的
>所有文件都可以被Git管理起来，每个文件的修改、删除，Git都能跟踪，以便任何时刻
>都可以追踪历史，或者在将来某个时刻可以还原。

1. 创建空目录

    ```Bash
    mkdir learngit
    cd learngit
    pwd
    ```

    pwd 显示该仓库位于/Users/michael/learngit

    - ** 如果使用Windows系统，为避免遇到奇怪的问题，请确保目录名（包括父目录）不包含中文。**

2. 初始化仓库
    
    ```Bash
    git init
    ```

### 把文件添加到版本库 ###

1. 用命令 ***git add*** 告诉 ***Git*** ，把文件添加到仓库：

    ```Bash
    git add readme.txt
    ```

2. 用命令 ***git commit*** 告诉 ***Git*** , 把文件提交到仓库：

    ```Bash
    git commit -m "[wrote a readme file]"
    ```

    >git commit 参数 -m 后面输入的是本次提交的说明，可以输入任意内容，
    >当然最好是有意义的，这样就能从历史记录里方便地找到改动记录。

- **commit 可以一次提交很多文件，所以可以多次add不同的文件。**

    ```Bash
    git add file1.txt
    git add file2.txt file3.txt
    git commit -m "[add 3 files]"
    ```

### 查看库状态 ###

1. 继续修改readme.txt文件后，使用 ***git status*** 查看结果

    ```Bash
    git status
    ```

    >  On branch master
    >  Changes not staged for commit:
    >    (use "git add <file>..." to update what will be committed)
    >    (use "git checkout -- <file>..." to discard changes in working directory)
    > 
    >    modified:   readme.txt
    > 
    > no changes added to commit (use "git add" and/or "git commit -a")

    - git status 命令可以让我们时刻掌握仓库当前的状态。

2. 使用 ***git diff*** 命令查看具体修改了什么内容。

    ```Bash
    git diff readme.txt
    ```

    > diff --git a/readme.txt b/readme.txt
    > index 46d49bf..9247db6 100644
    > --- a/readme.txt
    > +++ b/readme.txt
    > @@ -1,2 +1,2 @@
    > -Git is a version control system.
    > +Git is a distributed version control system.
    >  Git is free software.

    - git diff 就是查看difference，显示的格式是Unix通用的diff格式。

3.  执行git add后，在执行git commit之前，再运行git status产看当前仓库的状态。

    ```Bash
    git add readme.txt
    git status
    ```

    > On branch master
    > Changes to be committed:
    >   (use "git reset HEAD <file>..." to unstage)
    >
    >    modified:   readme.txt
    >

    - git status告诉我们，将要被提交的修改包括readme.txt。

4. 可以放心的提交了

    ```Bash
    git commit -m "[add xxxx]"
    ```

    >[master ea34578] add distributed
    > 1 file changed, 1 insertion(+), 1 deletion(-)

5. 提交后再用git status命令产看仓库当前状态

    ```Bash
    git status
    ```

    > On branch master
    >nothing to commit (working directory clean)

    - Git告诉我们当前没有需要提交的修改，而且，工作目录是干净的（working directory clean）

### 版本回退 ###

1. 每当你觉得文件修改到一定程度的时候，就可以“保存一个快照”，这个快照在Git中被称为Commit。

2. 一旦把文件改乱了，或者误删了文件，还可以从最近的一个Commit中恢复，然后继续工作。

3. 在Git可以用 git log 命令查看：

    ```Bash
    git log
    ```

    > commit 3628164fb26d48395383f8f31179f24e0882e1e0
    > Author: Michael Liao <askxuefeng@gmail.com>
    > Date:   Tue Aug 20 15:11:49 2013 +0800
    >
    >    append GPL
    >
    >commit ea34578d5496d7dd233c827ed32a8cd576c5ee85
    >Author: Michael Liao <askxuefeng@gmail.com>
    >Date:   Tue Aug 20 14:53:12 2013 +0800
    >
    >    add distributed
    >
    >commit cb926e7ea50ad11b8f9e909c05226233bf755030
    >Author: Michael Liao <askxuefeng@gmail.com>
    >Date:   Mon Aug 19 17:51:55 2013 +0800
    >
    >    wrote a readme file
    > 
    
    如果嫌输出信息太多，可以尝试添加参数--pretty=oneline

    ```Bash
    git log --pretty=oneline
    ```

    > 3628164fb26d48395383f8f31179f24e0882e1e0 append GPL
    > ea34578d5496d7dd233c827ed32a8cd576c5ee85 add distributed
    > cb926e7ea50ad11b8f9e909c05226233bf755030 wrote a readme file

    一大串类似 3628...882e1e0 的是 commit id（版本号），和SVN不一样，Gitde的commit id 不是1，2，3...递增的数字，而是一个SHA1计算出来的一个非常大的数字，用十六进制表示。因为Git是分布式的版本控制系统，如果多人在同一个版本库里工作，如果大家都用1，2，3...作为版本号，那肯定就冲突了。

    - 每提交一个新版本，Git就会把他们自动串成一条时间线。如果使用可视化工具查看，就可以清楚地查看到体骄傲历史的时间线。

        ![time_line](https://www.liaoxuefeng.com/files/attachments/001384907545599be4a60a0b5044447b47c8d8b805a25d2000/0)

4. 如果我们要把 readme.txt 回退到上一个版本，也就是“add distributed”

    > 首先，Git 必须知道当前版本是哪个版本，在Git中，用 HEAD 表示当前版本，
    > 也就是最新的提交 3628...e1e0 ，上一个版本就是 HEAD^ ，
    > 上上个版本就是 HEAD^^，当然往上100个版本，不容易表示，就可以使用HEAD~100

5. 现在，我们要把版本“append GPL”回退到上一个版本 “add distributed”,就可以使用git reset 命令。

    ```Bash
    git reset --hard HEAD^
    ```

    > HEAD is now at ea34578 add distributed
    
6. 还可以继续回退到上一个版本 wrote a readme file, 我们用git log再看看现在版本库状态

    ```Bash
    git log
    ```

    >commit ea34578d5496d7dd233c827ed32a8cd576c5ee85
    >Author: Michael Liao <askxuefeng@gmail.com>
    >Date:   Tue Aug 20 14:53:12 2013 +0800
    >
    >    add distributed
    >
    >commit cb926e7ea50ad11b8f9e909c05226233bf755030
    >Author: Michael Liao <askxuefeng@gmail.com>
    >Date:   Mon Aug 19 17:51:55 2013 +0800
    >
    >    wrote a readme file

    - 最新的版本 append GPL 已经看不到了，如何回到 append GPL?
    - 找到 append GPL 的 commit id 是 3628164 

7. 使用 git reset 命令去到未来版本。
    
    ```Bash
    git reset --hard 3628164
    ```

    > HEAD is now at 3628164 append GPL
    
    - 版本号没有必要写全，Git会自动查找匹配。

8. 但如果找不到 commit id ，还想要恢复到新版本，可以使用 git reflog 命令查找。
    
    > Git 提供了一个命令 git reflog 用来记录每一次命令
     
     ```Bash
     git reflog
     ```

    > ea34578 HEAD@{0}: reset: moving to HEAD^
    > **3628164 HEAD@{1}: commit: append GPL**
    > ea34578 HEAD@{2}: commit: add distributed
    > cb926e7 HEAD@{3}: commit (initial): wrote a readme file

    - 第二行显示 append GPL 的 commit id 是 3628164

9. Git的版本回退速度非常快。
    
    - 因为Git在内部有个指向当前版本的HEAD指针，当回退版本的时候，Git仅仅是把HEAD从指向 append GPL ：
    
        ![append_GPL](https://www.liaoxuefeng.com/files/attachments/001384907584977fc9d4b96c99f4b5f8e448fbd8589d0b2000/0)

    - 改为指向 add distributed :
    
        ![distributed](https://www.liaoxuefeng.com/files/attachments/001384907594057a873c79f14184b45a1a66b1509f90b7a000/0)

### 工作区和暂存区  ###

- 工作区（Working Directory）
    
    就是在电脑里能看到的目录，比如 learngit 文件夹就是一个工作区。

- 版本库 (Repository)

    工作区有一个隐藏目录 .git ，这个不算工作区，而是Git的版本库。

Git的版本库最重要的就是称为stage（或者叫index）的暂存区，还有Git为我们自动创建的第一个分支master，以及指向master的一个指针叫HEAD。

![Stage](https://www.liaoxuefeng.com/files/attachments/001384907702917346729e9afbf4127b6dfbae9207af016000/0)

把文件往Git版本库里添加的时候，实际上分两步执行的：

1. 用 git add 把文件添加进去，实际上就是把文件修改添加到暂存区;

2. 用 git commit 提交更改，实际上就是把暂存区的所有内容提交到当前分支。

使用git diff HEAD -- readme.txt 命令可以查看工作区和版本库最新版本的区别：

```Bash
git diff HEAD -- readme.txt
```

> diff --git a/readme.txt b/readme.txt
> index 76d770f..a9c5755 100644
> --- a/readme.txt
> +++ b/readme.txt
> @@ -1,4 +1,4 @@
>  Git is a distributed version control system.
>  Git is free software distributed under the GPL.
>  Git has a mutable index called stage.
> -Git tracks changes.
> +Git tracks changes of files.

### 撤销修改 ###

1. 只是在工作区内做了修改，或者删除文件，新建文件，没有add到暂存区。

    手动把文件恢复到上一个版本状态，如果用 git status 查看一下 ：
    
    ```Bash
    git status
    ```

    > On branch master
    > Changes not staged for commit:
    >   (use "git add <file>..." to update what will be committed)
    >   (use "git checkout -- <file>..." to discard changes in working directory)
    >
    >    modified:   readme.txt
    >

    git 会告诉你，git checkout -- file 可以丢弃工作区的修改:

    ```Bash
    git checkout -- readme.txt
    ```

    意思是把 readme.txt 文件在工作区的修改全部撤销。这里有两种情况：

    1. readme.txt自修改后还没有被放到暂存区，现在，撤销修改就回到和版本库一模一样的状态；
    
    2. readme.txt已经添加到暂存区后，又做了修改，现在撤销修改就回到添加暂存区后的状态。

2. 不仅做了修改，还 git add 到暂存区了,还未commit :
    
    ```Bash
    git status 
    ```

    > On branch master
    > Changes to be committed:
    > (use "git reset HEAD <file>..." to unstage)
    >
    > modified:   readme.txt
    > no changes added to commit (use "git add" and/or "git commit -a")
    
    使用git reset HEAD file 可以把暂存区的修改撤销掉（unstage）,重新放回工作区：

    ```Bash
    git reset HEAD readme.txt
    ```

    - git reset 命令既可以回退版本，也可以把暂存区的修改回退到工作区。当时用HEAD时，表示最新的版本。

    1. 再用git status查看一下，现在暂存区是干净的，工作区有修改：

    2. git checkout -- readme.txt，丢弃工作区的修改

### 删除文件 ###

1. 一般情况下，通常直接在文件管理器中把没用的文件删了，git会提醒工作区和版本库不一致。git status命令会提示哪些文件被删除了。

2. 确实要从版本库中删除文件，使用 git rm 删掉，并且 git commit：
    
    ```Bash
    git rm test.txt
    git commit -m "remove test.txt"
    ```

    另一种情况是删错了，可以从版本库把误删文件恢复到最新版本：

    ```Bash
    git checkout -- test.txt
    ```

<span id="jump_3"></span>
## ***Git*** 远程库连接 ##

### 远程仓库 ###

1. 创建SSH Key。
    
    >在用户主目录下，看看有没有.ssh目录，如果有，再看看这个目录下有没有id_rsa和
    >id_rsa.pub这两个文件，如果已经有了，可直接跳到下一步。如果没有，
    >打开Shell（Windows下打开Git Bash），创建SSH Key：
    
    ```Bash
    ssh-keygen -t rsa -C "youremail@example.com"
    ```

    如果一切顺利的话，可以在用户主目录里找到.ssh目录，里面有id_rsa和id_rsa.pub两个文件，这两个就是SSH Key的秘钥对，id_rsa是私钥，不能泄露出去，id_rsa.pub是公钥，可以放心地告诉任何人。

2. 登陆GitHub，打开"Account settings","SSH Keys"页面：
    
    然后，点“Add SSH Key”，填上任意Title，在Key文本框里粘贴id_rsa.pub文件的内容：

    ![Add SSH Key](https://www.liaoxuefeng.com/files/attachments/001384908342205cc1234dfe1b541ff88b90b44b30360da000/0)

### 添加远程仓库 ###

(以GitHub为例)

1. 登陆GitHub，右上角找到 "Create a new repo",创建一个新的仓库：

    ![Create a new repo](https://www.liaoxuefeng.com/files/attachments/0013849084639042e9b7d8d927140dba47c13e76fe5f0d6000/0)

    在Repository name填入learngit，其他保持默认设置，点击“Create repository”按钮，就成功地创建了一个新的Git仓库：

    ![Repository name](https://www.liaoxuefeng.com/files/attachments/0013849084720379a3eae576b9f417da2add578c8612a2e000/0)

2. 目前，在GitHub上的这个learngit仓库还是空的，GitHub告诉我们，可以从这个仓库克隆出新的仓库，也可以把一个已有的本地仓库与之关联，然后，把本地仓库的内容推送到GitHub仓库。

    ```Bash
    git remote add origin <git@github.com:michaelliao/learngit.git>
    git remote add origin git@server-name:path/repo-name.git；
    ```

    > < >内内容为自己的Git库账户名，及远程库连接。
    
3. 下一步，就可以把本地库的所有内容推送到远程库上：

    ```Bash
    git push -u origin master
    ```

    > Counting objects: 19, done.
    > Delta compression using up to 4 threads.
    > Compressing objects: 100% (19/19), done.
    > Writing objects: 100% (19/19), 13.73 KiB, done.
    > Total 23 (delta 6), reused 0 (delta 0)
    > To git@github.com:michaelliao/learngit.git
    >  * [new branch]      master -> master
    > Branch master set up to track remote branch master from origin.

    把本地库的内容推送到远程，用git push命令，实际上是把当前分支master推送到远程。

    由于远程库是空的，我们第一次推送master分支时，加上了-u参数，Git不但会把本地的master分支内容推送的远程新的master分支，还会把本地的master分支和远程的master分支关联起来，在以后的推送或者拉取时就可以简化命令。

4. 从现在起，只要本地作了提交，就可以通过命令：

    ```Bash
    git push origin master
    ```

    把本地master分支的最新修改推送至GitHub，现在，你就拥有了真正的分布式版本库！

5. SSH警告

当你第一次使用Git的clone或者push命令连接GitHub时，会得到一个警告：

>The authenticity of host 'github.com (xx.xx.xx.xx)' can't be established.
>RSA key fingerprint is xx.xx.xx.xx.xx.
>Are you sure you want to continue connecting (yes/no)?

这是因为Git使用SSH连接，而SSH连接在第一次验证GitHub服务器的Key时，需要你确认GitHub的Key的指纹信息是否真的来自GitHub的服务器，输入yes回车即可。

Git会输出一个警告，告诉你已经把GitHub的Key添加到本机的一个信任列表里了：

> Warning: Permanently added 'github.com' (RSA) to the list of known hosts.

### 从远程仓库克隆  ###

1. 创建远程库，从远程库克隆。在GitHub，创建新仓库 gitskills :

2. 我们勾选Initialize this repository with a README，这样GitHub会自动为我们创建一个README.md文件。创建完毕后，可以看到README.md文件：

3. 现在，远程库已经准备好了，下一步是用命令git clone克隆一个本地库：

```Bash
git clone git@github.com:michaelliao/gitskills.git
```

>Cloning into 'gitskills'...
>remote: Counting objects: 3, done.
>remote: Total 3 (delta 0), reused 0 (delta 0)
>Receiving objects: 100% (3/3), done.

你也许还注意到，GitHub给出的地址不止一个，还可以用https://github.com/michaelliao/gitskills.git这样的地址。实际上，Git支持多种协议，默认的git://使用ssh，但也可以使用https等其他协议。

使用https除了速度慢以外，还有个最大的麻烦是每次推送都必须输入口令，但是在某些只开放http端口的公司内部就无法使用ssh协议而只能用https。

<span id="jump_4"></span>
## ***Git*** 协同代码操作 ##

分支在实际中有什么用呢？假设你准备开发一个新功能，但是需要两周才能完成，第一周你写了50%的代码，如果立刻提交，由于代码还没写完，不完整的代码库会导致别人不能干活了。如果等代码全部写完再一次提交，又存在丢失每天进度的巨大风险。

现在有了分支，就不用怕了。你创建了一个属于你自己的分支，别人看不到，还继续在原来的分支上正常工作，而你在自己的分支上干活，想提交就提交，直到开发完毕后，再一次性合并到原来的分支上，这样，既安全，又不影响别人工作。

### 创建与合并分支  ###

>你已经知道，每次提交，Git都把它们串成一条时间线，这条时间线就是一个分支。
>截止到目前，只有一条时间线，在Git里，这个分支叫主分支，即master分支。
>HEAD严格来说不是指向提交，而是指向master，master才是指向提交的，
>所以，HEAD指向的就是当前分支。

1. 当创建新的分支，例如dev时，Git新建了一个指针叫dev,指向master相同的提交。再把HEAD指向dev，就表示当前分支在dev上：
    
    ![create dev](https://www.liaoxuefeng.com/files/attachments/001384908811773187a597e2d844eefb11f5cf5d56135ca000/0)

    Git创建一个分支很快，因为除了增加一个dev指针，改改HEAD的指向，工作区的文件都没有任何变化。

2. 对工作区的修改和提交就是针对dev分支了，新的提交dev指针向前一步，而master指针不变：

    ![commit dev](https://www.liaoxuefeng.com/files/attachments/0013849088235627813efe7649b4f008900e5365bb72323000/0)

3. 假如我们在dev上的工作完成了，就可以把dev合并到master上。直接把master指向dev的当前提交，就完成了合并:

    ![dev master](https://www.liaoxuefeng.com/files/attachments/00138490883510324231a837e5d4aee844d3e4692ba50f5000/0)

4. 合并完分支后，甚至可以删除dev分支，删除dev分支就是把dev指针给删掉。

    ![del dev](https://www.liaoxuefeng.com/files/attachments/001384908867187c83ca970bf0f46efa19badad99c40235000/0)

实际操作：

1. 创建dev分支，然后切换到dev分支

    ```Bash
    git checkout -b dev
    ```

    > switched to a new branch 'dev'

2. git checkout 命令加上-b参数表示创建并切换，相当于两条命令：

    ```Bash
    git branch dev
    git checkout dev
    ```

    > switched to branch 'dev'

3. 然后，用 git branch 命令查看当前分支：

    ```Bash
    git branch
    ```

    > * dev
    >   master

git branch 命令会列出所有分支，当前分支前面会标一个 * 号

4. 在dev分支上正常提交。

    ```Bash
    git add readme.txt
    git commit -m "branch test"
    ```

5. dev 分支的工作完成，我们就可以切换回master分支：
    
    ```Bash
    git checkout master
    ```

切换回master分支后，发现修改的内容不见了，因为哪个提交是在dev分支上,而master分支此刻的提交点并没有变：

6. 将dev分支的工作成果合并到master分支上：

    ```Bash
    git merge dev
    ```

git merge 命令用于合并指定分支到当前分支。合并后再查看，发现master跟dev分支的最新提交一致。

Fast-forword信息，Git告诉我们，这次合并是"快进模式"，也就是直接把master指向dev的当前提交。

7. 合并完成后，就可以放心地删除dev分支了。也可以不删除。

    ```Bash
    git branch -d dev
    ```

### 解决冲突 ###

但是分支合并并不是一帆风顺

1. 准备新的feature1分支，继续新分支开发：

    ```Bash
    git checkout -b feature1
    ```

    修改readme.txt最后一行

    ```TXT
    Creating a new branch is quick AND simple.
    ```

2. 在feature1分支上提交：

    ```Bash
    git add readme.txt
    git commit -m "AND simple"
    ```

    切换到master分支：

    ```Bash
    git checkout master
    ```

    > Switched to branch 'master'
    > Your branch is ahead of 'origin/master' by 1 commit.

** Git还会自动提示当前master分支比远程的master分支要超前1个提交。**

在master分支上把readme.txt文件的最后一行改为：

```TXT
    Creating a new branch is quick & simple.
```

3. 提交

    ```Bash
    git add readme.txt
    git commit -m "& simple"
    ```

4. 现在 master 分支和 feature1 分支各自都分别有新的提交

    ![master feature1](https://www.liaoxuefeng.com/files/attachments/001384909115478645b93e2b5ae4dc78da049a0d1704a41000/0)

    ** 这种情况下，Git无法执行"快速合并"，只能试图把各自的修改合并起来，但这种合并就可能会有冲突。**

    ```Bash
    git merge feature1
    ```

    >Auto-merging readme.txt
    >CONFLICT (content): Merge conflict in readme.txt
    >Automatic merge failed; fix conflicts and then commit the result.

5. Git告诉我们，readme.txt文件存在冲突，必须手动解决冲突后再提交。
    git status 也可以告诉我们冲突的文件：

    ```Bash
    git status
    ```

    > On branch master
    > Your branch is ahead of 'origin/master' by 2 commits.
    >
    > Unmerged paths:
    >   (use "git add/rm <file>..." as appropriate to mark resolution)
    >
    >       both modified:      readme.txt
    >
    > no changes added to commit (use "git add" and/or "git commit -a")

    或者直接看readme.txt的内容：

    > Git is a distributed version control system.
    > Git is free software distributed under the GPL.
    > Git has a mutable index called stage.
    > Git tracks changes of files.
    > <<<<<<< HEAD
    > Creating a new branch is quick & simple.
    > =======
    > Creating a new branch is quick AND simple.
    > >>>>>>> feature1

    ** Git用<<<<<<<，=======，>>>>>>>标记出不同分支的内容。**

6. 我们修改如下后保存：

    ```
    Creating a new branch is quick and simple.
    ```

    再提交：
    
    ```Bash
    git add readme.txt
    git commit -m "comflict fixed"
    ```

7. 现在,master分支和feature1分支变成了下图所示：

    ![master now](https://www.liaoxuefeng.com/files/attachments/00138490913052149c4b2cd9702422aa387ac024943921b000/0)

    ** 用带参数的 git log 也可以看到分支的合并情况：**

    ```Bash
    $ git log --graph --pretty=oneline --abbrev-commit
    ```

    > *   59bc1cb conflict fixed
    > |\
    > | * 75a857c AND simple
    > * | 400b400 & simple
    > |/
    > * fec145a branch test
    > ...

8. 最后删除feature1分支，也可以不删除。

    ```Bash
    git branch -d feature1
    ```

### 分支管理策略  ###

通常合并分支时，Git会用 Fast forword 模式，但这种模式下，删除分之后，会丢掉分支信息。

如果强制禁用 Fast forword 模式，Git 就会再merge时生成一个新的commit,从分支历史上就可以看出分支信息。

1. 创建并切换dev分支：
    
    ```Bash
    git checkout -b dev
    ```

2. 修改readme.txt文件，并提交一个新的commit:

    ```Bash
    git add readme.txt
    git commit -m "add merge"
    ```

3. 切换回master:

    ```Bash
    git checkout master
    ```

4. 准备合并dev分支，请注意 --no-ff 参数，表示禁用Fast forward:

    ```Bash
    git merge --no-ff -m "merge with no-ff" dev
    ```

    因为本次合并创建一个新的commit,所以加上-m参数，把commit描述写进去。

    合并后，我们用git log查看分支历史

    ```Bash
    git log --graph --pretty=oneline --abbrev-commit
    ```

    > *   7825a50 merge with no-ff
    > |\
    > | * 6224937 add merge
    > |/
    > *   59bc1cb conflict fixed
    > ...

    不使用Fast forword模式，merge后

    ![no ff](https://www.liaoxuefeng.com/files/attachments/001384909222841acf964ec9e6a4629a35a7a30588281bb000/0)

### 分支策略 ###

1. 首先，master 分支应该是非常稳定的，也就是仅用来发布新版本，平时不能再上面干活。

2. 干活都在dev分支上，dev分支是不稳定的，到某个时候，比如1.0版本发布的时候，再把dev分支合并到master上，在master分支发布1.0版本.

3. 每个人都在dev分支上干活，每个人都有自己的分支，时不时地往dev分支上合并就可以了。

    ![branch](https://www.liaoxuefeng.com/files/attachments/001384909239390d355eb07d9d64305b6322aaf4edac1e3000/0)

### BUG分支 ###

>软件开发中，每个bug都可以通过一个新的临时分支来修复，修复后，合并分支，
>然后将临时分支删除。

当你接到一个修复一个代号101的bug的任务时，很自然想创建一个分支issue-101来修复它，但当前dev上进行的工作还没有提交：
并不是不想提交，而是工作只进行到一半，还没法提交，预计完成还需1天时间，但是，必须在两小时内修复BUG,怎么办？

** Git 还提供了一个 stash 功能 **

1. 把当前工作现场"储藏"起来，等以后恢复现场后继续工作

```Bash
git stash
```

2. 首先确定要在哪个分支上修复BUG,假定需要在master分支上修复，就从master创建临时分支：

```Bash
git checkout master
git checkout -b issue-101
```

3. 修复BUG 后，提交。

```Bash
git add readme.txt
git commit -m "fix bug 101"
```

4. 切换到master分支，并完成合并，最后删除issue-01

```Bash
git checkout master
git merge --no-ff -m "merged bug fix 101" issue-101
git branch -d issue-101
```

5. 修复完BUG,是时候回dev分支干活了。

```Bash
git checkout dev
```

6. 但现在工作区是干净的，需要还原工作现场。

```Bash
git stash list
```

> stash@{0}: WIP on dev: 6224937 add merge

7. 恢复现场有两个办法：

- 用git stash apple恢复，但是恢复后，stash内容并不删除，需要用git stash drop来删除
- 另一种方式是用 git stash pop,恢复的同时把stash内容也删了

再用 git stash list 查看，就看不到任何stash内容了：

```Bash
git stash apply stash@{0}
```

### Feature分支 ###

软件开发中，总有无穷无尽的新的功能要不断添加进来。

** 添加一个新功能时，你肯定不希望因为一些实验性质的代码，把主分支搞乱了，所以，每添加一个新功能，最好新建一个feature分支，在上面开发，完成后，合并，最后，删除该feature分支。**

1. 准备开发：

    ```Bash
    git checkout -b feature-vulcan
    ```

    > Switched to a new branch 'feature-vulcan'

2. 开发完成：

    ```Bash
    $ git add vulcan.py
    $ git status
    # On branch feature-vulcan
    # Changes to be committed:
    #   (use "git reset HEAD <file>..." to unstage)
    #
    #       new file:   vulcan.py
    #
    $ git commit -m "add feature vulcan"
    [feature-vulcan 756d4af] add feature vulcan
    1 file changed, 2 insertions(+)
    create mode 100644 vulcan.py
    ```

3. 切回dev，准备合并

    ```Bash
    git checkout dev
    ```

    一切顺利的话，feature分支和bug分支是类似的，合并，然后删除。

4. 但是，突然命令新功能撤销

    ```Bash
    $ git branch -d feature-vulcan
    error: The branch 'feature-vulcan' is not fully merged.
    If you are sure you want to delete it, run 'git branch -D feature-vulcan'.
    ```

    > Git友情提醒，feature-vulcan分支还没有被合并，如果删除，将丢失掉修改，
    > 如果要强行删除，需要使用命令git branch -D feature-vulcan。

5. 强行删除

    ```Bash
    $ git branch -D feature-vulcan
    Deleted branch feature-vulcan (was 756d4af).
    ```

### 多人协作 ###

1. 产看远程仓库信息

    - 要产看远程库的信息，用git remote:
        
        ```Bash
        $ git remote
        origin
        ```

    - 或者，使用git remote -v 显示更详细的信息：
    
        ```Bash
        $ git remote -v
        origin  git@github.com:michaelliao/learngit.git (fetch)
        origin  git@github.com:michaelliao/learngit.git (push)
        ```

        ** 上面显示了可以抓取和推送的origin的地址。如果没有推送权限，就看不到push的地址。**

2. 推送分支
    
    推送分支，就是把该分支上的所有本地提交推送到远程库。推送时，要指定本地分支，这样，Git就会把该分支推送到远程库对应的远程分支上：

    ```Bash
    $ git push origin master
    ```
    
    如果要推送其他分支，比如dev，就改成：
    
    ```Bash
    $ git push origin dev
    ```

    但是，并不是一定要把本地分支往远程推送，那么，哪些分支需要推送，哪些不需要呢？

    - master分支是主分支，因此要时刻与远程同步；
        
    - dev分支是开发分支，团队所有成员都需要在上面工作，所以也需要与远程同步；
         
    - bug分支只用于在本地修复bug，就没必要推到远程了，除非老板要看看你每周到底修复了几个bug；
        
    - feature分支是否推到远程，取决于你是否和你的小伙伴合作在上面开发。

3. 抓取分支

    多人协作时，大家都会往master和dev分支上推送各自的修改。

    1. 现在，模拟一个你的小伙伴，可以在另一台电脑（注意要把SSH Key添加到GitHub）或者同一台电脑的另一个目录下克隆：

        ```Bash
        $ git clone git@github.com:michaelliao/learngit.git
        Cloning into 'learngit'...
        remote: Counting objects: 46, done.
        remote: Compressing objects: 100% (26/26), done.
        remote: Total 46 (delta 16), reused 45 (delta 15)
        Receiving objects: 100% (46/46), 15.69 KiB | 6 KiB/s, done.
        Resolving deltas: 100% (16/16), done.
        ```

        ** 当你的小伙伴从远程库clone时，默认情况下，你的小伙伴只能看到本地的master分支。**

    2. 可以用git branch命令看看：

        ```Bash
        $ git branch
        * master
        ```

    3. 现在，你的小伙伴要在dev分支上开发，就必须创建远程origin的dev分支到本地，于是他用这个命令创建本地dev分支：
    
        ```Bash
        $ git checkout -b dev origin/dev
        ```

    4. 现在，他就可以在dev上继续修改，然后，时不时地把dev分支push到远程：
    
        ```Bash
        $ git commit -m "add /usr/bin/env"
        [dev 291bea8] add /usr/bin/env
         1 file changed, 1 insertion(+)
        $ git push origin dev
        Counting objects: 5, done.
        Delta compression using up to 4 threads.
        Compressing objects: 100% (2/2), done.
        Writing objects: 100% (3/3), 349 bytes, done.
        Total 3 (delta 0), reused 0 (delta 0)
        To git@github.com:michaelliao/learngit.git
            fc38031..291bea8  dev -> dev
        ```

4. 解决冲突

    1. 你的小伙伴已经向origin/dev分支推送了他的提交，而碰巧你也对同样的文件作了修改，并试图推送：

        ```Bash
        $ git add hello.py 
        $ git commit -m "add coding: utf-8"
        [dev bd6ae48] add coding: utf-8
         1 file changed, 1 insertion(+)
        $ git push origin dev
        ```

        > To git@github.com:michaelliao/learngit.git
        >  ! [rejected]        dev -> dev (non-fast-forward)
        > error: failed to push some refs to 'git@github.com:michaelliao/learngit.git'
        > hint: Updates were rejected because the tip of your current branch is behind
        > hint: its remote counterpart. Merge the remote changes (e.g. 'git pull')
        > hint: before pushing again.
        > hint: See the 'Note about fast-forwards' in 'git push --help' for details.
        
        ** 推送失败，因为你的小伙伴的最新提交和你试图推送的提交有冲突，解决办法也很简单. **

    2. Git已经提示我们，先用git pull把最新的提交从origin/dev抓下来，然后，在本地合并，解决冲突，再推送：
        
        ```Bash
        $ git pull
        remote: Counting objects: 5, done.
        remote: Compressing objects: 100% (2/2), done.
        remote: Total 3 (delta 0), reused 3 (delta 0)
        Unpacking objects: 100% (3/3), done.
        From github.com:michaelliao/learngit
           fc38031..291bea8  dev        -> origin/dev
        There is no tracking information for the current branch.
        Please specify which branch you want to merge with.
        See git-pull(1) for details
        
            git pull <remote> <branch>
        
        If you wish to set tracking information for this branch you can do so with:
        
            git branch --set-upstream dev origin/<branch>
        ```

        ** git pull也失败了，原因是没有指定本地dev分支与远程origin/dev分支的链接. **

    3. 根据提示，设置dev和origin/dev的链接：
    
        ```Bash
        $ git branch --set-upstream dev origin/dev
        Branch dev set up to track remote branch dev from origin.
        ```

    4. 再pull：

        ```Bash
        $ git pull
        Auto-merging hello.py
        CONFLICT (content): Merge conflict in hello.py
        Automatic merge failed; fix conflicts and then commit the result.
        ```

    5. 这回git pull成功，但是合并有冲突，需要手动解决，解决的方法和分支管理中的解决冲突完全一样。解决后，提交，再push：

        ```Bash
        $ git commit -m "merge & fix hello.py"
        [dev adca45d] merge & fix hello.py
        $ git push origin dev
        Counting objects: 10, done.
        Delta compression using up to 4 threads.
        Compressing objects: 100% (5/5), done.
        Writing objects: 100% (6/6), 747 bytes, done.
        Total 6 (delta 0), reused 0 (delta 0)
        To git@github.com:michaelliao/learngit.git
           291bea8..adca45d  dev -> dev
        ```

5. 多人协作的工作模式

    1. 首先，可以试图用git push origin branch-name推送自己的修改；

    2. 如果推送失败，则因为远程分支比你的本地更新，需要先用git pull试图合并；

    3. 如果合并有冲突，则解决冲突，并在本地提交；

    4. 没有冲突或者解决掉冲突后，再用git push origin branch-name推送就能成功！

    ** 如果git pull提示“no tracking information”，则说明本地分支和远程分支的链接关系没有创建，用命令git branch --set-upstream branch-name origin/branch-name。**

### 标签管理 ###

Git的标签虽然是版本库的快照，但其实它就是指向某个commit的指针（跟分支很像对不对？但是分支可以移动，标签不能移动），所以，创建和删除标签都是瞬间完成的。

** tag就是一个让人容易记住的有意义的名字，它跟某个commit绑在一起。**

1. 创建标签
    
    1. 首先切换到需要打标签的分支上：

        ```Bash
        $ git branch
        * dev
          master
        $ git checkout master
        Switched to branch 'master'
        ```

    2. 然后，敲命令 git tag <name> 就可以打一个新标签：、
    
        ```Bash
        $ git tag v1.0
        ```

    3. 再用命令git tag查看标签：

        ```Bash
        $ git tag
        v0.9
        v1.0
        ```

        ** 注意，标签不是按时间顺序列出，而是按字母排序的。**
    
    4. 可以用git show <tagname>查看标签信息：

        ```Bash
        $ git show v0.9
        commit 622493706ab447b6bb37e4e2a2f276a20fed2ab4
        Author: Michael Liao <askxuefeng@gmail.com>
        Date:   Thu Aug 22 11:22:08 2013 +0800
        
            add merge
        ...
        ```

        可以看到，v0.9确实打在add merge这次提交上。

    5. 还可以创建带有说明的标签，用-a指定标签名，-m指定说明文字：

        ```Bash
        $ git tag -a v0.1 -m "version 0.1 released" 3628164
        ```

        用命令git show <tagname>可以看到说明文字：

        ```Bash
        $ git show v0.1
        tag v0.1
        Tagger: Michael Liao <askxuefeng@gmail.com>
        Date:   Mon Aug 26 07:28:11 2013 +0800
        
        version 0.1 released
        
        commit 3628164fb26d48395383f8f31179f24e0882e1e0
        Author: Michael Liao <askxuefeng@gmail.com>
        Date:   Tue Aug 20 15:11:49 2013 +0800
        
            append GPL
        ```

    6. 还可以通过-s用私钥签名一个标签：
    
        ```Bash
        $ git tag -s v0.2 -m "signed version 0.2 released" fec145a
        ```

        签名采用PGP签名，因此，必须首先安装gpg（GnuPG），如果没有找到gpg，或者没有gpg密钥对，就会报错：

        > gpg: signing failed: secret key not available
        > error: gpg failed to sign the data
        > error: unable to sign the tag
        
        如果报错，请参考GnuPG帮助文档配置Key。

        用命令git show <tagname>可以看到PGP签名信息：

        ```Bash
        $ git show v0.2
        tag v0.2
        Tagger: Michael Liao <askxuefeng@gmail.com>
        Date:   Mon Aug 26 07:28:33 2013 +0800
        
        signed version 0.2 released
        -----BEGIN PGP SIGNATURE-----
        Version: GnuPG v1.4.12 (Darwin)
        
        iQEcBAABAgAGBQJSGpMhAAoJEPUxHyDAhBpT4QQIAKeHfR3bo...
        -----END PGP SIGNATURE-----
        
        commit fec145accd63cdc9ed95a2f557ea0658a2a6537f
        Author: Michael Liao <askxuefeng@gmail.com>
        Date:   Thu Aug 22 10:37:30 2013 +0800
        
            branch test
        ```

        用PGP签名的标签是不可伪造的，因为可以验证PGP签名。验证签名的方法比较复杂，这里就不介绍了。

2. 操作标签
    
    1. 删除标签

        如果标签打错了，也可以删除：

        ```Bash
        $ git tag -d v0.1
        Deleted tag 'v0.1' (was e078af9)
        ```

        因为创建的标签都只存储在本地，不会自动推送到远程。所以，打错的标签可以在本地安全删除。

    2. 如果要推送某个标签到远程，使用命令git push origin <tagname>：

        ```Bash
        $ git push origin v1.0
        Total 0 (delta 0), reused 0 (delta 0)
        To git@github.com:michaelliao/learngit.git
         * [new tag]         v1.0 -> v1.0
        ```

        或者，一次性推送全部尚未推送到远程的本地标签：

        ```Bash
        $ git push origin --tags
        Counting objects: 1, done.
        Writing objects: 100% (1/1), 554 bytes, done.
        Total 1 (delta 0), reused 0 (delta 0)
        To git@github.com:michaelliao/learngit.git
         * [new tag]         v0.2 -> v0.2
         * [new tag]         v0.9 -> v0.9
        ```

    3. 如果标签已经推送到远程，要删除远程标签就麻烦一点，先从本地删除：

        ```Bash
        $ git tag -d v0.9
        Deleted tag 'v0.9' (was 6224937)
        ```

        然后，从远程删除。删除命令也是push，但是格式如下：

        ```Bash
        $ git push origin :refs/tags/v0.9
        To git@github.com:michaelliao/learngit.git
         - [deleted]         v0.9
        ```
        
        要看看是否真的从远程库删除了标签，可以登陆GitHub查看


<span id="jump_5"></span>
## ***GitHub***、码云、***Gitlib***使用 ##

### GitHub ###
### 码云 ###


<span id="jump_6"></span>
## 自定义 ***Git*** ##




参考文章：
廖雪峰Git教程： [https://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000](https://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000)