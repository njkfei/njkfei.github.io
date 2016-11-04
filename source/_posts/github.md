title: github 使用入门
date: 2016.01.15
categories: 工具 #分类
tags: [git, github]
toc: true
---
	最近开始全面拥抱github.现将使用过程，记录如下。
## 使用步骤
### 申请账号
	这个看似简单，其实不然，首先，申请账号时，需要关联邮箱，163邮箱有坑，收不到验证邮件，导致无法通过验证。
	gmail不稳定，也会导致验证不通过。
	后来使用qq邮箱，才搞定。

### 在本地建立账号,与github进行绑定
创建密钥，一路按回车即可。
``` bash
$ ssh-keygen -t rsa -C '297206021@qq.com'
```
这个密钥会与github进行关联。
在github个人中心，进行关联。
在__Add SSH Key__中，将生成的id_rsa.pub里面的内容拷下来，存储在github当中.
验证密钥配置正常。
``` bash
$ ssh -T git@github.com
$ Hi njkfei! You've successfully authenticated, but GitHub does not provide shell
access.
```
上面的信息，表示配置成功。^_^
配置好密钥，后续就不需要输入密码与github进行交互了。

### 搭建个人wiki主页
	在github上申请，利用github提供的空间搭建。申请时的仓库(repository)名称必须与github用户名一致。例如我的用户名为：njkfei.申请的仓库名为njpfie.github.io.
	基于hexo搭建，使用markdown语法进行编辑。刚开始使用google driver同步整个主页的工程和原始md文件。结果多个机器之间，由于版本的问题，导致文件混乱。
	最后的解决办法是，google driver 只保存markdown原始文件。其它文件放在其它地方。通过__config.yml文件指向google driver的source目录。

### 绑定域名
	通过http://njkfei.github.io,可以访问自己的wiki。当然，我不会这么做，我想使用个性化的域名。
	步骤如下：
* 在阿里云上申请域名，将申请的域名，指向njkfei.github.io即可。
  绑定方法可参考网上文章：aliyun 域名绑定github wiki.
* 在github个人主页上创建CNAME文件，绑定域名。
 文件内容为阿里云里面配置的域名，例如我:wiki.niejinkum.com
####　踩过的坑
	由于我申请了两个github账号，且分别创建了两个wiki，将CNAME设置为相同，结果，404了。
	后来通过github的反馈邮件发现，原来github会存在域名冲突。如果两个主页的CNAME相同，会有问题。
	这个域名不能用了，嗨。
####　解决办法
	把这两个账号删除了，重新申请新账号。
	在阿里云上建立新的域名，指向新创建的wiki个人主页。
	期待github更新DNS缓存，还我域名。

###　建立github工程
#### 在github下面，创建工程，如我：git@github.com:njkfei/SpringSecurityMybatisRemerbermeAnnotationExample.git
#### 在个人工作机上，创建目录，如我：
``` bash
$ mkdir SpringSecurityMybatisRemerbermeAnnotationExample
```

#### 初始化工作空间
``` bash
$ cd SpringSecurityMybatisRemerbermeAnnotationExample
$ git init
$ vi README.md
```

#### 创建工程，把我原来就有的项目，拷贝过来
``` bash
$ cp ../demo/src .
$ cp ../pom.xml .
$ cp ../jetty.xml .
```

#### 添加文件
``` bash
$ git add *
```

#### 看看文件状态吧
``` bash
$ git status
On branch master

Initial commit

Changes to be committed:
  (use "git rm --cached <file>..." to unstage)
	new file: pom.xml
	new file: jetty:xml
```

#### 与github远程仓库建立关联
``` bash
$ git remote add github git@github.com:njkfei/SpringSecurityMybatisRemerbermeAnnotationExample.git
```

#### 检查连接状态
``` bash
$ git remote -v
github  git@github.com:njkfei/SpringSecurityELAnnotationExample.git (fetch)
github  git@github.com:njkfei/SpringSecurityELAnnotationExample.git (push)
```
ok


#### 提交文件
``` bash
$ git add *.xml README.md src
$ git status
$ git commit -m "first commit"
```
提交文件后，可以通过git status 查看变化状态

#### 推送文件到远程github仓库
``` bash
$ git push github master
Counting objects: 8, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (6/6), done.
Writing objects: 100% (6/6), 1005 bytes | 0 bytes/s, done.
Total 6 (delta 1), reused 0 (delta 0)
To git@github.com:njkfei/SpringSecurityMybatisAnnotationExample.git
   e6f620b..0021914  master -> master
```
上面表示推送成功
