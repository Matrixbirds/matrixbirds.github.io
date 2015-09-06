title: 使用hexo搭建个人博客
date: 2015-09-06 10:41:31
tags:
---
### 简介

  hexo 是一个基于nodejs实现的静态博客框架,具有本地构建项目,以及发布到git得功能


### 搭建步骤
```bash
- 安装git环境  
- npm install -g hexo  
- cd /deploy_directory && hexo init ## 生成hexo的项目文件
- npm install ## 安装hexo的项目依赖
```


### hexo 基本命令
```bash
- hexo new "文章名" ## 新建文章  
- hexo generate ## 生成hexo资源文件  
- hexo deploy   ## 发布hexo  
- hexo server   ## 启动hexo本地服务  
```

### hexo的发布和部署

 - hexo分为两种目录结构
  - 发布结构: `带有发布目录以及_config.yml 部署配置文件`
  - 部署结构: `只带有静态文件 并没有部署文件 (该结构只有在执行hexo deploy时会生成)`

因此本文采用了两种分支来分别存放这两种不同的结构

  master: 分支存放部署结构
  deployer: 分支存放发布结构


### 部署到github, gitcafe
1. 修改当前项目根目录的package.json中的部署文件
   ```json
   {
     "name": "你项目的名称(随意填)",
     "version": "0.0.0(版本同上)",
     "private": true,
     "hexo": {
       "version": "3.1.1"
     },
     "dependencies": {
       "hexo": "^3.1.0",
       "hexo-generator-archive": "^0.1.2",
       "hexo-generator-category": "^0.1.2",
       "hexo-generator-index": "^0.1.2",
       "hexo-generator-tag": "^0.1.1",
       "hexo-renderer-ejs": "^0.1.0",
       "hexo-renderer-stylus": "^0.3.0",
       "hexo-renderer-marked": "^0.2.4",
       "hexo-server": "^0.1.2",
       "hexo-deployer-git": "0.0.4"
     }
   }  
   ```  

2. 首先需要分别在github, gitcafe上建立一个repository

  github上repository的名字为[username].github.io
  gitcafe上repository的名字为[username]

  github上需要部署到`master`分支, gitcafe上则需要部署到`gitcafe-pages`分支  

3. 修改根目录下面的: `_config.yml`  
  把`deploy`部分替换成如下  
  ```yml
  deploy:  
    type: git  
    repo:  
      github: git@github.com:Matrixbirds/matrixbirds.github.io.git,master  
      gitcafe: git@gitcafe.com:Matrixbirds/matrixbirds.git,gitcafe-pages  
    message: "deploy both of on github && gitcafe"  
  ```

4. 执行`hexo generate && hexo deploy`生成静态文件并且发布到目标项目地址的目标分支

5. 访问url进行测试 [http://matrixbirds.github.io](http://matrixbirds.github.io), [http://matrixbirds.gitcafe.io](http://matrixbirds.gitcafe.io)

6. 如果上述测试没问题则可以push发布版本到`deployer`分支

7. 如果在`hexo deploy`之前当前目录存在`.deploy_git目录`则应当先删除该目录后再执行发布命令
