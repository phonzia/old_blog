---
title: Docker使用官方mysql镜像
layout: post
categories: ''
tags: ''
---
# 简介
本文简单介绍了如何使用 docker 在 macOS 上搭建 mysql 数据库环境，其中使用了docker官方的 mysql 镜像.

<!--more-->

# 安装 docker
[docker的官网](http://www.docker.com/)现在提供了 mac 版本的 docker 安装文件下载. 只要简单下载安装就行，这里不再多说.

# 配置加速镜像
docker hub 在国内的网络下速度实在不行，这里我们需要配置一下加速器，以便拉取镜像。我使用的是 DaoCloud 的[加速镜像](https://www.daocloud.io/mirror#accelerator-doc).

# 拉取镜像
先使用以下指令拉取镜像

    docker pull mysql:latest
    
通过以下命令可以查看本地镜像：

    docker images

# 创建 mysql 服务
详细的文档可见 [docker hub](https://hub.docker.com/_/mysql/). 这里只简单介绍创建和访问的命令。

    docker run --name some-mysql -e MYSQL_ROOT_PASSWORD=my-password -d mysql:latest

其中 some-mysql 为容器名， my-password 为该 mysql 的 root 用户密码。

要通过命令行访问的话，可以用以下方式：

    docker run -it --link some-mysql:mysql --rm mysql sh -c 'exec mysql -h"$MYSQL_PORT_3306_TCP_ADDR" -P"$MYSQL_PORT_3306_TCP_PORT" -uroot -p"$MYSQL_ENV_MYSQL_ROOT_PASSWORD"'
    
同样的，其中 some-mysql 是容器名。

为了不在容器中访问，我们可以在之前创建容器的命令加入端口映射:

    docker run --name some-mysql -e MYSQL_ROOT_PASSWORD=my-password -d mysql:latest -p 3306:13555
    
这样我们就可以通过本机的13555端口访问到我们创建容器内的 mysql 数据库.