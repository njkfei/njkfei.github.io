title: 支付宝开发入门
date: 2016.01.08
categories: 服务 #分类
tags: [java, 支付宝, 支付, ]
---
  支付是电商应用的核心服务．常用的支付手段有银联支付，支付宝支付，财富通支付，微信支付等．支付宝是用户最常用的支付习惯．因此，先学习学习支付宝支付接口．
  支付宝开发[开发资源链接](https://doc.open.alipay.com/doc2/detail?treeId=54&articleId=103419&docType=1)为：https://doc.open.alipay.com/doc2/detail?treeId=54&articleId=103419&docType=1

##　概念
### return_url notify_url
#### return_url
 服务器后台通知,这个页面是程序后台运行的(买家和卖家都看不到),买家付完款后,支付宝会调用
#### notify_url
notify_url这个页面所在的页面并把相应的参数传 递到这个页面,这个页面根据支付宝传递过来的参数修改网站订单的状态,更新完订单后需要在页面上打印出一个success 给支付宝如果反馈给支付宝的不是success,支付宝会继续调用这个页面. 
流程: 买家付完款(trade_status=trade_finished)--->支付宝通知notify_url--->如果反馈给支付宝的 是success(表示成功,这个状态下不再反馈,如果不是继续通知,一般第一次发送和第二次发送的时间间隔是3分钟)
剩下的过程,卖家发货,买家确认收货,交易成功都是这个流程.

### 技术方案
　springmvc + alipay_api + jsp