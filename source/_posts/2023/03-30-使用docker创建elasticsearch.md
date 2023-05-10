---
layout: post
title: 使用docker创建elasticsearch
date: 2023-03-30 16:28:40
tags:
---


## 官网地址
[https://www.elastic.co/guide/en/elasticsearch/reference/current/docker.html#_install_docker_desktop_or_docker_engine](https://www.elastic.co/guide/en/elasticsearch/reference/current/docker.html#_install_docker_desktop_or_docker_engine)

## 安装elasticsearch

1. 下载elasticsearch8
```
docker pull docker.elastic.co/elasticsearch/elasticsearch:8.6.1
```

2. 为 Elasticsearch 和 Kibana 创建新的 docker 网络
```
docker network create elastic
```

3. 运行elasticsearch  
容器启动后会生成一个有限期30分钟的token，用于后续的kibana的登录，如果没有跳至第6步  
```
docker run --name es01 --net elastic -p 9200:9200 \ 
    -v plugins:/usr/share/elasticsearch/plugins \ 
    -it docker.elastic.co/elasticsearch/elasticsearch:8.6.1
```

4. 开启新窗口设置密码或重置密码
``` shell
# 使用以下命令重置一个随机生成的密码
docker exec -it es01 /usr/share/elasticsearch/bin/elasticsearch-reset-password -u elastic

# 加上-i参数指定密码
docker exec -it es01 /usr/share/elasticsearch/bin/elasticsearch-reset-password -u elastic -i
```

5. 检查安装
打开https://127.0.0.1:9200，输入用户名(`elastic`)和密码登录，成功后出现如下信息表示安装并登录成功
``` json
{
    "name": "4004c87cf02c",
    "cluster_name": "docker-cluster",
    "cluster_uuid": "Qu4Gnl3nT5i3BUtvwAjuFA",
    "version": {
        "number": "8.6.1",
        "build_flavor": "default",
        "build_type": "docker",
        "build_hash": "180c9830da956993e59e2cd70eb32b5e383ea42c",
        "build_date": "2023-01-24T21:35:11.506992272Z",
        "build_snapshot": false,
        "lucene_version": "9.4.2",
        "minimum_wire_compatibility_version": "7.17.0",
        "minimum_index_compatibility_version": "7.0.0"
    },
    "tagline": "You Know, for Search"
}
```


6. 生成token
```
docker exec -it es01 /usr/share/elasticsearch/bin/elasticsearch-create-enrollment-token -s kibana
```

## 安装kibana

地址：[https://www.elastic.co/guide/en/kibana/8.6/docker.html#run-kibana-on-docker-for-dev](https://www.elastic.co/guide/en/kibana/8.6/docker.html#run-kibana-on-docker-for-dev)

1. 下载kibana并运行
``` shell
docker pull docker.elastic.co/kibana/kibana:8.6.1
docker run --name kib01 --net elastic -p 5601:5601 docker.elastic.co/kibana/kibana:8.6.1 -d
```

2. 检查安装
打开http://127.0.0.1:5601，输入上面生成的token，连接至elastic

3. 输入验证码
![https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/8bea167f0be2479fbfa30597bf0558a0~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/8bea167f0be2479fbfa30597bf0558a0~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp)
在控制台中输入以下命令，拿到验证码
``` shell
docker exec -it kib01 bin/kibana-verification-code
```

4. 输入elastic的账号密码进行登录


## 安装分词器
``` shell
# 进入容器内部
docker exec -it es01 /bin/bash

# 下载分词器 注意插件版本要和elastic的版本对应，在GitHub中找
elasticsearch-plugin install https://github.com/medcl/elasticsearch-analysis-ik/releases/download/v8.6.1/elasticsearch-analysis-ik-8.6.1.zip

# 退出容器，重启服务
docker restart es01
```
