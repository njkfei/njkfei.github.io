title: linux常用命令
date: 2016.01.07
categories: linux #分类
tags: [linux, shell]
toc: true
---
　工欲善其事，必先利其器。我个人常用的linux命令有:
* find
* which
* locate
* grep
* sed
* ps
* kill
* date
* top
* free
* df
* du
* dstat
* iostat
* crontab

## find
``` bash
$ find -iname *.ts
```
## which
  which一般用于找二进制工具文件。
``` bash
$ which mysql
$ /opt/modules/mysql/bin/mysql
```

## locate
  如果**which**找不到文件，可以试试locate,locate会读文件系统数据库，因此，它比**which**更强大。
``` bash
$ locate mysql
$ /var/www/wiki/lib/plugins/authmysql/lang/sv/settings.php
$ /var/www/wiki/lib/plugins/authmysql/lang/zh/settings.php
$ /var/www/wiki/lib/plugins/authmysql/lang/zh-tw/settings.php
```
  如果**locate**也没找到，但文件确实是存在的，则先试试**updatedb**,更新文件系统数据库。
``` bash
$ updatedb
```

## grep
  **grep**是文件内搜索正则表达式利器。常用的有搜索日志，搜索配置文件。
#### 普通查找
``` bash
$ fgrep "收到" audio.log
$ 2016-01-06 18:05:44 [ jmsContainer-2:607467356 ] - [ INFO ] 收到的消息类型 ==>class org.apache.activemq.command.ActiveMQTextMessage
$ 2016-01-06 18:05:44 [ jmsContainer-2:607467356 ] - [ INFO ] Thread[jmsContainer-2,5,main]: 接收到一个消息 -> {"audio_id":"77","audio_src_url":"http://img.sanhao.com/dev/audio/opencourse/77/77.mp3","timestamp":1451468623}
```
#### 查找行
``` bash
$ fgrep -n "收到" audio.log
$ 129769:2016-01-06 18:05:44 [ jmsContainer-2:607467356 ] - [ INFO ] 收到的消息类型 ==>class org.apache.activemq.command.ActiveMQTextMessage
$ 129770:2016-01-06 18:05:44 [ jmsContainer-2:607467356 ] - [ INFO ] Thread[jmsContainer-2,5,main]: 接收到一个消息 -> {"audio_id":"77","audio_src_url":"http://img.sanhao.com/dev/audio/opencourse/77/77.mp3","timestamp":1451468623}
```

#### 查找匹配的数量
``` bash
$ grep -c "收到" audio.log
$ 6
```
#### or查询
``` bash
$ grep -e "m3u8成功" -e "接收" nohup.out 
2016-01-09 10:54:29 [audio]-[INFO]:75  Thread[jmsContainerVideo-1,5,main]: 接收到一个处理视频消息 -> {"video_id":"65","video_src_url":"img.sanhao.com\/video\/opencourse\/","timestamp":1452308069}
2016-01-09 11:05:41 [audio]-[INFO]:75  Thread[jmsContainerVideo-1,5,main]: 接收到一个处理视频消息 -> {"video_id":"66","video_src_url":"http:\/\/img.sanhao.com\/dev\/video\/opencourse\/66\/66.mp4","timestamp":1452308741}
2016-01-09 11:08:23 [com.sanhao.media.serviceImpl.ConvertCodeServiceImpl]-[INFO]:311  更新数据库  ..........................转换m3u8成功
```

#### 正则查找
　略

## sed
  经常有统一修改源代码中的变量需求，有时候，不想一个一个去修改，这时候,*sed*用上了。
#### 全部修改
``` bash
$ sed -i "s/收到/接收到/g" audio.log
```

## ps
  看进程的状态。
#### 常规使用
``` bash
$ ps aux 
$ root       174  0.0  0.0      0     0 ?        S     2015   0:00 [scsi_eh_0]
$ root       175  0.0  0.0      0     0 ?        S     2015   0:00 [scsi_eh_1]
$ root       186  0.0  0.0      0     0 ?        S     2015   0:00 [scsi_eh_2]
```

#### 配合**grep**使用
``` bash
$ ps aux | grep java
$ 10027    24221  0.0  0.0 103252   884 pts/1    S+   16:47   0:00 grep java
$ 10027    25724  0.1  1.9 4195924 157316 ?      Sl   Jan06   2:18 java -classpath ../lib/media-0.0.1-SNAPSHOT.jar:../conf:../bin:.. com.sanhao.media.entry.AppStart
$ 10027    28939  0.3  8.3 4608940 675596 ?      Sl    2015  68:30 java -classpath ../lib/push-0.0.1-SNAPSHOT.jar:../conf:../bin:.. com.sanhao.push.entry.AppStart
```

#### 配合**grep**使用过滤项
　配合*grep*使用时，会多一行无用项，如上面的第一行。这时，可以附加过滤。
``` bash
$ ps aux | grep java | grep -v "grep"
$ 10027    16475  0.2  1.6 4598864 134092 ?      Sl    2015  34:37 java -classpath ../lib/notice-0.0.1-SNAPSHOT.jar:../conf:../bin:.. com.sanhao.notice.entry.AppStart
$ 10027    25724  0.1  1.9 4195924 157316 ?      Sl   Jan06   2:18 java -classpath ../lib/media-0.0.1-SNAPSHOT.jar:../conf:../bin:.. com.sanhao.media.entry.AppStart
```

