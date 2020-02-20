---
title: log this hexo setup
date: 2019-12-08 10:10:29
tags:
- Hexo
---

## setup your hexo blog with github pages hosting

### install hexo
first, install these two npm packages: hexo-cli, hex-deployer-git 
```
npm install -g hexo-cli
npm install hexo-deployer-git --save
```

### create a hexo project
create a hexo project with the same name as your github repo
```
hexo init ocre.github.io
```
create a new post
```shell script
hexo new "my first hexo blog"
```

verify your hexo project:
```
hexo server
```
and you will get a running hexo site at http://localhost:4000/

### create github pages repo
create a new github repoistory named as 'your_github_username'.github.io

### deploy to github
modify git config in _config.yml
```shell script
# Deployment
## Docs: https://hexo.io/docs/deployment.html
deploy:
  type: git
  repo: git@github.com:ocre/ocre.github.io.git
  branch: master
```
test deploy
```shell script
hexo generate
hexo deploy
```
verify it via [https://ocre.github.io](https://ocre.github.io)

Mission Complete!
