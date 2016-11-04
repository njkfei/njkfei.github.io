title: JAVA架构最佳实践
date: 2016.08.24
categories: 设计 #分类
tags: [架构]
---
 
## 依赖框架
 springboot 无脑上

## ORM
 mybatis，可以满足个性化SQL,必须上
### 注意
* 建议配置SQL日志输出

## API自动维护
有两种方式维护API：
* WIKI
* 代码即文档，通过swagger,自动生成在线API文档。但要注意安全性问题，必须是授权的访问者可以看到API说明。

swagger，你值得拥有

### 资源参考
* http://swagger.io/

## 性能监控
性能监控的指标有：
* 响应时间
* 吞吐量
* 线程
* GC：GC是JAVA后台服务的最大一关。

性能监控有两种方式：
* 本地监控
* 远程监控

### 本地监控
在本地使用jstat命令进行监控。
* 优点是即时使用。
* 缺点是如果监控的是线上环境，需要开发人员在线上机器进行操作，上线操作是大忌。常在河边走，难免会湿靯。

### 远程监控
对于线上机器，可以考虑远程监控。
开启远程监控的思路很简单：
* 目标机运行监控agent,如jstatd,rmx
* 通过visualVM或zabbix插件，远程查看目标机运行状态

### 推荐工具
* JMeter : 批量发送HTTP请求，可进行压力测试
* apache ab: 
* jstatd: 后台监控进程
* VisuamVM: 图形化的jstat
* jstat: 命令行工具，最简单高效
* jolokia： 通过WEB UI，即可查看JAVA进程的环境变量，URL映射，配置参数，BEAN列表等，相当强大
* zabbix集成的java监控插件

## 推荐插件
* mvn helper
* lombok
* GsonFormat

### 依赖关系检查
mvn helper可以通过UI的方式，展现依赖之间的关系。
对于排队依赖错误很有帮助。

### 简化代码
实体类有大量的getter/setter，通过lombok插件，可大量简化代码。

### JSON to Bean Class
通过GsonFormat插件，可以将josn格式的文件，转化为类。从而简化代码的编写。

## 日志
日志，和代码一样重要。在上线运行之后，日志比代码更重要。

### 推荐
log4j2,logback.
log4j在大量写日志时会阻塞，导致出现性能问题。
因此，推荐log4j2和logback.

### 监控
建议日志监控，使用集中式日志监控平台，典型的如ELK.

## 调用链分析
调用链分析很难做，但分布式系统，如果出现了异常，肯定需要通过分析调用链来排查问题。一个小技巧是在调用链中传递惟一的上下文。


## CI/CD
持续集成，持续部署

## 静态检查
* findbugs

