---
title: linux基础-shell脚本开启自启
date: 2023-02-17 08:44:05
updated:
categories:
tags:
---

### 创建启动脚本
```sh
#!/bin/bash
aria2c --conf-path /etc/aria2/aria2.conf -D
```
### 迁移至 init.d 目录
将脚本文件迁移至 `/etc/init.d/` 目录下，并调整文件权限 `chmod 777 aria2.sh`。

### 设置启动
执行命令 `sudo update-rc.d  aria2.sh defaults`。

经过以上步骤即可，重启之后查看是否生效。
