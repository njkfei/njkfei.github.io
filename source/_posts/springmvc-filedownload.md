title: springmvc 文件下载
date: 2016.01.19
categories: java #分类
tags: [java, springmvc, download]
toc: true
---
## springmvc 文件下载
### 项目简介
 基于spring4 mvc 构建本项目。
 * spring4 提供对象管理功能。
 * springmvc 提供mvc框架

### 基本思路
 原始英文写得比较好，copy如下：
Downloading a file is quite simple and involves following steps.

* Create an InputStream to the file to be downloaded.
Find MIME type of the content of downloaded file.
– can be of application/pdf, text/html,application/xml,image/png, ..others.
*　Set Content-Type in response(HttpServletResponse) with MIME type found above.
response.setContentType(mimeType);
*　Set Content length in response with MIME type found above.
response.setContentLength(file.getLength());//length in bytes
* Set Content-Disposition HEADER in response.
response.setHeader(“Content-Disposition”, “attachment; filename=” + fileName); //With ‘attachement’ File will be downloaded as is. May show a ‘Save as’ dialog based on browser setting.
response.setHeader(“Content-Disposition”, “inline; filename=” + fileName);//With ‘inline’ browser will try to display content right into broswer for certain contents(imgages,PDF,text,..). For other content types, file will be download directly.

* Copy bytes from InputStream to OutputStream of response.
* Once copying done, close input & output stream.

本项目没有xml配置文件，全部以注解的方式，进行配置注入。

#### github: [https://github.com/njkfei/filedownload.git](https://github.com/njkfei/filedownload.git)
#### 项目参考：[www.websystique.com](http://www.websystique.com)
#### 个人blog: [wiki.niejinkun.com](http://wiki.niejinkun.com)
