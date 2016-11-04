title: git最佳实践
date: 2016.10.24
categories: 沟通协作 #分类
tags: [git]
toc: true
---
## 前言
* git是分布式版本控制系统
* git是协作系统
* svn是功能机，git是智能机，他们的用户体验差了一个时代，生产效率更是上了一个台阶
* git是一种生活方式
* git 配合sourcetree,是最佳实践，当然，可再配合jenkins使用
* github,gitlab自建review board.无缝集成review board
* gitlab是github的开源实现，企业可以使用gitlab,搭建自己的git环境。
  

## 开发模式

* 分支开发，主干分布
* master分支，只有运维管理员有权限操作,运维使用master的分支进行线上发布
* develop分支，只有code review权限的人员有权限操作，通过code review后，合并进develop分支
* develop_njp（名字）分支，控制权为个人，个人在该分支下进行操作

## 开发流程
* 新建项目：通过运维管理员申请创建项目，包括项目名称，参与人，角色
* 开发人员从delevop pull项目，创建自己的分支，命令为develop_xxx
* 开发人员在develop_xxx下进行开发，自行控制版本
* 开发人员自行进行单元测试，测试通过后，将项目push到develop_xxx
* 开发人员切换到develop分支，将develop分支中的更新，合并到自己的分支上
* 开发人员将合并后的develop_xxx分支，push推送到develop分支上
* code review自动开始
* code review通过，自动发邮件给开发
* code review未通过，自动发邮件给开发

分支merge，push,通过神器source tree即可。

## 最佳实践
* gitlab
* sourcetree
* jenkins
  gitlab + sourcetree +　jenkins构建企业级代码管理CI（持续集成)系统

## 建议
* 一个人负责一个项目，减少沟通成本
* 一个人负责一个项目，提高开发的自主能力
* 每个人关注自己的边界，针对边界编程

## 演示
   下面以github为例，进行演示。

## 感谢
* github
* sourcetree