#### 查看进程号
``` bash
$ ps aux | grep java | grep -v "grep" | awk '{print $2}'
$ 3164
$ 5056
$ 15623
$ 16475
$ 25724
$ 28939
```

#### 查看占用进程最多的进程
```
ps -aux | sort -k4nr | head
```

  好吧，这两个够我用一段时间了。

## kill
  **kill**杀死进程，我一般结合**-9**参数。进程号通过上面的命令得到。
``` bash
$ kill -9 28939
```
  为什么不使用stop.sh脚本杀死进程呢，因为它经常杀不死，或者有其它问题。所以，我经常用**kill**手杀.

## date
#### 看时间戳秒
``` bash
$ date +%s
$ 1452157081
```
  其实，我经常用它反查看**timestamp**的时间。
``` bash
$ date -d @1452157081
$ Thu Jan  7 16:58:01 CST 2016
```

## top
  性能分析，需要看它了。
#### 裸命令
``` bash
$ top
$ top - 17:00:38 up 41 days, 10:03,  2 users,  load average: 0.09, 0.05, 0.01
$ Tasks: 170 total,   1 running, 169 sleeping,   0 stopped,   0 zombie
$ Cpu(s):  1.1%us,  0.3%sy,  0.0%ni, 98.5%id,  0.0%wa,  0.0%hi,  0.0%si,  0.0%st
$ Mem:   8061376k total,  7745008k used,   316368k free,   263412k buffers
$ Swap:  8388600k total,   745360k used,  7643240k free,  3165132k cached
$
$ PID USER      PR  NI  VIRT  RES  SHR S %CPU %MEM    TIME+  COMMAND            
$ 1177 root      20   0  142m  11m  896 S  1.9  0.1  58:35.37 redis-server       
$ 16475 niejinpi  20   0 4491m 130m 5756 S  1.9  1.7  34:38.76 java               
$    1 root      20   0 21324 1100  892 S  0.0  0.0   0:36.80 init               
$    2 root      20   0     0    0    0 S  0.0  0.0   0:00.00 kthreadd 
```
#### 看执行的命令行
``` bash
$ top -c
$ PID USER      PR  NI  VIRT  RES  SHR S %CPU %MEM    TIME+  COMMAND                                                                                                                                                                        
$ 3164 activemq  20   0 2994m 307m 5516 S  0.7  3.9 160:42.74 java -Xms512m -Xmx512m -Djava.util.logging.config.file=logging.properties -Dhawtio.realm=activemq -Dhawtio.role=admins -Dhawtio.rolePrincipalClasses=org.apache.activemq.jaas.G
$ 16475 niejinpi  20   0 4491m 130m 5756 S  0.7  1.7  34:38.94 java -classpath ../lib/notice-0.0.1-SNAPSHOT.jar:../conf:../bin:.. com.sanhao.notice.entry.AppStart                                                                            
```

#### 看cpu的核数
 先输入**top**命令，再按**1**即可。
``` bash
$ top 
$ top - 17:03:06 up 41 days, 10:06,  2 users,  load average: 0.05, 0.04, 0.00
$ Tasks: 168 total,   1 running, 167 sleeping,   0 stopped,   0 zombie
$ Cpu0  :  0.7%us,  0.3%sy,  0.0%ni, 98.7%id,  0.0%wa,  0.0%hi,  0.3%si,  0.0%st
$ Cpu1  :  0.3%us,  0.0%sy,  0.0%ni, 99.7%id,  0.0%wa,  0.0%hi,  0.0%si,  0.0%st
$ Cpu2  :  0.3%us,  0.3%sy,  0.0%ni, 99.3%id,  0.0%wa,  0.0%hi,  0.0%si,  0.0%st
$ Cpu3  :  0.0%us,  0.0%sy,  0.0%ni,100.0%id,  0.0%wa,  0.0%hi,  0.0%si,  0.0%st
$ Mem:   8061376k total,  7753180k used,   308196k free,   263704k buffers
$ Swap:  8388600k total,   745360k used,  7643240k free,  3166164k cached
```

## free
 **free**和**top**一样，也是性能分析参考使用。
``` bash
$ free
$              total       used       free     shared    buffers     cached
$ Mem:       8061376    7754592     306784          0     263800    3166224
$ -/+ buffers/cache:    4324568    3736808
$ Swap:      8388600     745360    7643240
```

## df
  **df**看文件系统的宏观情况。
``` bash
$ df -h
$ Filesystem                      Size  Used Avail Use% Mounted on
$ /dev/mapper/vg_centos6-lv_root   30G   17G   12G  60% /
$ tmpfs                           3.9G     0  3.9G   0% /dev/shm
$ /dev/sda1                       485M   33M  427M   8% /boot
$ /dev/mapper/vg_centos6-lv_home   80G   56G   21G  74% /home
```

## du
  在我看来，**du**更实用，它能看某个目录占用的大小。我更喜欢使用**du**
``` bash
$ du -h --max-depth=1
$ 496M    ./push
$ 46M     ./media
$ 275M    ./video
$ 132M    ./notice_20151218
$ 7.8G    .
```

## dstat和iostat
　看网络io和磁盘io．在性能分析的时候可以用上．

## crontab
　这个是跑定时任务的，相对比较复杂，后续会单独出文介绍．
简单示例如下：
``` bash
$ crontab -l
no crontab for niejinping
```