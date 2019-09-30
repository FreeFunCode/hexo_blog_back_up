---
title: mac搭建hexo环境
date: 2019-09-30 15:47:50
tags: hexo
categories: GitHub
---

## mac搭建hexo环境

### 1. 安装homebrew
##### 1.1 Mac OS X 10.11系统以后，否则跳至1.2先授权。		
安装命令：
```
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```
	
##### 1.2 授权:

```
$ sudo chown -R $USER /usr/local
```

### 2.安装npm
##### 2.1 命令安装：

`$  brew install npm `

##### 2.2 pkg文件下载安装，需要设置环境变量：

创建：`touch ~/.bash_profile`
打开：`open -t ~/.bash_profile `
打开的文档里末尾加上：`export PATH=/usr/local/bin:/usr/bin:/bin:/usr/sbin:/sbin `
执行：`source .bash_profile`
		 
### 3. 安装hexo 
##### 3.1 设置淘宝镜像，安装hexo

```
npm install -g cnpm --registry=https://registry.npm.taobao.org
cnpm install -g hexo-cli
```

##### 3.2 如果权限报错（`npm ERR! Error: EACCES: permission denied, access '/usr/local/lib/node_modules'`）

修改安装目录的权限： `sudo chown -R $(whoami) $(npm config get prefix)/{lib/node_modules,bin,share}`


## 总结：
 如果抛开安装过程中遇到的权限问题，整个安装过程命令：

```
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew install git
brew install node
npm install -g cnpm --registry=https://registry.npm.taobao.org
cnpm install -g hexo-cli
```

Hexo init命令：
```
hexo init hexo_blog
cd hexo_blog
cnpm install
```

目前用到的两个插件：
   * cnpm install hexo-deployer-git --save
   * cnpm install --save hexo-tag-aplayer
