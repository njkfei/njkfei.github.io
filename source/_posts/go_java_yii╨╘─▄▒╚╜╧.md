title: go java php性能比较
date: 2016.04.30
categories: 架构 #分类
tags: [java, go,php]
---
 昨天对go,java,php进行了性能对比。

## 测试环境
 测试环境是基于阿里云的教育系统机器 。
 1G MEM,2.6G CPU.

## 测试基准
###　框架
* java采用springboot
* go采用beego
* php采用yii3

### 功能
* 通过http api提供外部数据访问接口
* 通过json格式返回数据 
* 优先从redis取数据，如果redis无数据，则从mysql中取出，放入redis

### 技术栈
* 缓存采用redis
* 数据存储采用mysql
* restful风格接口

## 测试方法
采用ab压测工具，300并发连接，10W请求量。

## 测试结论
### 现象
* 都能把cpu压到80%
* springboot，beego都比较快
* yii2负载非常高

### QPS性能
* JAVA最佳
* GO其次
* PHP最差，几乎不可用

### MEM占用情况
*　java 占了25%的空间
*　go 只占了2%左右
*  php占用不多

###　总结
* JAVA性能不错，大型公司选它还是有道理的，缺点是吃内存，是典型的拿空间换时间。
* GO性能不错，而且不吃内存
* yii2框架对于高并发，可能还是不太适合


