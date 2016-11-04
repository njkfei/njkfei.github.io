title: Travis CI集成自动生成博客
date: 2016.11.04
categories: wiki #分类
tags: [wiki]
toc: true
---

## 注册Travic账号
前往[https://travis-ci.org](https://travis-ci.org)账号，使用gitlab账号即可。

## 安装ruby
centos默认是1.8版本，需要升级为新版本，通过源码安装，方法如下：

```
$ wget http://cache.ruby-lang.org/pub/ruby/2.1/ruby-2.1.2.tar.gz
$ tar xvfvz ruby-2.1.2.tar.gz
$ cd ruby-2.1.2
$ ./configure
$ make
$ sudo make install
```

## 其它比较麻烦
建议google "hexo travis ci"