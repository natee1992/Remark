## yum源安装python3.6
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
***

## 修改pip镜像源
1.  新建文件 ~/.pip/pip.conf 
2.  设置
```
 [global]
   index-url = https://pypi.tuna.tsinghua.edu.cn/simple  #清华源
   index-rul = http://pypi.douban.com/simple/    # 豆瓣源
```

***
