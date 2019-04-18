---
layout: post
title:  "linux搭建node.js服务，使用pm2启动socket.io服务的过程"
date:   2017-07-02 10:41
categories: Server
tags: linux node.js
---

* content
{:toc}

### 1.安装Linux Git工具 
`sudo yum install git`
### 2.下载nvm(node版本管理工具)
`curl -o- https://raw.[githubusercontent.com/creationix](http://githubusercontent.com/creationix)/nvm/v0.33.1/install.sh | bash`

**下载完成后可能还是提示没有nvm命令，使用source更新环境变量 source ~/.bashrc或者source ~/.zshrc**
### 3.使用nvm下载
`node  nvm install node`
### 4.编写package.json文件
```
{
    "name": "node_test",

    "version": "0.0.1",

    "description": "my first socket.io app",

    "dependencies": {

        "express": "^4.15.2",

        "socket.io": "^2.0.4"

    }
}
```
### 5.编写server.js基础文件
```
// 'use strict'

var app = require('express')();

var http = require('http').Server(app);

var io = require('socket.io')(http);

http.listen(3000, function () {

    console.log('listening on *:3000');

});
```
### 6.将文件上传到你想要的路径

`scp /path/filename username@servername:/path`

`scp /ddz-server/server.js  root@192.168.0.101:/root/ddz/ddz_server`

### 7.node自带npm ,用npm安装node所需的模块 
`node install`
### 8.让node能够在后台一直运行，下载pm2管理工具 
然而这时候只要我们服务端控制台一退出我们的服务就会断了，这时候就需要我们使用pm2这个管理工具了。
`npm install pm2 -g`
### 9.利用pm2启动server.js文件 
`mp2 start server.js`

![image.png](https://coding.net/u/jianghongjohn/p/jianghongjohn.coding.me/git/raw/master/resource/server_node_pm2.png)

关于pm2的用法具体可以百度谷歌