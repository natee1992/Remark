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

