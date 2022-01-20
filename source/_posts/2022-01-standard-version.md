---
title: standard-version 入门
tags: release
date: 2022-01-18 16:08:31
updated:
categories:
  - release
  - changelog
  - conventional commit
---


## 前言

> 梳理工作流时，希望项目在发布时能够根据 commit 记录自动生成 changelog， 于时调研了 standard-version。

## 介绍

> standard-version 是一个版本控制的工具，它使用基于 Conventional Commits 约定的 semver 和 CHANGELOG 。

## 如何使用

首先你的仓库需要遵从 Conventional Commits 规范，当你准备发布时，执行 `standard-version` 命令。
执行该命令时，会做以下处理：

1. 通过查看 `packageFiles` 检查当前版本，返回到最后一个 git tag；
2. 基于 commits 调整 `bumpFiles` 中的版本；
3. 基于 commits 生成 changelog；
4. 创建一个新的 commit，该 commit 包含 `bumpFiles` 中的文件和更新后的 CHANGELOG 文件；
5. 创建一个新的 tag；

- `packageFiles`: 定义版本的文件，默认为`package.json`；在不指定版本时，`standard-version`会从该文件读取当前版本号，并且发布时会修改该文件中的版本号；如果读取不到，会报错；
- `bumpFiles`：需要修改版本号的文件；可以为多个；

## 配置

配置文件支持`.versionrc`, `.versionrc.json`, `.versionrc.js`。

```js
module.exports = {
  "types": [
    { "type": "feat", "section": "✨ Features | 新功能" },
    { "type": "fix", "section": "🐛 Bug Fixes | Bug 修复" },
    { "type": "docs", "section": "✏️ Documentation | 文档" },
    { "type": "style", "section": "💄 Styles | 风格" },
    { "type": "refactor", "section": "♻️ Code Refactoring | 代码重构" },
    { "type": "perf", "section": "⚡ Performance Improvements | 性能优化" },
    { "type": "test", "section": "✅ Tests | 测试" },
    { "type": "revert", "section": "⏪ Revert | 回退", "hidden": true },
    { "type": "build", "section": "📦‍ Build System | 打包构建" },
    { "type": "chore", "section": "🚀 Chore | 构建/工程依赖/工具" },
    { "type": "ci", "section": "👷 Continuous Integration | CI 配置" }
  ],
  skip: {
    bump: false,
    changelog: false,
    commit: false,
    tag: false
  },
  debug: true,
  dryRun: true,
  packageFiles: [{ filename: 'package.json', type: "json" }],
  bumpFiles: [
    { filename: 'package.json', type: "json" },
    { filename: 'package-lock.json', type: "json" }
  ],
  scripts: {
    "prebump": "echo 9.9.9"
  }
}
```

### 生命周期脚本

允许在 release 过程中执行自定义的命令；支持周期如下：
- `prerelease`: 在最开始执行，如果该脚本返回一个非零状态码，发布过程将被中断；
- `prebump` `postbump`: 在修改版本之前/之后执行；如果 `prebump` 返回一个版本，将会使用该版本；
- `prechangelog` `postchangelog`: 在生成 CHANGELOG 之前/之后执行；
- `precommit` `postcommit`: 在 commit 之前/之后执行；
- `pretag` `posttag` : 在 tag 之前/之后执行；

### 跳过生命周期

`skip` 可以允许执行时跳过对应的阶段，支持 `bump` `changelog` `commit` `tag`;

### dryRun

当设置 `dryRun` 为 `true` 时，你可以在命令行看到命令所执行的结果，而不会改变原有仓库；

## 版本号

默认情况下，`standard-version` 会根据 commit 记录自动计算发布的版本号；以下以 1.0.0 版本为例：
- 当 commit 记录包含 fix 时，`1.0.0` -> `1.0.1`;
- 当 commit 记录包含 feat 时，`1.0.0` -> `1.1.0`;
- 当 commit 记录包含 BREAKING CHANGE 或者 ! 时，`1.0.0` -> `2.0.0`;