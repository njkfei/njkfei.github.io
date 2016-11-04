title: yii2 ubuntu部署指南
date: 2016.05.03
categories: php #分类
tags: [php,yii2]
---

## 基本环境安装
要求提前安装下述依赖环境
* apache
* mysql
* php
版本使用主流的即可。
假设已经安装上述环境

## 约束
* 数据库中数据已准备好

## 线上部署包
* 下面假设部署包是yii2.zip
* yii2的部署包为压缩包，解压安装到apache指定的工作目录即可
* 假设yii2.zip解压后放在了目录"~/www/yii2下面"
* "~"在部署时，请使用绝对路径
* 后续的例子都是以此为说明的


## 配置
### httpd.conf配置
apache是yii2运行的容器环境，首先需要配置httpd.conf.
httpd.conf一般在目录“/etc/httpd/conf/httpd.conf”下面。

如下所示。
```
[niejinping@iZ28h3eaiotZ ~]$ more /etc/httpd/conf/httpd.conf
```

httpd.conf有如下项目需要配置
* rewrite规则重写
* Web Root目录
  

#### rewrite规则重写 
找到下面一行。
```
#LoadModule rewrite_module modules/mod_rewrite.so
```
把注释去掉。


#### Web Root配置
默认的Web Root是：
```
DocumentRoot "/var/www/html"
```
把这行改为yii2的目录。即：
```
DocumentRoot "~/www/yii2/web"
```

修改权限设置。
把
```
<Directory "/var/www/html">

#
# Possible values for the Options directive are "None", "All",
# or any combination of:
#   Indexes Includes FollowSymLinks SymLinksifOwnerMatch ExecCGI MultiViews
#
# Note that "MultiViews" must be named *explicitly* --- "Options All"
# doesn't give it to you.
#
# The Options directive is both complicated and important.  Please see
# http://httpd.apache.org/docs/2.2/mod/core.html#options
# for more information.
#
    Options Indexes FollowSymLinks

#
# AllowOverride controls what directives may be placed in .htaccess files.
# It can be "All", "None", or any combination of the keywords:
#   Options FileInfo AuthConfig Limit
#
    AllowOverride None

#
# Controls who can get stuff from this server.
#
    Order Deny,Allow
   Allow from all

</Directory>

```

修改为：
```
<Directory "~/www/yii2/web">

#
# Possible values for the Options directive are "None", "All",
# or any combination of:
#   Indexes Includes FollowSymLinks SymLinksifOwnerMatch ExecCGI MultiViews
#
# Note that "MultiViews" must be named *explicitly* --- "Options All"
# doesn't give it to you.
#
# The Options directive is both complicated and important.  Please see
# http://httpd.apache.org/docs/2.2/mod/core.html#options
# for more information.
#
    Options Indexes FollowSymLinks

#
# AllowOverride controls what directives may be placed in .htaccess files.
# It can be "All", "None", or any combination of the keywords:
#   Options FileInfo AuthConfig Limit
#
    AllowOverride None

#
# Controls who can get stuff from this server.
#
    Order Deny,Allow
   Allow from all

</Directory>

```


### db配置
在yii2/config/db.php里面，设置mysql连接参数
如：

```
<?php

return [
    'class' => 'yii\db\Connection',
    'dsn' => 'mysql:host=localhost;dbname=yii2basic',
    'username' => 'root',
    'password' => 'rootroot',
    'charset' => 'utf8',
];
```
请修改上述host,dbname,username,password为线上配置。
为了安全考虑，上述端口号不允许外网访问。

### redis配置
在文件yii2/config/web.php中，增加redis配置。
#### 修改Cache为Redis
去掉FileCache,改用RedisCache,如下所示：
```
        'cache' => [
            // 'class' => 'yii\caching\FileCache',
            'class' => 'yii\redis\Cache',
        ],
```


#### 修改redis连接
同样的，在文件yii2/config/web.php中，增加redis配置。
```
        'redis' => [
            'class' => 'yii\redis\Connection',
            'hostname' => 'localhost',
            'port' => 6379,
            'database' => 0,
        ],
```
把上述的配置设置为线上配置即可,需要修改hostname,port.
为了安全考虑，上述端口号不允许外网访问。

至此，线上配置已完成。

## 重启apache服务
 线上配置完成后，重启apache服务即可。

### 重启apache服务
 ```
 $sudo /etc/init.d/httpd restart
```

### 检查服务是否成功重启
```
$ps aux |grep httpd
[niejinping@iZ28h3eaiotZ ~]$ ps aux | grep httpd
500       2702  0.0  0.0 103132   852 pts/0    S+   15:07   0:00 more httpd.conf
500       2738  0.0  0.0 103308   844 pts/1    S+   15:20   0:00 grep httpd
apache   14556  0.0  1.6 561688 17044 ?        S    May01   0:00 /usr/sbin/httpd
apache   14557  0.0  1.6 561756 16844 ?        S    May01   0:00 /usr/sbin/httpd
apache   14558  0.0  1.6 561432 17112 ?        S    May01   0:00 /usr/sbin/httpd
apache   14559  0.0  1.6 561432 17224 ?        S    May01   0:00 /usr/sbin/httpd
apache   14560  0.0  1.8 562296 18948 ?        S    May01   0:00 /usr/sbin/httpd
apache   14561  0.0  1.4 561432 14552 ?        S    May01   0:00 /usr/sbin/httpd
apache   14562  0.0  1.6 561592 16536 ?        S    May01   0:00 /usr/sbin/httpd
apache   14563  0.0  1.4 561516 14852 ?        S    May01   0:00 /usr/sbin/httpd
root     17294  0.0  2.3 463612 23576 ?        Ss   Apr29   0:11 /usr/sbin/httpd
```
如果有httpd进行运行，则说明启动正常。
如果启动失败，请根据logs分析原因。


## 测试运行
在浏览器或者postman中输入url,看到下面的结果，应该就OK了。