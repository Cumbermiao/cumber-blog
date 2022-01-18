## hexo 常用命令

- 创建项目 `hexo init [folder]`

- 创建新文章 `hexo new [layout] <title> -p about/me -r` ； -p 可以自定义路径； -r 替换同名文章；

- 生成静态文件 `hexo g -d -f`； -d 生成之后立即部署； -f 重新生成所有文件；

- 启动本地服务 `hexo server`

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