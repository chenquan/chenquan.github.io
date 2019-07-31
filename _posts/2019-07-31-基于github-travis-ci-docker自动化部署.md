---
layout: post
title: 基于Github Travis-ci Docker自动化部署
categories: [Travis-ci]
tags:
- Github 
- Docker
description: 基于Github Travis-ci Docker自动化部署

---



# 基于Github Travis-ci Docker自动化部署



### 实现思路

1. `Dockerfile` + `docker-compose`来构建docker容器
2. `travis-ci` + `github` 来hook `repo`的变动
3. `travis-ci` 调用 `Dockerfile`打包 `docker image`并push到`dockerhub`
4. `travis-ci` ssh 登录到目标机器,copy `docker-compose`并执行来完成部署

#### docker容器构建

由于项目是基于`java`+`gradle`来构建的,所以`dockerfile`需要进行多阶段构建

先build出fat-jar来:

```yaml
#以gradle 为基础构建build环境
FROM gradle:5.2.1-jdk8-alpine AS build-env 
#copy源码
ADD --chown=gradle . /app
WORKDIR /app
#运行gradle task进行build
RUN gradle assemble --info

```

将jar包copy到jre环境:

```yaml
FROM openjdk:8-jre
# 时区
RUN ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime \
  && echo Asia/Shanghai > /etc/timezone \
  && dpkg-reconfigure -f noninteractive tzdata
# port
EXPOSE 9000
#copy
COPY --from=build-env /app/build/libs/xxx.jar /app/bin/xxx.jar
#运行
CMD ["java", "-jar", "/app/bin/xxx.jar"]

```

这里需要注意的是`ADD`的时候要授予权限,否则gradle命令执行不了，多阶段构建需要声明`--from`的上一个阶段,否则copy不到jar包。

#### docker-compose.yaml

```yaml
version: '2'
services:
  xxx-service:
    image: xxx:latest
    container_name: xxx
    environment:
      - JAVA_TOOL_OPTIONS=-Xms128m -Xmx256m
    ports:
      - "9000:8080"

```

#### .travis.yaml

```yaml
language: bash
services:
  - docker
sudo: required
branches:
  only:
    - master
script:
  - docker build . -t "xxx:latest"
  - docker-compose -f docker-compose.yaml down
  - docker-compose -f docker-compose.yaml up -d --force-recreate 
  - docker-compose -f docker-compose.yaml down

after_success:
  - docker login --username=username -p="password" dockerhub
  - docker push dockerhub:latest
  - chmod 600 id_rsa
  - scp -o "StrictHostKeyChecking no" -i id_rsa docker-compose.yml ubuntu@ip:/home/ubuntu/docker/
  - ssh -o "StrictHostKeyChecking no" -i id_rsa ubuntu@ip "cd /home/ubuntu/docker/;sudo docker-compose -f docker-compose.yml pull;sudo docker-compose -f docker-compose.yml up -d;exit"


```



分解一下:

- 声明language:bash
- services选择docker环境
- script 启动docker image build
- after_success构建完成之后,登录dockerhub,push image,登录服务器并cp执行docker-compose.
- ssh 后跟的"命令"会在登录成功后执行

敏感信息这里可以通过`Travis CI`的环境变量来设置,可以避免是公开仓库而泄露 ssh_key可以使用`Travis CI`的加密key来避免泄露

