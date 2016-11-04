title: AngularJS简介
date: 2016.01.20
categories: 前端 #分类
tags: [AngularJS]
toc: true
---
  AngularJS是Google维护管理的前端开发框架。它的核心特征有。
## 核心特征
* 数据绑定Data-binding
	数据绑定可以在模型和视图之间的数据进行自动同步。
* 范围Scope
	Scope是控制器和视图之间的桥梁，类似于SpringMVC中的ModelAndView。
* 控制器Controller
　	Controller是JS函数，它用于绑定特定的Scope
* 服务Services
	服务用于完成业务逻辑，比如执行http请求。服务是单例，一个app只有一个服务对象。
* 过滤器Filters
	过滤器作一些比较琐碎的工作，比如大小写转换，指定排序order by等。
* 指令Directives
	暂不知道。
* 模板Templates

* 路由Routing
	这个类似于SpringMVC中的url路由。
* 依赖注入DI
	这个类型于spring框架的依赖注入

## AngularJS优点
* 简化web app开发
* 数据绑定提高了用户体验
* 依赖注入实现业务组件之间的解藕，解藕会带来开发上极大的便利
* MVVM模型，使得views是纯粹的html页面。控制器使用js编写业务逻辑。

## AngularJS缺点
* 不安全
	需求服务端提供授权和认证机制。

## AngularJS主要组件
* ng-app
	这个指令定义了AngularJS应用与HTML之间的联系。
* ng-model
	这个指令绑定了AngularJS应用的数据与HTML输入控件的数据。
* ng-bing
	这个指令绑定了AngularJS应用的数据到HTML的标签。

## 