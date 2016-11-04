title: crontab使用方法
date: 2016.01.08
categories: linux #分类
tags: [linux, crontab, job, 定时任务]
---
  　定时服务是很常见的业务需求，例如：
 * 每天晚上1点清理不用的文件
 * 每天晚上对日志进行清理
 * 每天晚上1点重启机器
 * 每天晚上统计运营数据
 等等等等．
 　　最简单的定时任务就是:
## crontab

## 使用方法
* 查看
* 编辑
* 生效

### 查看
``` bash
$ crontab -l

```
### 编辑
``` bash
$ crontab -e
```
### 生效
``` bash
$ sudo /etc/init.d/crond restart
```