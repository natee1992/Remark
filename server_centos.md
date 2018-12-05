**目录导航**

<a href='#1'>yum源安装python3.6</a>

<a href='#2'>pip修改镜像源</a>

<a href='#3'>scp传输文件/文件夹</a>

<a href='#4'> yum安装mongodb 3.4</a>

<a href='#vim'> yum安装vim</a>

<a href='#zip'> yum安装zip</a>
         
<a href='#http'> http转https</a>
         
<a href='#sentry'> docker搭建sentry</a>
         
<a href='#cpu'>centos查看cpu信息</a>

<a href='#f_s_g'>flask+gunicorn+gevent+supervisor+nginx部署</a>

<a href='#mongodb'>mongodb分片架构部署</a>

<a href='#mysql'>centos7安装mysql5.7</a>

<a href='#cron'>cron命令定时任务</a>
<hr>
<hr>

## yum源安装python3.6
<a name='1'></a>
#### 安装EPEL和IUS软件源

> yum install epel-release

>  yum install https://centos7.iuscommunity.org/ius-release.rpm

#### 安装Python3.6

> yum install python36u

#### 创建python3连接符

> ln -s /bin/python3.6 /bin/python3

#### 安装pip3

> yum install python36u-pip

#### 创建pip3链接符

> ln -s /bin/pip3.6 /bin/pip3


**yum采用python作为命令解释器，python2和python3不兼容，所以如果安装python3后，如果坚持将python链接指向python3，则需要改动一下几个文件中的python解释器路径：**


```python

  1. /usr/bin/yum
  2. /usr/libexec/urlgrabber-ext-down
  3. yum-config-manager  # 找不到文件时 where
  
```
<hr>

## 修改pip镜像源
<a name='2'></a>

1.  新建文件 ~/.pip/pip.conf 
2.  设置
```
 [global]
   index-url = https://pypi.tuna.tsinghua.edu.cn/simple  #清华源
   index-rul = http://pypi.douban.com/simple/    # 豆瓣源
```

***

## scp传输文件/文件夹
<a name='3'></a>
传输文件
> scp 传输文件 root@xxx.xxx.xxx.xxx:目标目录

传输文件夹
> scp -r 传输文件夹 root@xx.xx.x.xx:目标目录

***

## yum安装mongodb 3.4
<a name='4'></a>
**1. 配置mongodb yum源**
> vim /etc/yum.repos.d/mongodb-org-3.4.repo

**2. 添加yum源配置**
```
[mongodb-org-3.4]  
name=MongoDB Repository  
baseurl=https://repo.mongodb.org/yum/redhat/$releasever/mongodb-org/3.4/x86_64/  
gpgcheck=1  
enabled=1  
gpgkey=https://www.mongodb.org/static/pgp/server-3.4.asc
```

**3. yum源安装**
> yum -y install mongodb-org

**4. 修改配置信息**

> vim /etc/mongod.conf

*参数解释*
```
--dbpath 数据库路径(数据文件)
--logpath 日志文件路径
--master 指定为主机器
--slave 指定为从机器
--source 指定主机器的IP地址
--pologSize 指定日志文件大小不超过64M.因为resync是非常操作量大且耗时，最好通过设置一个足够大的oplogSize来避免resync(默认的 oplog大小是空闲磁盘大小的5%)。
--logappend 日志文件末尾添加，即使用追加的方式写日志
--journal 启用日志
--port 启用端口号
--fork 在后台运行
--only 指定只复制哪一个数据库
--slavedelay 指从复制检测的时间间隔
--auth 是否需要验证权限登录(用户名和密码)
--syncdelay 数据写入硬盘的时间（秒），0是不等待，直接写入
--notablescan 不允许表扫描
--maxConns 最大的并发连接数，默认2000  
--pidfilepath 指定进程文件，不指定则不产生进程文件
--bind_ip 绑定IP，绑定后只能绑定的IP访问服务
```
需要修改bind_ip

**5. 关闭防火墙端口**

  *查看防火墙状态*
  
> service firewalld status

  *关闭防火墙*
  
> service firewalld stop

**6. 启动**
> systemctl start mongod.service

**7. 增加远程链接用户**

 *主机端*
> mongo

> use admin

> db.createUser({user:"root",pwd:"123456",roles:["root"]})

>  db.getCollectionNames()

*查看是否增加成功*

> db.auth("root","123456")

 *退出*
 
> exit

**8. 关闭**
> ps -ef |grep mongod

**9. 认证模式启动**
> mongod --auth --config /etc/mongd.conf

<hr>

## yum安装vim
<a name='vim'></a>
> yum -y install vim*

***

## yum安装zip unzip
<a name='zip'></a>
> yum install zip unzip


***

## HTTP转HTTPS
<a name='http'></a>
*1.阿里云申报ssl证书*
*2.证书导入*
*3.Nginx配置*
```
        ssl on;
        ssl_certificate /usr/local/nginx/cert/xxxxxxxxx.pem;
        ssl_certificate_key /usr/local/nginx/cert/xxxxxxxxxxx.key;
        ssl_session_timeout 5m;
        ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:ECDHE:ECDH:AES:HIGH:!NULL:laNULL:!MD5:!ADH:!RC4;
        ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
        ssl_prefer_server_ciphers on;
```

