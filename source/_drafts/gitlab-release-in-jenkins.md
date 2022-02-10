---
title: 在 jenkins 任务中触发 gitlab 项目 release
date: 2022-02-10 19:51:31
updated:
categories:
tags:
---

## 前言

原本想要做的自动发布是在 gitlab 中配置 tag push webhook 触发 jenkins release job，job 中执行 npm run release 生成 CHANGELOG、tag，然后执行打包命令生成 dist 文件夹，触发 gitlab release api 并在 release 中增加 dist 压缩包为 asset link （jenkins 存储 dist 为打包产物）。

但是在 jenkins 执行 release 命令时， lerna 会执行 git remote update 需要输入用户名及密码，导致 jenkins 任务报错。于是将 release 命令让用户手动执行。


## 实现

### 配置 webhook

jenkins 需要安装 gitlab 插件。
![](/images/jenkins-gitlab.jpg)

gitlab 中配置 jenkins 中对应的 url 和 secret，webhook 触发的历史可以点击 edit 查看。
![](/images/gitlab-webhook.jpg)

### lerna version

本项目使用的是 lerna 管理， lerna version 使用可以参考[文档](/2022/02/10/lerna-version/)

### jenkins 配置

![](/images/jenkins-mf-release1.jpg)
![](/images/jenkins-mf-release2.jpg)

以上在配置仓库时, 通过 Refspec 获取仓库所有的 tag，执行shell如下：
```bash
#!/bin/sh

echo $tag
echo $BUILD_NUMBER
echo "ready to install"
npx pnpm install
npm run bootstrap
$build_cmd
curl --header 'Content-Type: application/json' --header "PRIVATE-TOKEN: KwqyDk3dzx7EnET-SPXh" --data '{ "name": "mf release", "tag_name": "'$tag'", "description": "released by jenkins" }' --request POST http://10.3.7.241/api/v4/projects/57/releases 
curl --header "PRIVATE-TOKEN: KwqyDk3dzx7EnET-SPXh" --data name="archive.zip" --data url="http://10.3.7.241:8984/job/fe-release-mf/"$BUILD_NUMBER"/artifact/*zip*/archive.zip" --request POST http://10.3.7.241/api/v4/projects/57/releases/v3.1.0/assets/links
```