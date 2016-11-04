title: RMI VS Restful API
date: 2016.08.24
categories: 设计 #分类
tags: [架构]
---
 
## RMI
### 优点
* 性能高
* 基于TCP
* 开发方便，可通过spring集成，对业务代码无入侵

### 缺点
* 只支持JAVA，不能跨语言
* 适合在内网访问，做为内部服务
* 做分布式

## Restful API
### 优点
* 基于HTTP,适合做开放API
* 易沟通，易交流
* 开发效率高

### 缺点
* 性能稍差

## 总结
* RMI性能高，相对适合于内部服务
* Restful API开放性强，特别适合做APP后端API
* Restful API需要考虑安全，限流，幂等问题
