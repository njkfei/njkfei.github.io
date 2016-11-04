title: java开启远程监控
date: 2016.08.24
categories: 设计 #分类
tags: [架构]
---
 
线上运行的服务，如果没有监控，无异于盲人摸象。
裸奔的机器，根据墨菲定律，盲点必须出现问题。
所以，必须要上监控。

监控手段有两种：
* 事前监控
* 事后监控
事后监控太晚啦，所以，监控应该在事前就去做。

## 事前监控
事前监控的手段，有两个方面：
* 业务监控
* 进程监控

监控不能直接进入生产环境，因此，监控应该是远程监控，本机监控另说。

业务监控可以通过统一的日志平台进行管理。这个以后再讲
进程监控，有两种方法：
* rmx remote
* jstatd
rmx remote需要在进程启动脚本中加入选项参数。需要编写启动脚本。
jstatd不需要修改启动脚本。直接在目标机启动jstatd后台进程即可。

### rmx remote配置
如下所示：
```
JAVA_OPTS="${JAVA_OPTS} -Dcom.sun.management.jmxremote -Dcom.sun.management.jmxremote.port=9123 -Dcom.sun.management.jmxremote.ssl=false -Dcom.sun.management.jmxremote.authenticate=false -Dcom.sun.management.jmxremote.local.only=true"
```

### jstatd配置
#### 安装jstatd
如果没有安装，那请自行安装吧。

#### 编辑配置文件
需要配置安全权限
```
vi jstatd.policy 
grant codebase "file:../lib/tools.jar" {
    permission java.security.AllPermission;
};
```

#### 启动jstatd
```
jstatd -J-Djava.security.policy=./jstatd.policy
```
#### 查看端口号是否启动
jstatd进程1099是默认端口
```
netstat -an | grep 1099
```

#### 远程运行
 visualVM 打开连接端口，太爽啦！！！

## 附加完整的脚本
```
#!/bin/sh

curdir=`dirname $0`
cd $curdir
curdir=`pwd`

APP_NAME=teacherfeed-1.0.jar
MAIN_CLASS=org.springframework.boot.loader.JarLauncher
APP_HOME=..

export APP_HOME

LIB_PATH=$APP_HOME/lib
export LIB_PATH

#for i in $LIB_PATH/*
#       do CLASSPATH=$i:$CLASSPATH
#done
CLASSPATH=$LIB_PATH/$APP_NAME
#export CLASSPATH

CLASSPATH=$CLASSPATH:$APP_HOME/conf:$APP_HOME/bin:$APP_HOME

JAVA_OPTS="${JAVA_OPTS} -Dcom.sun.management.jmxremote -Dcom.sun.management.jmxremote.port=9123 -Dcom.sun.management.jmxremote.ssl=false -Dcom.sun.management.jmxremote.authenticate=false -Dcom.sun.management.jmxremote.local.only=true"

export CLASSPATH
cd $APP_HOME/bin

nohup java ${JAVA_OPTS} -classpath $CLASSPATH $MAIN_CLASS &
                                                                                           
```

