title: ionic helloworld
date: 2016.01.21
categories: 前端 #分类
tags: [ionic]
toc: true
---
  
## 安装nodejs
 	前往nodejs.org,下载安装nodejs

## 安装ionic
``` 
$ npm install -g ionic
```

## 安装cordova
```
$ npm install -g cordova
```

## 启动demo
```
$ ionic start myapp tabs
```
有如下demo:
* tabs
* sidemenu
* maps
* salesforce
* complex-lixt
* blank

## 进入myapp
```
$ cd myapp
```

## 添加到运行平台
如ios
``` 
$ ionic platform add ios 
```
android
``` 
$ ionic platform add android 
```
注意：__添加运行平台，需要管理员权限。__
等待下载，需要一定的时间。

## 在浏览器中进行测试
```
ionic save
```
## 编译app
ios平台
``` 
$ ionic build ios
```
android平台
编译需要花费很长的一段时间，要有耐心
## 模拟运行
ios 平台
``` 
$ ionic emulate ios
```

android平台
```
$ ionic emulate android
```

## 命令总结
```
$ npm install -g cordova 
$ npm install -g ionic
$ ionic start myapp tabs
$ cd myapp
$ ionic platform add android
$ ionic build android
$ ionic emulate android
```