---
title: Docker常用命令
toc: true
date: 2019-01-05 19:56:29
tags: [技术开发]
categories:
---


## 常用命令总结

```bash
# -t 表示 tag, -f 表示 file, 默认是 Dockerfile, 最后的 . 不要忘了
docker build -t ubuntu-16.04:v1 -f Dockerfile.gpu .

docker ps -a
docker image ls
docker rm container_id
docker rmi image_id
docker run -v /path/of/host:/path/in/constainer -p /port/of/host:/port/of/container -it image_id [command]
docker pull image_in_dockerhub
docker commit -a author -m message container_id repository:tag

# -a 表示 attach, -i 表示 iterative
docker start -a -i container_id

# -f 表示 force
docker stop
docker kill -f
```




## 参考资料
> - []()
> - []()
