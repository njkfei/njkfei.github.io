title: QUARTZ定时任务
date: 2016.01.08
categories: 服务 #分类
tags: [java, quartz, job]
---
  　定时服务是很常见的业务需求，例如：
 * 每天晚上1点清理不用的文件
 * 每天晚上对日志进行清理
 * 每天晚上1点重启机器
 * 每天晚上统计运营数据
 等等等等．
 　　最简单的定时任务就是:
 ## crontab

 当然，java也提供了重量级的任务调度框架：
 ## QUARTZ
