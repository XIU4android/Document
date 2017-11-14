
# ***Jenkins*** 持续集成学习

|No.|Contect|
|:--:|:-------------------:|
| [1](#jump_1)| Jenkins 简介 |
| [2](#jump_2)| Jenkins 安装 |
| [3](#jump_3)| Jenkins 使用 |
| [4](#jump_4)| Jenkins 项目创建 |
| [5](#jump_5)| Jenkins 总结 |


<span id="jump_1"></span>
## Jenkins 简介

>Jenkins是一个开源软件项目，是基于Java开发的一种持续集成工具，
>用于监控持续重复的工作，旨在提供一个开放易用的软件平台，
>使软件的持续集成变成可能。


### Jenkins 功能

Jenkins 功能包括：

** 1.持续的软件版本发布/测试项目。**

** 2.监控外部调用执行的工作。**

<span id="jump_2"></span>
## Jenkins 安装

** 1. 首先从Jenkins官方网站 https://jenkins.io/ 下载最新的war包。**

** 2. 安装只需要运行命令：**
    
```jvc
java -jar jenkins.war
```

Jenkins 就启动成功了！它的war包自带Jetty服务器，剩下的工作我们全部在浏览器中进行。

<span id="jump_3"></span>
## Jenkins 使用

>第一次启动Jenkins时，出于安全考虑，Jenkins会自动生成一个随机的按照口令。
>注意控制台输出的口令，复制下来，然后在浏览器输入：

http://localhost:8080/

** 粘贴口令，进入安装界面，如果执行默认的安装，Jenkins就自动配置好了Maven、git等常用插件。最后，创建一个admin用户，完成安装。**

<span id="jump_4"></span>
## Jenkins 项目创建

1. 用管理员账号登录Jenkins后，第一次使用前，需要在“系统管理”->“Global Tool Configuration”->“Maven”中新增一个Maven，直接输入一个名字，选中“自动安装”，Jenkins会自动下载并安装Maven：

2. 然后，在Jenkins首页选择“新建”，输入名字，选择“构建一个maven项目”：

3. 在配置页中，源码管理选择Git，填入地址：

4. 默认使用master分支。如果需要口令，在Credentials中添加用户名/口令，或者使用SSH Key。

5. 构建触发器指定了触发一次构建的条件。推荐使用最简单的配置“Poll SCM”，它的意思是，定时检查版本库，发现有新的提交就触发构建。这种方式对git、SVN等所有版本管理系统都是通用的。

6. 我们在日程表中填入：

```Bash
* * * * *
```

  表示每分钟检查一次。如果你觉得太频繁，可以改成“每3分钟检查一次”：
```Bash
*/3 * * * *
```

7. 在“Build”中，默认的Root POM是pom.xml。如果pom.xml不在根目录下，就填入子目录，例如：wxapi/pom.xml。

8. 在Goals and options中，填入需要执行的mvn命令：clean package，Jenkins将执行如下命令：
mvn clean package

9. 特殊参数也在这里填写，如-DskipTests=true clean package。
保存后，就可以执行自动化构建了。

10.  点击一个构建任务，可以在Console Output中看到控制台详细输出，便于出错排查：

<span id="jump_4"></span>
## Jenkins 部署

** 1. 如果要部署构建好的war包，可以在Post Steps中填上shell命令，直接用脚本部署。**

** 2. 另一种方式是创建另外一个构建项目，手动触发部署。**
 
>无论用哪种方式，都是为了确保编译、部署是通过CI服务器完成的，而不是某台开发机器

我们不想每次登录到Linux去启动Jenkins，也不想写脚本来启动服务。推荐安装JDK后，配合supervisor，把Jenkins直接变成一个服务。

** 1. 可以在Linux上创建一个ci用户，然后，用supervisor启动并指定9001端口：**

```Bash
# /etc/supervisor/conf.d/ci.conf

[program:ci]
command=java -jar /home/ci/jenkins.war --httpPort=9001
user=ci
autostart=true
autorestart=true
startsecs=30
startretries=5
```

Jenkins默认在当前用户的主目录下创建.jenkins目录，所有的配置文件、数据库都存放在里面，只需要备份这个目录就备份了整个CI配置。

这样，一个CI环境就搭建完毕。