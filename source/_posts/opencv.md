title: opencv java集成使用简介
date: 2016.05.12
categories: opencv
tags: [java,opencv]
---
  
opcv图像处理的开发包，可以完成人脸识别， 裁剪，缩放，旋转等功能。基于opencv可以方便的构建公司自己的图片服务。
opcv是基于C/C++进行开发的，而java开发效率高，因此，很有必须将他们集成起来，构建自己的图片服务框架。

## 技术栈
* spring mvc : 提供对外服务的接口
* opencv
* opcv.jar: 提供与opcvn的桥接功能。
* build.gradle: 构建框架

## 开发环境
 idea

## 安装opcv javasdk
   在官网[http://opencv.org/](http://opencv.org/)下载安装包。
   例如安装在'C:/SOFTWARE/opencv‘目录下。

## 依赖管理
   opcv java sdk不在maven中央仓库，需要加入本地依赖
   compile fileTree(include: ['*.jar'], dir: 'C:/SOFTWARE/opencv/build/java')
在idea中添加依赖的jar包

## 关于坑
   在windows环境下测试通过，但生成的JAVA包 在centos上面执行会有问题，因为底层调用的动态库是不一样的。在windows下面是opencv.dll,
   在linux 下面是so,因此，windows下面编译通过的java包在linux可能不可用，需要注意。
   