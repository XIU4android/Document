#  ***Git*** 使用教程

## ***Git*** 命令

>创建版本库

```Bash
git init //创建版本库
git add readme.txt //把文件添加到仓库 
git commit -m "wrote a readme file" 
//告诉Git,把文件提交到仓库， -m 命令 提示修改内容
```

>查看Git库状态

```Bash
git status //工作区的状态
git diff //随时查看修改过的内容
git log //查看提交历史纪录
git log --pretty=oneline //记录单行处理

cb926e7ea50ad11b8f9e909c05226233bf755030 wrote a readme file
//commit id
```

>版本回退

```Bash
git reset --hard commit_id //版本回退
git log //查看回退到哪个版本
git reflog //查看命令历史
```

>管理修改

```Bash
git diff HEAD -- readme.txt 
//查看工作区和版本库里面最新版本的区别
```

>撤销修改

```Bash
git checkout -- readme.txt 
//把readme.txt在工作区的修改全部撤销
git reset HEAD file 
//将缓存区的修改撤销掉，重新放回工作区
//git reset 命令既可以回退版本，也可以把暂存区的修改回退到工作区。
//使用HEAD时表示最新版本

```

>删除文件

```Bash
git rm //git中删除，并且git commit
git checkout -- test.txt
//误删除恢复
```

>远程仓库

>>setting 中添加SSH KEY ，填写title ,复制pub文件中内容到对话框中。

```Bash
//产生sshkey
ssh-keygen -t rsa -C “xxxxxx@gmail.com”
//在本地仓库运行
git remote add origin git@github.com:michaelliao/learngit.git
//michaelliao替换成自己的账户名
git remote add origin "ssh key ssh address"
git push -u origin master
//git push 命令 实际上是把当前分支Master推送到远程。
//-u 参数 不但会把本地的Master分支内容推送的远程新的master分支，
//还会把本地的Master和远程的Master分支关联起来。简化以后推送或者拉取。
//之后本地做了提交
git push origin master 
//就可以推送至github
```

>从远程库克隆

>>GitHub创建一个新仓库 gitskills
>>勾选Initialize this repository with a README

```Bash
git clone git@github.com:michaelliao/gitskills.git
//git clone 命令克隆 
//支持多种协议，包括https,ssh
```

## 分支管理

>分支在实际中有什么用呢？假设你准备开发一个新功能，但是需要两周才能完成，第一周
>你写了50%的代码，如果立刻提交，由于代码还没写完，
>不完整的代码库会导致别人不能干
>活了。如果等代码全部写完再一次提交，又存在丢失每天进度的巨大风险。
>现在有了分支，就不用怕了。你创建了一个属于你自己的分支，别人看不到，还继续在原
>来的分支上正常工作，而你在自己的分支上干活，想提交就提交，直到开发完毕后，再一
>次性合并到原来的分支上，这样，既安全，又不影响别人工作

>创建分支与合并

>>在Git里，主分支即Master分支。HEAD严格来说不是指向提交，而是指向master,
>>master才是指向提交的，所以，HEAD指向的就是当前分支。


