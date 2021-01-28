## heroku deploy

1. `npm i hexo-deployer-heroku`

2. modify _config.yml

```yml
url: http://cumber-blog.herokuapp.com/

deploy:
  type: 'heroku'
  repo: 'https://git.heroku.com/cumber-blog.git'
  message: ''
```

3. package.json script

```
"start": "hexo server -p $PORT"
```

## npx hexo d 失败报错

1. 
```
ERROR: Application not supported by 'heroku/nodejs' buildpack
The 'heroku/nodejs' buildpack is set on this application, but wasunable to detect a Node.js codebase
```

执行 `buildpacks:clear`

## WARN no layout

查看 theme 中的主题是否存在， 有可能是 git 没有同步 theme 的主题文件夹。

## git push heroku master warning

```
remote: ! WARNING:
remote: ! Do not authenticate with username and password using git.
remote: ! Run `heroku login` to update your credentials, then retry the git command.
remote: ! See documentation for details: https://devcenter.heroku.com/articles/git#http-git-authentication
```

用户名使用 blank
密码使用 token， 可通过命令 heroku auth:token 获取