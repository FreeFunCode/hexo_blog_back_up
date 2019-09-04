---
title: Hello Hexo
date: 2014-04-27 19:20:47
tags: hexo
categories: Hexo
---
## 安装
* 安装Node.js: Node.js安装包下载地址，选择安装位置，然后安装时一直点next即可。
* 安装Git: Git安装包下载地址,选择安装位置，然后安装时一直点next即可。
* 安装Hexo:设置全局淘宝源，键入命令 `npm config set registry https://registry.npm.taobao.org` 这样就可以加速下载,然后键入命令`npm install -g hexo-cli`,等待安装完成，至此Hexo的环境就搭建好了。
* 在对应的文件夹下键入命令`hexo init Blog`
* 键入命令`hexo s`，本地访问 `http://localhost:4000`
## 常用命令
`hexo clean && hexo g && hexo d`
### 部署需要用到插件
`hexo-deployer-git`
`npm install hexo-deployer-git --save`
## 发布文章
创建文章相关文件：`$ hexo new <title>`  `hexo clean && hexo g && hexo d`

## git常用命令

> `git init` 新建一个空的仓库
> `git status` 查看状态
> `git add . ` 添加文件
> `git commit -m` '注释' 提交添加的文件并备注说明
> `git remote add origin git@github.com:FreeFunCode/hexo_blog_back_up.git` 连接远程仓库
> `git push -u origin master` 将本地仓库文件推送到远程仓库




## 排查
`http://www.yamllint.com/`