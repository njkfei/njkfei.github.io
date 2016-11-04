title: mysql常用命令
date: 2016.01.08
categories: 数据库 #分类
tags: [数据库, mysql]
---
  　


### 增
### 删

### 改
### 查
### 备份
### 导出
导出某表某列的数据
```
$ mysql -h host -u root -p database --execute="select teacher_id,user_id,teacher_pic from user_teacher" -X teacherpics.xml
```
导出的文件存储方式为xml.