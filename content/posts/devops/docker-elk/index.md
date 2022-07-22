---
title: docker-compose 安装docker-elk
description: docker-compose 安装docker-elk
date: 2022-07-22
tags:
- "devops"
---
<!--more-->
## docker-compose 安装docker-elk

### 安装docker-compose

前置条件:[安装docker]([天涯屐痕的博客 (cnblogs.com)](https://www.cnblogs.com/yanshaoshuai/p/15811826.html))

```shell
 sudo apt-get update
 # 查看可用版本
 apt-cache madison docker-compose-plugin
 # 安装特定版本
 sudo apt-get install docker-compose-plugin=<VERSION_STRING>
 docker compose version
```



### 下载docker-elk

```sh
# https://github.com/deviantony/docker-elk/tags 选择特定版本
wget https://github.com/deviantony/docker-elk/archive/refs/tags/8.2206.1.tar.gz
tar -xzvf 8.2206.1.tar.gz
```

### 用docker-compose 启动docker-elk

进入解压目录,修改docker-compose.yaml文件,主要是修改密码,否则启动会报格式错误

按如下格式修改一遍

${KIBANA_SYSTEM_PASSWORD:-}=>${KIBANA_SYSTEM_PASSWORD}

.env就是环境变量的真实值,有要自定义的值可以在这里改。

启停:

```sh
# 前台启动
docker compose up
# 后台启动
docker compose up -d
# 停止不删除持久化数据
docker compose down
# 停止并且删除持久化数据
docker compose down -v
```

默认用户名是elastic/changeme,打开ip:5601地址即可登陆kibana。