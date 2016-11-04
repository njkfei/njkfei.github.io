title: zuul
date: 2016.06.07
categories: api #分类
tags: [api]
toc: true
---

### 为什么使用zuul
Zuul提供了一个框架，可以对过滤器进行动态的加载，编译，运行。过滤器之间没有直接的相互通信。他们是通过一个RequestContext的静态类来进行数据传递的。RequestContext类中有ThreadLocal变量来记录每个Request所需要传递的数据。
过滤器是由Groovy写成。这些过滤器文件被放在Zuul Server上的特定目录下面。Zuul会定期轮询这些目录。修改过的过滤器会动态的加载到Zuul Server中以便于request使用。
下面有几种标准的过滤器类型：
* PRE：这种过滤器在请求到达Origin Server之前调用。比如身份验证，在集群中选择请求的Origin Server，记log等。
* ROUTING：在这种过滤器中把用户请求发送给Origin Server。发送给Origin Server的用户请求在这类过滤器中build。并使用Apache HttpClient或者Netfilx Ribbon发送给Origin Server。
* POST：这种过滤器在用户请求从Origin Server返回以后执行。比如在返回的response上面加response header，做各种统计等。并在该过滤器中把response返回给客户。
* ERROR：在其他阶段发生错误时执行该过滤器。
* 客户定制：比如我们可以定制一种STATIC类型的过滤器，用来模拟生成返回给客户的response。

Zuul可以通过加载动态过滤机制，从而实现以下各项功能：
* 动态路由
* 压力测试
* 过载保护
* 认证和安全
* 审查与监控

个人感觉，zuul有点类似于nginx，可以干反向代理的活。据说性能上和nginx差不多。


###  Why did we build Zuul?

The volume and diversity of Netflix API traffic sometimes results in production issues arising quickly and without warning. We need a system that allows us to rapidly change behavior in order to react to these situations.

Zuul uses a range of different types of filters that enables us to quickly and nimbly apply functionality to our edge service. These filters help us perform the following functions:

Authentication and Security - identifying authentication requirements for each resource and rejecting requests that do not satisfy them.

Insights and Monitoring - tracking meaningful data and statistics at the edge in order to give us an accurate view of production.

Dynamic Routing - dynamically routing requests to different backend clusters as needed.

Stress Testing - gradually increasing the traffic to a cluster in order to gauge performance.

Load Shedding - allocating capacity for each type of request and dropping requests that go over the limit.

Static Response handling - building some responses directly at the edge instead of forwarding them to an internal cluster

Multiregion Resiliency - routing requests across AWS regions in order to diversify our ELB usage and move our edge closer to our members

For more details: How We Use Zuul At Netflix