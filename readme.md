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