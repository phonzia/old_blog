---
title: virtualenv和django的安装
layout: post
categories: ''
tags: ''
---
# 简介

本文介绍 virtualenv 和在 virtualenv 环境下安装 Django. 本文均采用最简便方式安装，如果需要其他安装方式，请参考官方文档.

<!--more-->

# virtualenv 的安装和使用
VirtualEnv用于在一台机器上创建多个独立的python运行环境，有利于隔离项目的第三方包依赖，比如不同项目使用不同版本的包，也可以为部署应用提供方便。

我们使用 pip 的方式安装 virtualenv:

    pip install virtualenv
    
安装完成之后，使用以下命令可以创建一个新的 virtualenv 环境：

    virtualenv ENV

其中 ENV 为存放环境的目标文件夹.

进入目标文件夹之后，使用以下命令激活环境：

    source bin/activate
    
这个时候如果使用 pip 安装新的依赖包，则会安装在这个 virtualenv 环境，而不是安装到全局的 python 环境. 如果要退出 virtualenv环境，则执行以下指令：

    deactivate
    
# Django 安装

一般来说不推荐将 Django 安装到全局环境，所以在我们可以激活创建好的 virtualenv 环境之后，执行:

    pip install Django
    
这样 Django 就安装好了，Let the games begin!