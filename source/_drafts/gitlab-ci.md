---
title: gitlab-ci 实践
date: 2021-03-10 11:49:46
updated: 2021-03-10 11:49:46
categories: gitlab
tags:  
  - ci
---

## 基于 docker 搭建 CI 平台

### 安装 gitlab-runner

参考[官方文档](https://docs.gitlab.com/runner/install/docker.html)

### 注册 runner

参考[官方文档](https://docs.gitlab.com/runner/register/index.html#docker)

token 在 gitlab setting->CI/CD 中获取。

### .gitlab-ci.ymal 配置

参考[官方文档](https://docs.gitlab.com/ee/ci/yaml/README.html#onlyexcept-basic)

### deploy 免密登录

参考[文档](https://cloud.tencent.com/developer/article/1661095)

## 详细过程

1. 使用 docker 创建 gitlab-runner 容器之后，默认情况下退出时容器会自动停止，可以带上 --restart always 参数；
2. gitlab-runner 容器运行后使用 `docker exec gitlab-runner gitlab-runner register` 注册 runner；
3. runner 注册完之后需要手动运行，执行`docker exec gitlab-runner gitlab-runner run`命令；
4. 对于一些没有用的 runner 可以执行命令 `gitlab-runner verify --delete`进行验证， --delete 参数可以删除无用 runner；

### FAQ

1. scp 报错 `Host key verification failed` ，有可能是远程的 ip 未加入到 docker 容器中的 `know_hosts` 中， 可以使用命令 `docker exec gitlab-runner ssh-keygen -R 10.3.7.231` 先清空，再执行 `docker exec gitlab-runner ssh 10.3.7.231` 重新将 ip 加入到 `know_hosts` 中。 如不行，可以将系统的 `know_hosts` 重置。