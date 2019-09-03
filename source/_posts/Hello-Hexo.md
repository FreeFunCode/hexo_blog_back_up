---
title: Hello Hexo
date: 2019-09-02 19:20:47
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

## 排查
`http://www.yamllint.com/`