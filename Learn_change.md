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