---
title: lerna version
date: 2022-02-10 19:50:28
updated:
categories:
tags:
---


# @lerna/version

## 使用
```
lerna version 1.0.1 # explicit
lerna version patch # semver keyword
lerna version       # select from prompt(s)
```

当执行该命令时会做以下操作：
1. 识别上次版本发布以来所更新的 packages。
2. 确认新的版本号。
3. 修改 package 的元数据映射至新的发布，在根目录及每个 package 的目录执行对应的生命周期脚本。
4. 将所有的 tags 和 changes 提交为一个 commit。
5. 推送至 git 远程仓库。

## 定版词

### 语义版本升级

```
lerna version [major | minor | patch | premajor | preminor | prepatch | prerelease]
# uses the next semantic version(s) value and this skips `Select a new version for...` prompt
```
当传递定版词参数，`lerna version`会跳过选择版本的确认过程，使用该参数值升级版本。
你仍然需要使用 `--yes` 标识来跳过所有的确认过程。

## 预发布

如果仓库有任意一个预发布的 package （例如版本号为 2.0.0-beta.3）并在执行 `lerna version` 时带上了一个非预发布的定版词 (`major`, `minor`, `patch`)，那么这个预发布版本的 package 会和其他需要发布的 package 一样都会被发布。

对于使用了约定式提交的项目，可以使用以下标识来预发布：
- `--conventional-prerelease`: 将当前改动发布为预发布版本。
- `--conventional-graduate`: 将预发布版本的 packages 升级为稳定版本。

不带上以上标识直接执行命令 `lerna version --conventional-commits` 时，只有当前已经是预发布版本时才会将改动发布为预发布版本。

## 选项

### `--allow-branch <glob>`

白名单，只有匹配的 git 分支才能执行 `lerna version`。最简单的方式（也是最推荐的）是在`lerna.json`中配置，当然也可以在命令行中传入参数。
```json
{
  "command": {
    "version": {
      "allowBranch": "main"
    }
  }
}
```
使用上面的配置，如果当前的分支不是 `main` 时， `lerna version`会失败。将`lerna version`限制在主分支上是被认为最佳实践。

```json
{
  "command": {
    "version": {
      "allowBranch": ["main", "feature/*"]
    }
  }
}
```
使用上面的配置，`lerna version` 可以在以 `feature/` 为前缀的分支上执行。请注意，在功能分支中生成 git 标记充满了潜在的错误，因为这些分支会被合并到主分支中。如果从它们原来的上下文（可能是一个合并的merge或者一个解决冲突的merge），未来`lerna version`执行时会难以定位到正确的"上次发布以后的变动"。

通过命令行参数可以覆盖配置，但请谨慎使用。
`lerna version --allow-branch hotfix/oops-fix-the-thing`

### `--amend`
```
lerna version --amend
# commit message is retained, and `git push` is skipped.
```
使用该标识时， `lerna version` 会把所有变动提交至当前的 commit，而不会创建一个新的 commit。这对在使用CI减少项目的 commits 记录会非常有用。
为了防止错误的覆盖，该命令会跳过 `git push` （实际采用了 `--no-push`）。

### `--changelog-preset`
```
lerna version --conventional-commits --changelog-preset angular-bitbucket
```
