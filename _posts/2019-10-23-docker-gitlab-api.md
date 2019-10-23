---
title: Docker 搭建 GitLab 添加用户
date: 2019-10-23 20:00
categories: blog
tags: [docker, git]
---

想要使用 Docker 来搭建 GitLab 的话需要先安装 Docker, 官方的说法Desktop版本是最方便的部署方式，安装非常简单就官网下载即可，另外可以设置一下免sudo使用docker也挺方便的。

### pull GitLab CE image

```bash
$ docker pull gitlab/gitlab-ce
```

> 如果需要下载其他版本如： $ sudo docker pull gitlab/gitlab-ce:rc

### Run GitLab Instance

运行之前先在Desktop里边添加一个本机路径

![路径设置](../assets/in-post/2019-10-23-docker-share-dir.png)

```bash
docker run --detach \
  --hostname gitlab.bblu.com \
  --publish 8080:80 --publish 2022:22 \
  --name gitlab \
  --restart always \
  --volume /var/gitlab/config:/etc/gitlab \
  --volume /var/gitlab/logs:/var/log/gitlab \
  --volume /var/gitlab/data:/var/opt/gitlab \
  gitlab/gitlab-ce:latest
```

### Add user private_token

- 给User 添加Access_token

### Add Users by API

```bash
#!/bin/bash
cmp="@sample.com"
while read line
do
    name=`echo $line | awk '{print $1}'`
    username=`echo $line | awk '{print $2}'`
    mail=$username$company
    echo $mail
    curl -d "projects_limit=0&can_create_group=false&password=User@1234&email=$mail&username=$username&name=$name&private_token=i15ZspAj_sVkTxvTUqKG" "http://172.16.19.157:8080/api/v3/users"
done < ./user

# user file
张三 zhangsan
李四 lisi
```