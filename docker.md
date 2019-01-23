## Docker

### Docker简介
docker 是一种容器技术，作用是用来快速部署服务
Docker 提供了一个开发、打包、运行app的平台

### Docker Engine
- 后台进程 （dockerd）
- REST API server
- CLI接口 （docker）

### Docker 部署优势
1. 不用考虑不同机器环境不同问题
2. 生成镜像，pull之后，直接可start运行
3. 方便多服务部署，集群部署docker-compose，或者K8s
4. Dockerfile分层生成，方便修改，将变量放置最后
5. 启动秒级，方便回滚版本

### Docker 核心概念
**镜像image**
镜像是指一个系统的镜像，类似windows的ghost镜像

**性能损耗**
得益于现在的 linux 内核的 namespace， 我们可以拥有各种直达内耗的容器可以用，你在 docker 中的进程其实进程就是直接的宿主机进程，这一切都在系统启动 clone 函数的时候就决定了， 所以谈不上性能损耗。

**容器**
容器是一个镜像image的实例，在运行image后会生成一个实例

**hub**
就是镜像仓库。
如果我们部署，我肯定就要用 dockerhub， 我们把镜像托管到 docker hub 上(当然我们也可以假设，或者是用别人假设的hub)
国内有很多三方 dockerhub 服务器， 有阿里云，网易蜂巢，有容云，daocloud 等等等等
至于国外就是 amazon

### Docker 命令

```

ocker   # docker 命令帮助

Commands:
    attach    Attach to a running container                 # 当前 shell 下 attach 连接指定运行镜像
    build     Build an image from a Dockerfile              # 通过 Dockerfile 定制镜像
    commit    Create a new image from a container's changes # 提交当前容器为新的镜像
    cp        Copy files/folders from the containers filesystem to the host path
              # 从容器中拷贝指定文件或者目录到宿主机中
    create    Create a new container                        # 创建一个新的容器，同 run，但不启动容器
    diff      Inspect changes on a container's filesystem   # 查看 docker 容器变化
    events    Get real time events from the server          # 从 docker 服务获取容器实时事件
    exec      Run a command in an existing container        # 在已存在的容器上运行命令
    export    Stream the contents of a container as a tar archive   
              # 导出容器的内容流作为一个 tar 归档文件[对应 import ]
    history   Show the history of an image                  # 展示一个镜像形成历史
    images    List images                                   # 列出系统当前镜像
    import    Create a new filesystem image from the contents of a tarball  
              # 从tar包中的内容创建一个新的文件系统映像[对应 export]
    info      Display system-wide information               # 显示系统相关信息
    inspect   Return low-level information on a container   # 查看容器详细信息
    kill      Kill a running container                      # kill 指定 docker 容器
    load      Load an image from a tar archive              # 从一个 tar 包中加载一个镜像[对应 save]
    login     Register or Login to the docker registry server   
              # 注册或者登陆一个 docker 源服务器
    logout    Log out from a Docker registry server         # 从当前 Docker registry 退出
    logs      Fetch the logs of a container                 # 输出当前容器日志信息
    port      Lookup the public-facing port which is NAT-ed to PRIVATE_PORT
              # 查看映射端口对应的容器内部源端口
    pause     Pause all processes within a container        # 暂停容器
    ps        List containers                               # 列出容器列表
    pull      Pull an image or a repository from the docker registry server
              # 从docker镜像源服务器拉取指定镜像或者库镜像
    push      Push an image or a repository to the docker registry server
              # 推送指定镜像或者库镜像至docker源服务器
    restart   Restart a running container                   # 重启运行的容器
    rm        Remove one or more containers                 # 移除一个或者多个容器
    rmi       Remove one or more images                 
              # 移除一个或多个镜像[无容器使用该镜像才可删除，否则需删除相关容器才可继续或 -f 强制删除]
    run       Run a command in a new container
              # 创建一个新的容器并运行一个命令
    save      Save an image to a tar archive                # 保存一个镜像为一个 tar 包[对应 load]
    search    Search for an image on the Docker Hub         # 在 docker hub 中搜索镜像
    start     Start a stopped containers                    # 启动容器
    stop      Stop a running containers                     # 停止容器
    tag       Tag an image into a repository                # 给源中镜像打标签
    top       Lookup the running processes of a container   # 查看容器中运行的进程信息
    unpause   Unpause a paused container                    # 取消暂停容器
    version   Show the docker version information           # 查看 docker 版本号
    wait      Block until a container stops, then print its exit code   
              # 截取容器停止时的退出状态值
Run 'docker COMMAND --help' for more information on a command.
```

*docker命令实例*

*docker生成新image*
> docker commit -m 'commit message'  container_id 'name'

*docker复制文件*
> docker cp 文件名  container_id /地址

*docker删除镜像*
> docker rmi image_id

*docker停止容器*
> docker stop comtainer_id 

*docker退出容器不停止*
> ctrl + p + q

*docker进入容器*
> docker exec -it container_id /bin/bash

> ssh root@localhost -p 映射端口号

**docker 外部命令**
查找镜像
> docker search XX

下载镜像
> docker pull X

查看所有镜像
> docker images

删除镜像
> docker rmi

查看所有容器
> docker ps -a

删除容器
> docker rm x

启动容器
> docker run/start

查看日志
> docker logs x

查看最近50行日志
> docker logs -f -t --tail 50 8ffe6610c91a

## Dockerfile
*docker 时区问题+8或者-8*
> RUN cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime \
> && echo 'Asia/Shanghai' >/etc/timezone

example
```
ROM freedoms1988/centos7-sshd
MAINTAINER zb <xxx@mail.com>

RUN yum install -y gcc
RUN yum install -y epel-release
RUN yum -y update
RUN yum install -y https://centos7.iuscommunity.org/ius-release.rpm
RUN yum install -y python36u
RUN yum install -y python36u-pip
RUN yum install -y python36u-devel python-devel supervisor

RUN ln -s /bin/python3.6 /bin/python3 && ln -s /bin/pip3.6 /bin/pip3 && mkdir /root/web

COPY requirements.txt /root/web/

RUN pip3 install -r /root/web/requirements.txt -i https://pypi.tuna.tsinghua.edu.cn/simple

COPY supervisor.ini /etc/supervisord.d/
COPY web /root/web/
RUN cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime \
&& echo 'Asia/Shanghai' >/etc/timezone

CMD ["/usr/bin/supervisord"]

```
## docker-compose

**简介**
docker-compose 是用来做docker 的多容器控制
docker-compose 是一个用来把 docker 自动化的东西。
有了 docker-compose 你可以把所有繁复的 docker 操作全都一条命令，自动化的完成



