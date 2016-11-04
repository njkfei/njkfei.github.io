title: yii2 redis mysql集成指南
date: 2016.04.30
categories: php #分类
tags: [php,yii2,redis,mysql]
---

## yii2安装
*　yii2是基于php的web框架。它提供了web开发的一整套套件，是典型的中间件和半成品。
*　yii2支持插件，通过插件，可以丰富应用开发。 
*  yii2的插件是通过composer管理的，因此，需要安装composer

### 前提
 你得安装了wamp或者lamp.

### composer安装
composer是php环境下的插件管理器。类似于java系的maven,gralde等。
因此，需要先安装composer.安装方法如下：
```
todo
```

composer是通过composer.json格式进行管理的。

### yii2安装
 可以从官网下载，或者找一个可以封装好的包。

### redis插件安装

### mysql插件安装

## 配置
### url配置
#### 开启pretty url功能
pretty url功能可以使得url更美观
方法如下：

```
todo
```

### db配置
在config/db.php里面，设置mysql连接参数

### redis配置
去掉FileCache,改用RedisCache


```
todo
```

修改redis连接

## 部署
yii2的部署，直接复制源码包，再配置apache即可。

## 测试运行
在浏览器或者postman中输入url,看到下面的结果，应该就OK了。