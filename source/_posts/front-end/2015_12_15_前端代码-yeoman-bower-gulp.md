---
title: 前端代码-yeoman-bower-gulp
date: 2015-12-15 14:13
categories:
	- Front-End
tags:
	- yeoman
	- bower
	- gulp
---
# 工具
codekit
fis

grunt Build tool构建工具
类似的有ant gmake等

## NodeJS
grunt依赖NodeJS

```
npm install -g grunt-cli

```

`package.json`中的`~`和`^`，版本更新的控制

## yo
* 使用yeoman来生成项目的文件，代码结构。
* yeoman自动将最佳实践和工具整合进来，大大加速和方便

```sh
npm install -g yo
```
依赖`Node.js`

### 安装模板生成器
```sh
npm install -g generator-webapp
or
npm install -g generator-angular
```

### 生成

```sh
yo angular
```
or

```sh
yo angular --coffee
```

**在Angular中**

```sh
$ yo angular:controller myController
$ yo angular:directive myDirective
$ yo angular:filter myFilter
$ yo angular:service myService
```
**查看已经安装的模板生成器**

npm ls -g  --depth=1 2>/dev/null | grep generator-

## bower

### 安装
```
npm install -g bower
bower install
bower install xxx --save

```
### bower安装github上的应用

```
bower install jquery/jquery
bower install https://xxx.xx.git
```

### 通过url安装

直接bower install 地址

### bower的两个配置文件
bower.json

bowerrc

```json
{
    "directory": "bower_components",
    "proxy": "http://.com",
    "https-proxy": "https://...",
    "timeout": 6666
}
```


## grunt
Build tool

3个 概念  Task Options Target
### 安装

```
npm install -g grunt-cli
```

## gulp

[中文官方API](http://www.gulpjs.com.cn/docs/api/)

启用一个默认的任务
```
var gulp = require("gulp")

gulp.task('default', function() {
	console.log("I have configured a gulpfile")
})
```


## webpack
相对于gulp,webpack更加高级,关注的是web发布的逻辑构建,二gulp(grunt)是从底层构建

webpack的配置文件.webpack.config.js
入口文件
```
entry: [
    'webpack/hot/only-dev-server',
    './src/components/GalleryByReactApp.js'
]
```

出口文件
```
output: {
    filename: 'main.js'
    publicPath: '/assets/'
}
```

`resolve`: 模块解析配置项
`alias`的好处是:
require(".../src/style/main.css")可以简写成
require("styles/main.css")
```
resolve: {
    extensions: ['', '.js', '.jsx'],
    alias: {
        'styles': __dirname + '/src/styles'
    }
}
```

webpack的loader机制, `loaders`

![webpack的loaders](http://7xrn62.com1.z0.glb.clouddn.com/9c2763e916bdcdf0174a5f511b6829d8.png)


autoprefixerloader解决不同浏览器兼容CSS的问题.
