## Docker

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

