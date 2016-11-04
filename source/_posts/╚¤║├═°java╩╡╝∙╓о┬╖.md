title: 三好网java实践之路
date: 2016.04.30
categories: php #分类
tags: [java,maven,gradle]
---

## 优惠券活动服务
* 直接使用servlet,底层java api开发。
* 使用spring,基于XML方式进行依赖注入
* 使用反射进行API路由
* ORM采用MYBATIS
* 使用MAVEN进行编译，打包

## 短信服务
* 使用ACTIVEMQ进行异步化
* 使用世纪，云片网络，创蓝，互亿等短信服务商
* 使用spring,基于XML方式进行依赖注入
* 使用责任链模式对短信服务商进行管理
* 使用spring 基于java config进行管理（升级项目）
* ORM采用MYBATIS

## 优惠券批量管理
* 使用spring job进行定时任务管理
* 使用spring,基于XML方式进行依赖注入
* ORM采用MYBATIS

## 音视频服务
* 使用ACTIVEMQ进行异步化
* 转码采用ffmpeg
* 使用spring,基于XML方式进行依赖注入
* ORM采用MYBATIS
* 提供音频转码，视频转码功能
* 提供水印功能
* 存储采用阿里云OSS

## 推送服务
* 使用ACTIVEMQ进行异步化
* 初始xml配置，后期java config管理BEAN
* 使用BAIDU云推送服务进行推送管理
* 同时支持教师，学生，IOS,ANDORID4种APP
* 支持点推，全推功能

## 学生，老师收支管理
* 使用java config管理BEAN，更清爽
* ORM采用MYBATIS
* 采用SPRING JOB进行定时任务批处理，定时时间可配置
* 使用logback,代替log4j进行日志管理

## 教师工作室数据性能优化
* 第一次使用gradle进行编译，打包
* 首次使用redis进行性能优化
* 第一次使用springboot进行开发
*　支持全量倒数据，增加更新数据

## 下一步
### 集成jekins

## 总结
### 化繁为简
* 从maven走向gradle
* 从XML走向JAVA CONFIG

### 更快
* 从spring 到springboot
* 从mybatis到spring data
* 从ehcache到redis
* 从log4j到logback（log4j死锁）


