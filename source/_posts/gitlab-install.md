title: gitlab安装
date: 2016.02.01
categories: 工具 #分类
tags: [linux]
toc: true
---

### 环境
　aws - ec2 1G RAM免费版本
### 必要安装包
```
sudo yum -y install libicu-devel patch gcc-c++ readline-devel zlib-devel libffi-devel openssl-devel make autoconf automake libtool bison libxml2-devel libxslt-devel libyaml-devel zlib-devel openssl-devel cpio expat-devel gettext-devel curl-devel perl-ExtUtils-CBuilder perl-ExtUtils-MakeMaker
```

### 安装ruby
```
sudo yum install ruby
```

### 安装MySQL及初始化GitLab库
aws的mysql无法登陆，默认不明知道是什么，安装失败结束．
　