---
title: 基于 gitlab flow 的工作流
date: 2022-01-06 11:49:46
updated: 2022-01-06 11:49:46
categories: workflow
tags:  
  - ci
  - cd
  - workflow
---

## 前言

在初期，前端是基于 webpack 搭建的 vue 项目，代码管理流程使用的是 git flow 。当时能够满足产品和项目的管理需求。但随着功能模块不断增多，前端项目愈发庞大，后来根据业务模块对项目进行了微应用拆分，并使用了 lerna 管理应用，前端团队也跟着业务被拆分到两个组，根据之前的代码管理经验，对现有项目的代码管理采用了 gitlab flow 。

参考资料：
- [高效团队的gitlab flow最佳实践](https://cloud.tencent.com/developer/article/1784988)
- [gitlab 官方文档](https://docs.gitlab.com/ee/topics/gitlab_flow.html)


## 现状

1. 前端代码管理以前采用 git flow 的方式；
2. 后端代码管理采用类似 gitlab flow 的方式；
3. 前后端都是采用 mono repo 的方式管理；

## 问题

1. 使用 git flow 的方式：分支多，无法确认 dev|release|fix 分支是否合并；
2. commit 不够规范，不统一；
3. 代码提交无人审核；


## 需求

1. 前后端代码管理方式统一，开发测试人员统一操作；
2. 简化代码管理操作；
3. 统一规范 commit 提交；
3. 增加代码审查环节；

## git flow

![git flow 流程](/images/git-flow.png)

## gitlab flow 介绍

![gitlab flow 分支管理](/images/gitlab-flow2.jpg)
![gitlab flow 整体流程](/images/gitlab-flow1.jpg)
![gitlab flow fork仓库管理](/images/gitlab-flow3.jpg)


## merge request 规范

1. 创建 MR 时， 标题使用 `WIP: `标识当前 MR 功能处于开发状态中，开发结束后删除该标识；

2. 建议勾选 `Delete source branch when merge request is accepted.` 和 `Squash commits when merge request is accepted. ` 选项；

3. 功能开发完毕之后，开发人员需要通知代码审查人员对 MR 中的代码进行审查。审查人员按照代码规范可以对存在的问题进行逐行评论建议，开发人员按照建议修改代码，审查人员确认无误后执行合并操作；

4. 合并之前，如果提示 MR 需要 rebase，直接点击 rebase 即可，如果 rebase 失败，需要通知开发人员同步 fork 仓库与原仓库的更新，推送之后再次合并；

5. 合并时，如果 MR 中存在多个 commit，勾选合并 commit 记录选项，按照 commit 格式规范填写信息，信息应尽量详细，最好包含每个子应用修改的内容。建议在 commit 信息中手动指定关联的 MR `related MR !7`；如果只存在一个 commit，需要将 MR 标题修改为符合规范的 commit 格式；

