
# 基于 ***CentOS*** 6.5 x64 服务器搭建

|No.|Contect|
|:--:|:-------------------:|
|[1](#jump_1)|总体运行环境搭建与优化|
|[2](#jump_2)|数据库服务器搭建|
|[3](#jump_3)|主从热备、冷备|
|[4](#jump_4)|Tomcat服务器搭建|
|[5](#jump_5)|Apache服务器搭建|
|[6](#jump_6)|php环境搭建|
|[7](#jump_7)|nginx环境搭建|
|[8](#jump_8)|负载均衡配置|

* 基于 ***CentOS*** 6.5 x64系统，所用源码版本根据需求选择版本下载。

* 根据项目需求，选择搭建 ***Nginx+Tomcat+MySQL***,***Nginx+MySQL+Php***,***Apache+MySQL+Php***环境

<span id="jump_1"></span>
## 总体运行环境搭建与优化

###1. 安装 ***CentOS*** 6.5 x64位操作系统，可以选择完整开发包。

###2. 新建文件夹为目录扩容做准备

>
>|**文件目录**|**目录功能**|
>|:--------------------|:-----------------------------|
>|/usr/local/webserver | 工程及相关环境目录
>|/data                | 数据及log文件、备份保存目录
>|/rootcp              | 暂时保存root目录下文件
>|/htdocs/software     | 保留上传安装文件
>|/htdocs/www          | 保留上传项目工程

* 建立目录代码

```Bash
mkdir -p /usr/local/webserver
mkdir -p /data
mkdir -p /rootcp
mkdir -p /htdocs
```

* 将原 ***/root*** 文件夹下所有文件复制到 ***/rootcp*** 文件夹下，已备完成 ***/root*** 目录扩容后恢复。

```Bash
cp /root/.* /rootcp
```

###3. 弹性云存贮分区，并挂载扩展文件夹

* 查看分区。不存在扩展分区的情况跳过扩展文件夹目录

```Bash
fdisk -l
```

> Disk ***/dev/vdb***: 1073.7 GB, 1073741824000 bytes
> 16 heads, 63 sectors/track, 2080507 cylinders
> Units = cylinders of 1008 * 512 = 516096 bytes
> Sector size (logical/physical): 512 bytes / 512 bytes
> I/O size (minimum/optimal): 512 bytes / 512 bytes
> Disk identifier: 0x00000000

* 创建分区，并格式化。
```Bash
fidsk /dev/vdb
```

>Device contains neither a valid DOS partition table, nor Sun, SGI or OSF disklabel
>Building a new DOS disklabel with disk identifier 0x5ac15d55.
>Changes will remain in memory only, until you decide to write them.
>After that, of course, the previous content won't be recoverable.
>
>Warning: invalid flag 0x0000 of partition table 4 will be corrected by w(rite)
>
>WARNING: DOS-compatible mode is deprecated. It's strongly recommended to
>         switch off the mode (command 'c') and change display units to
>         sectors (command 'u').
>
>Command (m for help): 

* n 创建分区 d 删除分区 在出现 Command 后直接输入d或n即可。

>Command (m for help): n
>Command action
>  e   extended
>  p   primary partition (1-4)

* 输入 p 并选择分区（1-4）中的某一项。

>p
>Partition number (1-4): 1

* 选择分区大小。

>First cylinder (1-2080507, default 1): 
>
>>1T: 
>>520126
>>1040253
>>1560380

* 输入w保存所做设置，并等待设置完成生效。

* 完成后使用 ***fdisk -l*** 命令查看结果。

>   |Device Boot|      Start    |     End   |   Blocks  | Id | System  |
>   |:---------:|:-------------:|:---------:|:---------:|:--:|:-------:|
>   |/dev/vdb1  |      ***1***  | ***520126***| 262143472+| 83 | Linux |
>   |/dev/vdb2  | ***520127***  |***1040253***| 262144008 | 83 | Linux |
>   |/dev/vdb3  |***1040254***  |***1560380***| 262144008 | 83 | Linux |
>   |/dev/vdb4  |***1560381***  |***2080507***| 262144008 | 83 | Linux |

* 进行格式化

```Bash
mkfs.ext4 /dev/vdb1
mkfs.ext4 /dev/vdb2
mkfs.ext4 /dev/vdb3
mkfs.ext4 /dev/vdb4
```

###4. 自动挂载设置

* 自动挂载需要设备 ***UUID*** ，使用指令查询并复制备用。

```Bash
ls -l /dev/disk/by-uuid/

blkid
```

* 编辑系统分区列表

```Bash
vim /etc/fstab
```

* 添加格式化后新增加的 ***UUID*** 及对应设备

* 保存并退出，重启系统

```Bash
shutdown -r now
```

* 重启系统确认挂载成功

* 复制 ***/rootcp*** 下文件到 ***/root*** 文件夹下

```Bash
cp /rootcp/.* /root/
```

* 建立其他目录，及用户用户组,以及赋予权限

    * 工程服务器

    ```Bash
    mkdir -p /htdocs/software
    mkdir -p /htdocs/www
    mkdir -p /usr/local/webserver/website
    mkdir -p /data/bak
    mkdir -p /data/logs
    ```

    * 数据库服务器

    ```Bash
    mkdir -p /htdocs/software
    mkdir -p /htdocs/www
    mkdir -p /usr/local/webserver/mysql
    mkdir -p /usr/local/webserver/mysql/data
    mkdir -p /data/bak
    mkdir -p /data/logs
    ```
    
    * 添加工程用户用户组
    
    ```Bash
    /usr/sbin/groupadd www
    /usr/sbin/useradd -g www www 
    chmod +w /htdocs/www
    chown -R www:www /htdocs/www
    chmod +w /data/logs
    chown -R www:www /data/logs
    chmod +w /usr/local/webserver/website
    chown -R www:www /usr/local/webserver/website
    ```
    
    * 添加数据库用户用户组
    
    ```Bash
    /usr/sbin/groupadd mysql
    /usr/sbin/useradd -g mysql mysql
    chmod +w /htdocs/www
    chown -R mysql:mysql /htdocs/www
    chmod +w /data/logs
    chown -R mysql:mysql /data/logs
    chmod +w /usr/local/webserver/mysql
    chown -R mysql:mysql /usr/local/webserver/mysql
    chmod +w /usr/local/webserver/mysql/data
    chown -R mysql:mysql /usr/local/webserver/mysql/data
    ```

### 5. 安装类库

```Bash
yum -y install gcc cc perl git lua-devel pcre-devel openssl-devel gd-devel \
gcc-c++ ncurses-devel openssh-server openssh-clients
```

* 如需要类库特定版本或最新版本需下载源码，进行源码安装。

### 6. 更新系统时间

```Bash
ntpdate cn.pool.ntp.org
```

### 7. 重启系统

```Bash
shutdown -r now
```

* 因安装openssh,openssl后系统会自动关闭，如是机房服务器需重新联系机房开机。如自行进行重启操作，可避免联系机房人员操作。

### 8. 安装 ***libevent*** 类库

* 查找系统集成 ***libevent*** 并删除

```Bash
rpm -qa|grep libevent
rpm -ql libevent
rpm -e libevent --nodeps
rpm -e `rpm -qa|grep libevent` --allmatches --nodeps
```

* 安装上传到 ***/htdocs/software*** 目录下的源码文件

```Bash
cd /htdocs/software
tar zxf libevent-2.0.22-stable.tar.gz
cd libevent-2.0.22-stable
mkdir -p /usr/local/webserver/libevent
./configure --prefix=/usr/local/webserver/libevent
make && make install
```

* 安装完成后设置软链接

```Bash
ln -sv /usr/local/webserver/libevent/lib/libevent-2.0.so.5 /usr/lib/libevent-2.0.so.5 
ln -sv /usr/local/webserver/libevent/lib/libevent-2.0.so.5 /usr/lib64/libevent-2.0.so.5 
ln -sv /usr/local/webserver/libevent/lib/libevent-2.0.so.5 /lib64/libevent-2.0.so.5 
ln -sv /usr/local/webserver/libevent/lib/libevent-2.0.so.5 /lib/libevent-2.0.so.5 
ln -sv /usr/local/webserver/libevent/lib/libevent-2.0.so.5 /usr/local/lib64/libevent-2.0.so.5 webserver/
ln -sv /usr/local/webserver/libevent/lib/libevent-2.0.so.5 /usr/local/lib/libevent-2.0.so.5
```

### 9. linux 内核优化

* 用户最大进程数配置

```Bash
vim /etc/security/limits.d/90-nproc.conf

#Add
www hard nproc unlimited
www soft nproc unlimited

#Add
mysql hard nproc unlimited
mysql soft nproc unlimited
```

* 产生 ***core*** 文件

```Bash
ulimit -SHn 65535
ulimit -c unlimited
```

* 修改 ***/etc/profile*** 文件配置

```Bash
vim /etc/sysctl.conf

#Add
net.ipv4.tcp_max_syn_backlog = 65536
net.core.netdev_max_backlog = 32768
net.core.somaxconn = 32768
net.core.wmem_default = 8388608
net.core.rmem_default = 8388608
net.core.rmem_max = 16777216
net.core.wmem_max = 16777216
net.ipv4.tcp_timestamps = 0
net.ipv4.tcp_synack_retries = 2
net.ipv4.tcp_syn_retries = 2
net.ipv4.tcp_tw_recycle = 1
#net.ipv4.tcp_tw_len = 1
net.ipv4.tcp_tw_reuse = 1
net.ipv4.tcp_mem = 94500000 915000000 927000000
net.ipv4.tcp_max_orphans = 3276800
#net.ipv4.tcp_fin_timeout = 30
#net.ipv4.tcp_keepalive_time = 120
net.ipv4.ip_local_port_range = 1024 65535

/sbin/sysctl -p
```

<span id="jump_2"></span>
## 数据库服务器搭建

### 1. 安装 ***cmake***

```Bash
cd /htdocs/software
tar -zxf cmake-2.8.10.2.tar.gz
cd cmake-2.8.10.2
./configure --prefix=/usr/local/webserver/camke
make && make install
```

### 2. 将 ***cmake*** 永久加入系统环境变量

```Bash
vim /etc/profile

#Add
PATH=/usr/local/webserver/cmake/bin:$PATH
export PATH

source /etc/profile

echo $PATH
```

###3. 安装 ***MySQL***

* 创建 ***mysql*** 用户及用户组

```Bash
groupadd mysql
useradd -r -g mysql mysql
```

* 编译安装 ***MySQL***
    
```Bash
rpm -ivh mysql-community-5.7.18-1.el6.src.rpm
cd /mysql-5.7.18/SOURCE/
tar -zxf mysql-5.7.18.tar.gz
cd mysql-5.7.18 
```

* ***cmake*** 编译 ***MySQL*** 源码参数

```Bash
cmake -DCMAKE_INSTALL_PREFIX=/usr/local/webserver/mysql \
-DMYSQL_UNIX_ADDR=/usr/local/webserver/mysql/mysql.sock \
-DDEFAULT_CHARSET=utf8 \
-DDEFAULT_COLLATION=utf8_general_ci \
-DWITH_MYISAM_STORAGE_ENGINE=1 \
-DWITH_INNOBASE_STORAGE_ENGINE=1 \
-DWITH_MEMORY_STORAGE_ENGINE=1 \
-DWITH_READLINE=1 \
-DENABLED_LOCAL_INFILE=1 \
-DMYSQL_DATADIR=/usr/local/webserver/mysql/data \
-DMYSQL_USER=mysql \
-DMYSQL_TCP_PORT=3306

make && make install
```

* 等待编译完成后

```Bash
cp /usr/local/webserver/mysql/support-files/my-huge.cnf /etc/my.cnf
```

* 编辑 ***my.cnf*** 内容

```Bash
vim /etc/my.cnf

[mysqld]
max_allowed_packet = 32M 
#过小会导致导入数据库SQL时出错
加入以下参数
#Add
lower_case_table_names = 1
#允许表名小写
max_connections = 10000
#最大连接数
wait_timeout = 31536000
#连接等待时间
datadir = /usr/local/webserver/mysql/data
#指定数据库存放位置

log-bin=bin_log

```

* 创建系统数据库的表

```Bash
cd /usr/local/webserver/mysql
scripts/mysql_install_db --user=mysql
```

* 测试 ***MySQL***

```Bash
/usr/local/webserver/mysql/bin/mysql -uroot msyql
```

* 如果提示未发现 ***mysql.sock*** ，说明创建系统数据库表时有问题。需要排查 ***my.cnf*** 内容。删除/usr/lcoal/webserver/mysql/data/目录下的log与err文件，重新执行创建数据库表。直至测试成功。

### 4. 将 ***mysql*** 加入系统环境变量

```Bash
vim /root/.bash_profile

#PATH=$PATH:$HOME/bin修改为
PATH=$PATH:$HOME/bin:/usr/local/webserver/mysql/bin:/usr/local/webserver/mysql/lib

source /root/.bash_profile

chown -R mysql:mysql /usr/lcoal/webserver/mysql
chown -R mysql:mysql /usr/lcoal/webserver/mysql/data
```

### 5. 将 ***mysql*** 加入到开机启动服务

```Bash
cp /usr/local/webserver/mysql/support-files/mysql.server /etc/init.d/mysql

service mysql start

chkconfig --add mysql
#让mysql服务开机运行
chkconfig --list|grep mysql
#查看是否已经添加成功
```

### 6. 修改 ***MySQL*** 的root用户的密码以及打开远程连接

```Bash
/usr/local/webserver/mysql/bin/mysql -u root mysql
mysql> usr mysql;
mysql> desc user;
mysql> grant all privileges on *.* to root @"%" identified by "XXXXXX";
# 为root添加远程连接的能力（XXXXXX为密码）
mysql> update user set password = password('XXXXXX') where User='root';
# 设置root用户本地登录密码，非远程登录密码！
mysql> select host,user,password from user where user='root';
# 查看root用户权限
mysql> flush privileges;
# 必须刷新，否则设置无效
mysql> exit

#重新登陆
mysql -uroot -p
Enter password:
```

### 7. 打开Linux防火墙端口，否则无法远程连接。

```Bash
vim /etc/sysconfig/iptables

#在22端口规则链下加入
-A INPUT -m state NEW -m tcp -p --dport 3306 -j ACCEPT service iptables restart
```

* 机房服务器内网之间网站服务器与数据库服务器3306访问端口默认开放。如需外网访问数据库，需联系机房人员开放机房3306上行访问端口

<span id="jump_3"></span>
## 主从热备份与冷备份

### 1. 热备份  ***master*** 主数据库配置

* 设置备份数据库并拷贝原数据库

```Bash
grant replication slave on *.* to root@'192.168.1.2' identified by 'XXXXXX';
# 这里 192.168.1.2 是 slave-mysql 对应的ip 备份数据库内网ip

flush tables with read lock;
mysqldump -uroot -pXXXXXX [table_name] > [table_name].sql
unlock tables;
```

* 修改 ***my.cnf*** 配置

```Bash
[mysqld]
#唯一值，并不能与副服务器相同
server-id = 1 
#日志文件以binary_log为前缀，如果不给log-bin赋值，日志文件将以 master-server-hostname为前缀
log-bin=bin-log
#日志文件跳过的数据库（可选属性）
binlog-ignore-db = mysql,test,information_schema
#日志文件操作的数据库（可选属性）
binlog-do-db= [tables_name]

#linux 重启mysql
/etc/init.d/mysqld restart

mysql> reset master;
mysql> show master status;
#查看master_log_file master_log_pos
```

### 2. 修改 ***slave*** 从服务器

```Bash
#快照备份还原至从服务器
mysql> create database [table_name];
mysql> use [table_name];
mysql> source /[table_name].sql

#修改 my.cnf 
[mysqld]
#唯一，并与主服务器上的server-id不同。
server-id=2
#主服务器的主机名或者ip地址
#master-host = 192.168.0.104
#如果主服务器没有在默认的端口上监听，则需确定master-port选项
#master-port=3306
#创建的用户名和密码
#master-user=root
#master-password=XXXXXX
#复制操作要针对的数据库（可选，默认为全部）
replicate-do-db=[table-name]
#如果主副服务器间连接经常失败，需增加master-retry-count 和 master-connect-retry
master-retry-count = 999
#master-connect-retry = 60

/etc/init.d/mysqld restart

mysql> change master to,
>master_host='192.1658.0.104',
>master_port=3306,
>master_user='AffairLog',
>master_password='password',
>master_log_file='log.00027',
>mster_log_pos=3151;

#master_log_file
#master_log_pos
#这两个参数在master服务器中，使用show master status 查看

mysql> change master to master_host='192.168.1.2',master_port=3306,master_user='root',
>master_password='XXXXXX',master_log_file='bin_log.000001',master_log_pos=107;

#如果Slave_IO_State显示waiting for master to send event 的话就表示已经启动了，反之就运行
start slave;

#在show slave status 的命令输出后，应该包含Slave_IO_Running对应的值为YES。
stop slave;

```

### 3. 冷备份

>CentOS冷备份基于 ***crontab*** 定时进行压缩打包

* 安装 ***crontab*** 软件及支持

```Bash
yum install vixie-cron
yum install crontabs
```

* 创建备份脚本
    
    * 数据库备份脚本 

    ```Bash
    
    mkdir -p /data/bak
    vim /root/sqlbakup.sh
    #备份数据库脚本，在数据库服务器上执行
    
    #!/bin/bash
    #backup database
    
    #################删除备份数据库####################
    rm -f /data/bak/[table_name]_$(date +%Y%m%d --date='7 days ago').sql.gz
    #################备份数据库########################
    mysqldump -uroot -p"XXXXXX" [base_name] | gzip >/data/bak/[base_name]_$(date    +%Y%m%d).sql.gz
    #################导出单个表数据和结构##############
    #mysqldump -u root -p"XXXXXX" [base_name] [table_name]>/data/back/[ table_name]_dump.20170101_0300.sql
    #################还原单个数据库和结构##############
    #mysql -u root -p XXXXXX
    #use [base_name];
    #source /data/bak/[table_name]_dump.20170101_0300.sql

    chmod 755 /root/sqlbakup.sh

    ```

    * 工程备份脚本 

    ```Bash

    mkdir -p /data/backup
    vim /root/webbakup.sh
    #备份项目脚本，在工程服务器上执行


    #!/bin/bash
    #backup web
    
    cd /data/backup/
    
    ###################删除备份工程####################
    rm -f /data/backup/[web_name]_$(date +%Y%m%d --date='7 days ago').tar.gz

    ###################备份工程####################
    tar -zcvf /data/backup/[web_name]_$(date +%Y%m%d).tar.gz /usr/local/webserver/website/[web_name]

    chmod 755 /root/webbakup.sh

    ```

* 将脚本添加到 ***crontab*** 服务中

```Bash
crontab -e

#添加一行，root 用户不需要指定执行的用户名
0 3 * * * /root/webbakup.sh
#数据库服务器同理

Chmod +x /root/webbakup.sh
#给脚本执行权限

#service crond restart

```

* ***crontab*** 简单说明

```Bash
# Example of job definition:
# ·---------------------- minute (0-59)
# |  ·------------------- hour (0-23)
# |  |  ·---------------- day of month(1-31)
# |  |  |  ·------------- month(1-12)OR jan,feb,mar,apr ...
# |  |  |  |  ·---------- day of week(0-6)(Sunday=0 or 7) OR 
# |  |  |  |  |           sun,mon,tue,wed,thu,fri,sat
# |  |  |  |  |
# *  *  *  *  *  user-name command to be executed

```

>可以发现，crontab的执行周期有5个部分组成，其中第一个是分钟数，第二个是小时数，
>第三个是一个月中的第几天。如果是 * 就表示每天都会调度。
>
>user-name 部分，如果需要以其他用户调度，则可以指定，否则不能指定。
>比如root用户就不能指定root，否则有调度日志，但是没有实际执行。

<span id="jump_4"></span>
## Tomcat服务器搭建

### 1. 安装 ***JDK***

```Bash
cd /htdocs/software
tar zxf jdk-7u80-linux-x64.tar.gz
mv jdk1.7.0_80/ /usr/local/webserver/jdk/

#修改系统环境变量
vim /etc/profile 
export JAVA_HOME=/usr/local/webserver/jdk
export PATH=$JAVA_HOME/bin:$PATH
export CLASSPATH=$JAVA_HOME/lib:$JAVA_HOME/jre/lib:$CLASSPATH

#使其立即生效，并检查是否安装成功
source /etc/profile
java -version
```

### 2. 安装 ***tomcat***

```Bash
cd /htdocs/software
tar zxf apache-tomcat-7.0.61.tar.gz
mv apache-tomcat-7.0.61 /usr/local/webserver/tomcat/

#运行测试
/usr/local/webserver/tomcat/bin/startup.sh
#显示动态日志
tail -f /usr/local/webserver/tomcat/logs/catalina.out
#停止
/usr/local/webserver/tomcat/bin/shutdown.sh

#增加JVM的内存空间（数值根据机器具体配置）
vim /usr/local/webserver/tomcat/bin/catalina.sh
#在第二行复制如下参数（适合内存大于等于8GB的服务器）
JAVA_OPTS="-server -Xms3g -Xmx3g -Xss256k -Xmn1g -XX:PermSize=1g -XX:MaxPermSize=1g -XX:+UseParNewGC -XX:+UseConcMarkSweepGC -XX:+UseFastAccessorMethods -XX:+UseBiasedLocking -XX:+UseCMSCompactAtFullCollection -XX:+UseCMSInitiatingOccupancyOnly -XX:+CMSParallelRemarkEnabled -XX:+CMSClassUnloadingEnabled -XX:CMSFullGCsBeforeCompaction=0 -XX:CMSInitiatingOccupancyFraction=80 -XX:+DisableExplicitGC -XX:+AggressiveOpts -XX:LargePageSizeInBytes=128M -XX:SoftRefLRUPolicyMSPerMB=0 -Djava.awt.headless=true"

CATALINA_OUT=/data/tomcat/logs/catalina.out

#建立文件夹
mkdir -p /data/tomcat/logs/

```

### 3. 修改 ***server.xml*** 配置 

```xml
<!--1. 配置多个项目-->
<Host name="localhost"  appBase="webapps" 
      unpackWARs="true" autoDeploy="true">
    <Valve className="org.apache.catalina.valves.AccessLogValve" directory="logs"
        prefix="localhost_access_log." suffix=".txt"
        pattern="%h %l %u %t &quot;%r&quot; %s %b" />

        <Context docBase="uim" path="" />
        <Context docBase="third" path="/usr/local/webserver/website/AAA/third" />
     </Host>

<Host name="www.ycyoes.com"  appBase="hcode"
      unpackWARs="true" autoDeploy="true">
    <Valve className="org.apache.catalina.valves.AccessLogValve" directory="logs"
        prefix="localhost_access_log." suffix=".txt"
        pattern="%h %l %u %t &quot;%r&quot; %s %b" />

        <Context docBase="hcode" path="" />
        <Context docBase="four" path="/usr/local/weberver/website/XXX/four" />
     </Host>
<<!-- 其中name为localhost或者IP或者是域名 -->
<<!-- appBase为项目所在目录，可为绝对路径或者相对路径 -->

<!--2. 配置域名访问-->
<!--在host节点输入name属性域名即可-->
<Host name="www.aaabbb.com" appBase="hcode"
      unpackWARs="true" autoDeploy="true">
<!--即可在浏览器中通过http://www.aaabbb.com/appName 进行访问-->

<!--3. 项目名缺省-->
<!--
一般的，进行项目访问时需要在url最后输入系统名称，如http://www.aaabbb.com/appName,
可通过tomcat配置，缺省系统名称也可访问。
在<Host>节点内添加如下内容：
-->
<Context docBase="hcode" path="" />
<!--其中，docBase即为系统名称，此时通过http://www.ycyoes.com
即可进行系统的访问。-->

<!--4. 系统访问时端口号缺省-->
<!--在访问系统时一般需要在ip地址后带上端口号，如http://ip:port/appName
,但是80端口可缺省，配置tomcat端口为80则不需要在url中输入端口。配置如下：
-->
<Connector port="80" protocol="HTTP/1.1"
               connectionTimeout="20000"
               redirectPort="443" />
<!--此时可通过http://ip/appName 进行访问。-->

<!--5. https访问配置-->
<!--在server.xml中打开如下配置：-->

<Connector port="443" protocol="org.apache.coyote.http11.Http11Protocol"
               maxThreads="150" SSLEnabled="true" scheme="https" secure="true"
               clientAuth="false"
                sslProtocol="TLS"
                keystoreFile="/home/doc/keys/213972284410468.pfx"
                keystoreType="PKCS12"
                keystorePass="213972284410468"         />

<!--添加keystoreFile，keystoreType，keystorePass即可进行https访问。-->

<!--6. 自动跳转到https连接-->
<!--如果希望输入http链接时自动跳转到https，需要在web.xml中添加如下内容：-->
<login-config>
                <!-- Authorization setting for SSL -->
                <auth-method>CLIENT-CERT</auth-method>
                        <realm-name>Client Cert Users-only Area</realm-name>
        </login-config>
        <security-constraint>
                <!-- Authorization setting for SSL -->
                <web-resource-collection >
                        <web-resource-name >SSL</web-resource-name>
                        <url-pattern>/*</url-pattern>
                </web-resource-collection>
                <user-data-constraint>
                        <transport-guarantee>CONFIDENTIAL</transport-guarantee>
                </user-data-constraint>
        </security-constraint>

<!--以上内容位置在</web-app>内，添加后输入http访问时会自动跳转到https连接。-->

<!--7. https访问端口号缺省-->
<!--Tomcat中https默认端口为8443，该为443后可进行端口号缺省访问系统。如下：-->

<Connector port="443" protocol="org.apache.coyote.http11.Http11Protocol"
               maxThreads="150" SSLEnabled="true" scheme="https" secure="true"
               clientAuth="false"
                sslProtocol="TLS"
                keystoreFile="/home/doc/keys/213972284410468.pfx"
                keystoreType="PKCS12"
                keystorePass="213972284410468"         />

```

### 4. 数据库服务器异地的情况下，需要修改项目数据库配置

<span id="jump_5"></span>
## Apache服务器搭建

### 1. 上传安装包到  ***/htdocs/software*** 目录

>apr-1.5.2.tar.gz
>apr-util-1.5.4.tar.gz  
>autoconf-2.10.tar.gz 
>cmake-2.8.11.2.tar.gz  
>freetype-2.4.0.tar.gz  
>gd-2.0.35.tar.gz 
>httpd-2.4.12.tar.gz
>jpegsrc.v8c.tar.gz  
>libgd-gd-libgd-9f0a7e7f4f0f.tar.gz
>libmcrypt-2.5.7.tar.gz 
>libpng-1.6.17.tar.bz2 
>libxml2-2.7.8.tar.gz 
>mysql-5.6.25.tar.gz
>pcre-8.33.zip
>PHP-5.6.9.tar.gz 
>zlib-1.2.5.1.tar.gz 

* 版本根据需求下载

### 2. 检查安装时使用的编译工具是否存在

```Bash
gcc -v
```

### 3. 卸载默认的低版本环境及rpm包环境

```Bash
rpm -qa | grep -i httpd
service httpd stop
rpm -e httpd --nodeps
rpm -qa | grep -i mysql
rpm -qa | grep -i php
```

### 4. 准备工作

>正式开始编译安装步骤，我们按照一定的顺序安装：Apache –>Mysql –>PHP。
>但在安装PHP之前，应该先安装PHP5需要的最新版本库文件,
>例如：libxml2、libmcrypt、GD2库等文件。
>安装GD2库是为了让PHP5支持GIF、PNG、JPEG图片格式，
>所以在安装GD2库之前还要先安装最新的 zlib、libpng、freetype 和 jpegsrc 等库文件。
>而且中间还会穿插安装一些软件。
>正式安装前先做以下操作：

* 加载 CURL  SSL

```Bash
yum -y install curl-devel
yum  install  openssl.x86_64 openssl-devel.x86_64 -y
```

* 安装libtool.i386

```Bash
yum install libtool
```

* 安装libtool-ltdl-devel.i386

```Bash
yum install libtool-ltdl-devel
```

### 5. 安装 ***Apache*** 依赖软件

* 安装apr 

```Bash
cd /htdocs/software
tar zxf apr-1.5.2.tar.gz
cd apr-1.5.2
./configure --prefix=/usr/local/webserver/apr
mkdir -p /usr/local/webserver/apr
make && make install
```

* 安装apr-util

```Bash
cd /htdocs/software
tar zxf apr-util-1.5.4
cd apr-util-1.5.4

./configure --prefix=/usr/local/webserver/apr-util \
--with-apr=/usr/local/webserver/apr

mkdir -p /usr/local/webserver/apr-util

make && make install

```

* 安装pcre

```Bash
#安装前需要安装
yum install make gcc gcc-c++ zlib-devel libaio -y

cd /htdocs/software
unzip pcre-8.33.zip
cd pcre-8.33
./configure --prefix=/usr/local/webserver/pcre

mkdir -p /usr/local/webserver/pcre

make && make install
```

* 安装zlib

```Bash
tar zxf zlib-1.2.5.1.tar.gz
cd zlib-1.2.5
./configure --prefix=/usr/local/webserver/zlib
# 如果有报错：需要添加64位支持
CFLAGS="-O3 -fPIC" ./configure --prefix=/usr/local/webserver/zlib

mkdir -p /usr/local/webserver/zlib

make && make install
```

### 6. 安装 ***apache***

```Bash
tar zxf httpd-2.4.12.tar.gz
cd httpd-2.4.12
./configure --prefix=/usr/local/webserver/apache/ \
 --with-apr=/usr/local/webserver/apr \
 --with-apr-util=/usr/local/webserver/apr-util \
 --with-pcre=/usr/local/webserver/pcre/ \
 --with-zlib=/usr/local/webserver/zlib/ \
 --disable-userdir \
 --enable-deflate-shared \
 --enable-expires=shared \
 --enable-rewrite=shared \
 --enable-static-support  \
 --enable-so \
 --enable-rewrite

make && make install

```

### 7. 将 ***Apache*** 安装为系统服务

```Bash
cp /usr/local/apache/bin/apachectl /etc/init.d/httpd
vim /etc/init.d/httpd

#!/bin/sh
#chkconfig:35 50 90 
#description:Apache Web Server

chkconfig --add httpd
chkconfig --list httpd
service httpd start

```

### 8. 关闭防火墙service iptables stop，机房服务器联系机房人员开放80端口

```Bash
/sbin/iptable -I INPUT -p tcp --dport 80 -j ACCEPT
#保存
/etc/rc.d/init.d/iptables save
#查看端口
/etc/init.d/iptables status
```

>在浏览器刷入ip地址，可以打开看到 It works!，Apache已经成功安装。

### 9. ***Apache*** 配置多项目

```Bash
# 基本配置
User www
Group www

ServerName [IP] 
ServerName [www.aaabbb.com]

DcumentRoot "/usr/local/webserver/website"
<Directory "/usr/local/webserver/website">
    Options Indexes FollowSymLinks
    AllowOverride None
    Require all granted
</Directory>

# 配置多项目
# 开启包含
Include /etc/httpd/extra/httpd-vhosts.conf
vim /etc/httpd/extra/httpd-vhosts.conf

<VirtualHost 127.0.0.2:80>
    DocumentRoot /usr/local/webserver/website
    ServerName 127.0.0.2:80
</VirtualHost>
<Directory "/usr/local/webserver/website">
    Options Indexes FollowSymLinks Multiviews
    AllowOverride All
    Order Allow,Deny
    Allow from all
</Directory>

# 配置虚拟主机
<VirtualHost *:80>
    ServerName www.aaabbb.com
    ServerAdmin aaabbb@mail.example.com
    DocumentRoot "/usr/local/webserver/website"
    ErrorLog"/data/logs/apache2/aaabbb_errors.log"
    CustomLog"/data/logs/apache2/aaabbb_accesses.log" common
</VirtualHost>

# 配置多个端口

Listen 80
Listen 81

ServerName localhost:80
ServerName localhost:81

DcumentRoot "/usr/local/webserver/website/aaabbb"
DucmentRoot "/usr/local/webserver/website/cccddd"
```


<span id="jump_6"></span>
## php环境搭建

### 1. 参照 ***MySQL*** 安装方法安装  ***MySQL***

### 2. 安装 ***Php*** 扩展库

* 安装 ***libxml2***

```Bash
tar zxf libxml2-2.7.8.tar.gz
cd libxml2-2.7.8
./configure --prefix=/usr/local/webserver/libxml2
mkdir -p /usr/local/webserver/libxml2
make && make install
```

* 安装 ***libmcrypt***

```Bash
tar zxf libmcrypt-2.5.7.tar.gz
cd libmcrypt-2.5.7
./configure --prefix=/usr/local/webserver/libmcrypt
mkdir -p /usr/local/webserver/libmcrypt
make && make install
```

* 安装 ***libpng***

```Bash
tar zxf libpng-1.6.17.tar.gz
cd libpng-1.6.17
./configure --prefix=/usr/local/webserver/libpng
mkdir -p /usr/local/webserver/libpng
make && make install
```

* 安装 ***jpeg8***

```Bash
tar zxf jpegsrc.v8c.tar.gz
cd jpeg-8
./configure --prefix=/usr/local/webserver/jpeg8 --enable-shared --enable-static
mkdir -p /usr/local/webserver/jpeg8
make && make install
```

* 安装 ***freetype***

```Bash
tar zxf freetype-2.4.0.tar.gz
cd freetype-2.4.0/builds/unix
mkdir -p /usr/local/webserver/freetype
./configure --prefix=/usr/local/webserver/freetype --enable-shared
cd /usr/local/webserver/freetype-2.4.0
make && make install
```

* 安装 ***autoconf***

```Bash
tar zxf autoconf-2.10.tar.gz
cd autoconf-2.10
./configure
make && make install
```

* 安装 ***GD*** 库文件

```Bash
tar zxf libgd-gd-libgd-9f0a7e7f4f0f.tar.gz
cd libgd-gd-libgd-9f0a7e7f4f0f
cmake .
make && make install
```

### 3. 安装 ***Php***

```Bash
tar zxf php-5.6.9.tar.gz
cd php-5.6.9
mkdir -p /usr/local/webserver/php
./configure --prefix=/usr/local/webserver/php \
--with-apxs2=/usr/local/webserver/apache/bin/apxs \
--with-msyql \
--with-mysqli=/usr/local/webserver/mysql/bin/mysql_config \
--with-libxml-dir=/usr/local/webserver/libxml2/ \
--with-jpeg-dir=/usr/local/webserver/jpeg8/ \
--with-png-dir=/usr/local/webserver/libpng/ \
--with-freetype-dir=/usr/local/webserver/freetype/ \
--with-gd -with-zlib-dir=/usr/local/webserver/zlib/ \
--with-mcrypt=/usr/local/webserver/libmcrypt/ \
--enable-soap --enable-mbstring-all --enable-sockets

make && make install

cp php.ini-production /usr/local/webserver/php/lib/php.ini
vim /usr/lcoal/apache/conf/httpd.conf
vim /etc/httpd/conf/httpd.conf
DirectoryIndex index.html index.htm index.php
AddType application/x-httpd-php .php .phtml
service httpd restart
```

### 4. 测试 ***Php***

```Bash
vim /usr/lcoal/webserver/website/test.php

<?php
phpinfo();
?>

# 在浏览器输入：[IP]:80 即可看到有关php的信息。
```

<span id="jump_7"></span>
## nginx环境搭建

### 1. ***nginx*** 安装及配置

```Bash
chmod +w /data/logs
chown -R www:www /data/logs

cd /htdocs/software
tar zxf ngx_cache_purge-2.3.tar.gz

tar zxf LuaJIT-2.0.2.tar.gz
cd LuaJIT-2.0.2
make && make install PREFIX=/usr/local/webserver/luajit

export LUAJIT_LIB=/usr/local/webserver/luajit/lib

ln -sv /usr/local/webserver/luajit/lib/libluajit-5.1.so.2 /lib64/libluajit-5.1.so.2

cd /usr/local/webserver

git clone https://github.com/simpl/ngx_devel_kit.git
git clone https://github.com/agentzh/echo-nginx-module.git
git clone https://github.com/chaoslawful/lua-nginx-module.git

tar zxf nginx-1.8.1.tar.gz
cd nginx-1.8.1

./configure --user=www --group=www \
--prefix=/usr/local/webserver/nginx1.8.1 \
--with-http_stub_status_module \
--with-http_ssl_module \
--with-http_flv_module \
--with-http_gzip_static_module \
--with-http_sub_module \
--add-module=/htdocs/software/ngx_cache_purge-2.3 \
--add-module=/htdocs/software/fastdfs-nginx-module/src/ \
--with-http_image_filter_module \
--with-pcre \
--with-http_gzip_static_module \
--with-http_ssl_module \
--with-openssl=/htdocs/software/openssl-1.0.2g \
--with-ld-opt="-Wl,-rpath,$LUAJIT_LIB" \
--add-module=/usr/local/webserver/ngx_devel_kit \
--add-module=/usr/local/webserver/echo-nginx-module \
--add-module=/usr/local/webserver/lua-nginx-module

make && make install

cd /usr/local/webserver/nginx1.8.1

#将nginx.conf和--fcgi.conf--拷贝至conf文件夹内，并修改nginx.conf

/usr/local/webserver/nginx1.8.1/sbin/nginx –t
/usr/local/webserver/nginx1.8.1/sbin/nginx
/usr/local/webserver/nginx1.8.1/sbin/nginx –s reload

```

<span id="jump_8"></span>
## 负载均衡配置

### 1. 安装 ***memcached***

```Bash
cd /htdocs/software
tar zxvf memcached-1.4.25.tar.gz
cd memcached-1.4.25
./configure --prefix=/usr/local/webserver/memcached \
--with-livevent=/usr/local/webserver/libevent/
make && make install
```

### 2. 启动 ***memecached***

```Bash
/usr/local/webserver/memcached/bin/memcached -d -m 256 -u root -l 192.168.6.44 -p 11211 -c 1024 -p /tmp/memcached.pid

/usr/local/webserver/memcached/bin/memcached -d -m 1024 -u root -p 11211 -c 1024 -p /tmp/memcached.pid
```

### 3. 安装 ***fastdfs***

```Bash
cd /htdocs/software
tar zxf libfastcommon-1.0.7.tar.gz
cd libfastcommon-1.0.7
sudo ./make.sh
sudo ./make.sh install
# 创建软链接
ln -sv /usr/lib64/libfastcommon.so /usr/local/lib/libfastcommon.so
ln -sv /usr/lib64/libfastcommon.so /usr/lib/libfastcommon.so
ln -sv /usr/lib64/libfdfsclient.so /usr/local/lib/libfdfsclient.so
ln -sv /usr/lib64/libfdfsclient.so /usr/lib/libfdfsclient.so
ln -sv /usr/include/fastcommon /usr/local/include/fastcommon

tar zxf fastdfs-nginx-module_v1.16.tar.gz
tar zxf fastdfs-5.05.tar.gz
mv fastdfs-5.05 /usr/local/webserver/fastdfs-5.05
cd /usr/local/webserver/fastdfs-5.05
sudo ./make.sh
sudo ./make.sh install

mv /etc/init.d/tracker.conf.sample /etc/init.d/tracker.conf
mv /etc/init.d/storage.conf.sample /etc/init.d/storage.conf
mv /etc/init.d/client.conf.sample /etc/init.d/client.conf

mv /etc/fdfs/tracker.conf.sample /etc/fdfs/tracker.conf
mv /etc/fdfs/storage.conf.sample /etc/fdfs/storage.conf
mv /etc/fdfs/client.conf.sample /etc/fdfs/client.conf

cp /htdocs/software/fastdfs-nginx-module/src/mod_fastdfs.conf /etc/fdfs/
cp /usr/local/webserver/fastdfs-5.05/conf/mime.types /etc/fdfs/
cp /usr/local/webserver/fastdfs-5.05/conf/http.conf /etc/fdfs/

ln -sv /usr/include/fastdfs /usr/local/include/fastdfs 

cp /usr/local/webserver/fastdfs-5.05/restart.sh /usr/local/bin/restart.sh
cp /usr/local/webserver/fastdfs-5.05/stop.sh /usr/local/bin/stop.sh
cp /etc/init.d/fdfs_trackerd /usr/local/bin/fdfs_trackerd
cp /etc/init.d/fdfs_storaged /usr/local/bin/fdfs_storaged 

mkdir -p /data/fastdfs

/usr/bin/fdfs_trackerd /etc/fdfs/tracker.conf restart
/usr/bin/fdfs_storaged /etc/fdfs/storage.conf restart
/usr/bin/fdfs_monitor /etc/fdfs/storage.conf

修改相应的fastdfs配置
ln -s fastdfs-路径/data fastdfs-路径/data/M00 

```