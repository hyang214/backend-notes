# Docker 问题记录

## 时区不正确
+ 时间：2018.04.08
+ 场景：docker 容器启动的 java 应用中的时间和本地时间不一致，不是东8的时区，而是UTC
+ 原因：基础的alpine镜像是从Docker Hub拉取的，默认时区是UTC
+ 解决：
```
ENV TZ=Asia/Shanghai
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone
```
+ Reference: [Docker Container time & timezone (will not reflect changes)](https://serverfault.com/questions/683605/docker-container-time-timezone-will-not-reflect-changes)
