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