<hr>

## Docker搭建Sentry并结合tornado
<a name='sentry'></a>
*1.下载docker*
> yum install -y yum-utils

> yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo

> yum makecache fast

> yum install docker-ce


*安装指定版本docker*
> yum install docker-ce-<VERSION>

*列出版本*

> yum list docker-ce.x86_64  --showduplicates |sort -r

> yum install python-pip

> pip insatll docker-compose

*2.拉取sentry*
> git clone https://github.com/getsentry/onpremise.git

*3.构建容器并创建数据库和sentry安装目录*
> mkdir -p data/{sentry,postgres}

> cd onpremise

*4.生成secret key并添加到docker-compose文件*
> docker-compose run --rm web config generate-secret-key

*5.重建数据库，并创建sentry超级管理员用户*
> sudo docker-compose run --rm web upgrade

*6.启动所有服务*
> docker-compose up -d

*7.停止docker,重启*
> docker-compose down && docker-compose up -d

<hr>

## Centos下查看cpu信息
<a name='cpu'></a>
*查看CPU型号：cpu型号是E7-4820*
> cat /proc/cpuinfo | grep name | cut -f2 -d: | uniq -c

*查看物理cpu核心数*
> cat /proc/cpuinfo | grep "physical id" | sort | uniq|wc -l

*查看逻辑cpu的个数*
> cat /proc/cpuinfo | grep "processor" |wc -l

*查看cpu是几核*
> cat /proc/cpuinfo | grep "cores"|uniq

<hr>

## flask+gunicorn+gevent+supervisor+nginx部署
<a name='f_s_g'></a>

***

## mongodb分片架构部署
*服务器架构，3台主机服务器*

<a name='mongodb'></a>

***

## centos7安装mysql5.7

<a name='mysql'></a>

*配置 yum 源*
> wget https://dev.mysql.com/get/mysql57-community-release-el7-11.noarch.rpm

> yum localinstall mysql57-community-release-el7-11.noarch.rpm

*检查*
>  yum repolist enabled | grep "mysql.*-community.*"

*安装*
> yum install -y mysql-community-server

*启动*
> systemctl start mysqld

*查看本地账户密码*
> grep 'temporary password' /var/log/mysqld.log

*登录*
> mysql -uroot -p

*修改密码*
> ALTER USER 'root'@'localhost' IDENTIFIED BY 'MyNewPass4!'; 

*推入修改*
> flush privileges;

*允许远程登录*
> use mysql;

> update user set host = '%' where user = 'root';

> grant all privileges on *.* to 'root'@'%' identified by '#######';

> flush privileges;

*修改数据库默认编码格式*
> vim /etc/my.cnf

*查看数据库编码格式*
> show variables like 'character%';

```
[mysqld]
character-set-server=utf8
[client]
default-character-set=utf8
[mysql]
default-character-set=utf8
```

> systemctl stop mysqld

> systemctl start mysqld

---
## cron命令

<a name='cron'></a>

*查看定时任务*

> crontab -l

*编辑定时任务*
> crontab -e

*定时任务编写逻辑*

```
实例1：每1分钟执行一次myCommand
* * * * * myCommand
实例2：每小时的第3和第15分钟执行
3,15 * * * * myCommand
实例3：在上午8点到11点的第3和第15分钟执行
3,15 8-11 * * * myCommand
实例4：每隔两天的上午8点到11点的第3和第15分钟执行
3,15 8-11 */2  *  * myCommand
实例5：每周一上午8点到11点的第3和第15分钟执行
3,15 8-11 * * 1 myCommand
实例6：每晚的21:30重启smb
30 21 * * * /etc/init.d/smb restart
实例7：每月1、10、22日的4 : 45重启smb
45 4 1,10,22 * * /etc/init.d/smb restart
实例8：每周六、周日的1 : 10重启smb
10 1 * * 6,0 /etc/init.d/smb restart
实例9：每天18 : 00至23 : 00之间每隔30分钟重启smb
0,30 18-23 * * * /etc/init.d/smb restart
实例10：每星期六的晚上11 : 00 pm重启smb
0 23 * * 6 /etc/init.d/smb restart
实例11：每一小时重启smb
* */1 * * * /etc/init.d/smb restart
实例12：晚上11点到早上7点之间，每隔一小时重启smb
0 23-7 * * * /etc/init.d/smb restart
```

*日志重定向*

```
每条任务调度执行完毕，系统都会将任务输出信息通过电子邮件的形式发送给当前系统用户，这样日积月累，日志信息会非常大，可能会影响系统的正常运行，因此，将每条任务进行重定向处理非常重要。 例如，可以在crontab文件中设置如下形式，忽略日志输出:

0 */3 * * * /usr/local/apache2/apachectl restart >/dev/null 2>&1
“/dev/null 2>&1”表示先将标准输出重定向到/dev/null，然后将标准错误重定向到标准输出，由于标准输出已经重定向到了/dev/null，因此标准错误也会重定向到/dev/null，这样日志输出问题就解决了。
```

*更换系统时区后重启定时任务*

> service cron restart

*查看log日志*

> tail -f /var/log/cron
