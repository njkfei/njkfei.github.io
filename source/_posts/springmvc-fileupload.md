title: springmvc file upload 文件上传
date: 2016.01.18
categories: java #分类
tags: [java, springmvc, fileuploads, commons-fileupload]
---
  文件上传是一项基本功能，例如支付宝上传身份证照片，个人中心上传个人头像，微信图片分享上传个人图片等。因此有必要了解如何上传照片。

##　依赖包
``` bash
<dependency>
    <groupId>commons-fileupload</groupId>
    <artifactId>commons-fileupload</artifactId>
    <version>1.3.1</version>
</dependency>

##　代码示例
　见github项目[https://github.com/njkfei/spring-fileupload.git](https://github.com/njkfei/spring-fileupload.git)


本项目没有xml配置文件，全部以注解的方式，进行配置注入。

#### github: [https://github.com/njkfei/spring-fileupload.git](https://github.com/njkfei/spring-fileupload.git)
#### 项目参考：[www.websystique.com](http://www.websystique.com)
#### 个人blog: [wiki.niejinkun.com](http://wiki.niejinkun.com)
