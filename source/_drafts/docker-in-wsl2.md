---
title: windows11 WSL2 安装 docker
date: 2022-07-30 09:57:54
updated:
categories:
tags:
---

参考文章
- [Windows 11 安装 WSL2](https://zhuanlan.zhihu.com/p/475462241)


## wsl2 升级及 Ubuntu 安装

基本安装参考上面文章进行操作，下面记录安装遇到的问题及解决方法。

1. 安装好 Ubuntu 之后进入报错 WslRegisterDistribution failed with error: 0x800701bc ，原因是 wsl1 升级到之后内核未升级。 解决方法： 下载最新的wsl安装包，下载地址：https://wslstorestorage.blob.core.windows.net/wslblob/wsl_update_x64.msi； 下载后直接安装即可。


## Ubuntu 存储位置迁移

1. 关闭 wsl `wsl --shutdown`
2. 导出需要迁移的Ubuntu 系统 `wsl --export Ubuntu D:\ubuntu\ubuntu.tar`
3. 卸载原来的 Ubuntu `wsl --unregister Ubuntu`
4. 导入迁移的系统 `wsl --import Ubuntu D:\ubuntu D:\ubuntu\ubuntu.tar --version 2`

## docker 安装

1. 下载 docker desktop 并安装。