![Master](https://www.liaoxuefeng.com/files/attachments/0013849087937492135fbf4bbd24dfcbc18349a8a59d36d000/0)

>>创建dev分支，然后切换到dev分支

```Bash
git checkout -b dev
```

>>git checkout 命令加上-b参数表示创建并切换，相当于以下两条命令：

```Bash
git branch
```

>>git branch 命令会列出所有分支，当前分支前面会标一个*号。

>>在dev分支上正常提交

```Bash
git add readme.txt
git commit -m "branch test"
```

>>现在，dev分支的工作完成，我们可以切换回master分支
>>此刻master分支提交点并没有改变

```Bash
git checkout master
```

>>把dev分支的工作成果合并到master分支上：

```Bash
git merge dev
```

>>git merge命令用于合并指定分支到当前分支。

>>合并完成后，就可以放心地删除dev分支了

```Bash
git branch -d dev
git branch
```

>>删除后，查看branch,就只剩下master分支了。

```Bash
//查看分支
git branch
//创建分支
git branch <name>
//切换分支
git checkout <name>
//创建+切换分支
git checkout -b <name>
//合并某分支到当前分支
git merge <name>
//删除分支
git branch -d <name>
```

>解决冲突

>>现在，master分支和feature1分支各自都分别有新的提交，变成了这样：

![feature1](https://www.liaoxuefeng.com/files/attachments/001384909115478645b93e2b5ae4dc78da049a0d1704a41000/0)

>>在这种情况下，Git无法执行快速合并，只能试图把各自修改合并起来，
>>但这种合并就可能会有冲突

```Bash
git merge feature1
git status
//会告诉我们冲突的文件
//Git 会标记处不同分支的内容 修改后保存
git log --graph --pretty=oneline --abbrev-commit
//带参数git log --graph 也可以看到分支的合并情况。
git branch -d feature1
//删除feature1分支
```

>>分支管理策略

>>通常合并分支时，如果可能，Git会用Fast forward模式，
>>但这种模式下，删除分之后，会丢掉分支信息。

>>如果强制禁用Fast Forward模式，Git就会在merge时生成一个新的commit.

```Bash
//首先创建并切换Dev分支
git checkout -b dev 
//修改readme.txt 并提交一个新的commit
git add readme.txt
git commit -m "add merge"
//切换回master
git checkout master
//准备合并dev分支，--no-ff表示禁用Fast forward
git merge --no-ff -m "merge with no-ff" dev
//合并后，使用git log 查看分支历史
git log --graph --pretty=oneline --abbrev-commit
```

>>分支策略
>>实际开发中，应该按照几个基本原则进行分支管理

>>1. master分支应该是非常稳定的，也就是仅用来发布新版本。
>>2. 干活都在dev分支上，也就是说dev分支是不稳定的。到某个时候，比如1.0发布时，
>>再把dev分支，合并到master上。
>>3. 每个人都有自己的分支，时不时地往dev分支上合并就可以了。

![branch](https://www.liaoxuefeng.com/files/attachments/001384909239390d355eb07d9d64305b6322aaf4edac1e3000/0)

>>BUG分支

>>每个BUG都可以通过一个新的临时分支来修复，修复后，
>>合并分支，然后将临时分支删除。但有时候工作只进行到一半，没法提交，
>>但有BUG需要在两个小时内修复。
>>Git提供stash功能，把工作现场“储藏”起来，等以后恢复现场后工作：

```Bash
git stash
//之后用git status查看工作区，可以放心创建分支来修复BUG。
//首先确定要在哪个分支上修复BUG，假如需要在master分支上修复，就从master创建分支
git checkout master
gti checkout -b issue-101
//修复BUG
git add readme.txt
git commit -m "fix bug 101"
//修复完成后，切换到master分支，并完成合并。删除issue-101分支：
git checkout master
git merge --no-ff -m "recursive" strategy.
git branch -d issue-101
//完成修复，回dev分支干活
git checkout dev
git status
git stash list
//工作现场还在，但需要恢复一下
git stash apply
git stash drop 
//使用apply 恢复后需要 drop 删除恢复区内内容
git stash pop
//恢复的同时把stash内容也删除
git stash list
//使用 list 查看就看不到任何内容了。
//可以多次stash 恢复的时候先用 git stash list 查看。
//然后恢复指定的stash
git stash apply stash@{0}
```

>Feature分支

>>每添加一个新功能，最好新建一个个feature分支，开发完成合并删除。

```Bash
git checkout -b feature-vulcan
git add vulcan.py
git status
git commit -m "add feature vulcan"
//切回DEV
git checkout dev
//新功能取消，分支就地销毁
git branch -D feature-vulcan
//强制删除 -D 丢弃一个没有被合并过的分支
```

>多人协作

>>当从远程仓库克隆时，实际上Git自动把本地的Master分支和
>>远程的Master分支对应起来了,并且远程仓库的默认名称是origin.

```Bash
//查看远程库的信息
git remote
//-v 参数显示更详细的信息
git remote -v
//如果没有推送权限，就看不到push的地址。
```

>推送分支

>>推送分支，就是把该分支上的所有本地提交推送到远程库。推送时，要制定本地分支
>>Git就会把该分支推送到远程库对应的远程分支上：

```Bash
git push orgin master
//推送其他分支，比如dev
git push origin dev
```

>> master 分支是主分支，因此要时刻与远程同步；
>> dev 分支是分开发分支，团队所有成员都需要在上面工作，所以也需要与远程同步；
>> bug分支只用于在本地修复Bug,就没有必要推到远程了，除非老板要看每周修复几个
>> feature分支是否推到远程，取决于你是否和你的小伙伴合作在上面开发。

>抓取分支

```Bash
git clone git@github.com:jmichaelliao/learngit.git
```

>从远程库clone时，默认情况下，只能看到本地的master分支。

```Bash
git branch
//只能看到本地的master
```

>需要在dev分支上开发，就必须创建远程origin的dev分支到本地，

```Bash
git checkout -b dev origin/dev
```

>现在他可以在dev上继续修改，然后，时不时把dev分支push到远程

```Bash
git commit -m "add /usr/bin/env"
git push origin dev
```

>你的小伙伴已经向origin/dev分支推送了他的提交，而碰巧你也对同样的文件作了修改
>并试图推送

```Bash
git add hello.py
git commit -m "add coding:utf-8"
git push orgin dev
```

>推送失败，因为你的小伙伴的最新提交和你的试图推送的提交有冲突，
>解决办法先用git pull把最新的提交从orgin/dev抓取下来，
>然后在本地合并，解决冲突再推送

```Bash
git pull
```

>git pull也失败了，原因是没有指定本地dev分支与远程origin/dev分支的链接，
>根据提示，设置dev和origin/dev的链接。

```Bash
git branch --set-upstream dev origin/dev
git pull
```

>因此，多人写作的工作模式时通常
>1. 首先，可以试图用git push origin branch-name 推送自己的修改
>2. 如果推送失败，则因为远程分支比你的本地更新，需要先用git pull试图合并
>3. 如果合并有冲突，则解决冲突，并在本地提交；
>4. 没有冲突，或者解决掉冲突后,再用git push origin branch-name 推送就能成功！
>如果git pull提示“no tracking information”,则说明本地分支和远程分支的链接关系
>没有创建，用命令 git branch --set-upstream branch-name origin/branch-name


