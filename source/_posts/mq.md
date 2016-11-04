title: message queue消息队列服务
date: 2016.01.08
categories: 服务 #分类
tags: [java, mq, 消息, 队列]
toc: true
---
  消息系统服务是大型网站的基础设施，消息服务在异步，系统解藕方面的效果非常好．
  消息服务，大型网站必备．

## 基本概念
	简单说一句：消息服务是企业内部各个子系统之间异步通信的总线．是各个子系统之间通信的桥梁．
	消息系统支持生产－消费者模板和发布－订阅模型．
	简单的概念有：
* 消息消费者：消费消息，完成消息的处理．根据消息内部的语义，完成业务逻辑处理．
* 消息发布者：生产消息．
* 消息中介：消息消费者与生产者之间的媒介，通过它，实现消费者与发布者之间的解藕．

## ActiveMQ简介
	ActiveMQ是使用最广泛的消息服务．它：
* 支持各语言客户端．因此，不同语言开发的子系统之间通信，可以使用activemq.
* 可以支持jdbc．
* 与spring集成方便，使用起来简单，容易上手．
* 跨平台，linux,windows均支持．可以在windows上进行单机测试开发，再到linux上联调部署．这是我认为的最佳实践．

## ActiveMQ安装
### windows安装
　下载二进制包解压，即可．
### linux安装
　　下载二进制包解压，即可．
	cenots下，可以通过yum方式安装，通过chkconfig设置为隋机器启动．

## ActiveMQ的启动
直接运行bin的方式启动，开发模式下．
或者通过chkconfig启动．生产模式下．

## 协议设计
	ActiveMQ只是提供消息服务，具体的业务处理，需要设计者考虑．设计原则是根据业务需求来定．常见的协议类型有：
* 文本协议：如XML协议，json协议，http协议
* 二进制协议：tcp,ip诸如此类
* 混合型协议: pb之类的
　个人推荐json，即有语义，压缩量也不错．另外，它跨语言，方便不同语系的同学并行开发．

## ActiveMQ与spring集成
### 依赖管理
``` bash
		<!-- activemq 内核 -->
		<dependency>
			<groupId>org.apache.activemq</groupId>
			<artifactId>activemq-core</artifactId>
			<version>5.7.0</version>
		</dependency>
		<!-- jms -->
		<dependency>
			<groupId>javax.jms</groupId>
			<artifactId>jms</artifactId>
			<version>1.1</version>
		</dependency>
		<!-- spring jms -->
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-jms</artifactId>
			<version>3.0.5.RELEASE</version>
		</dependency>
		<!-- fastjson: 解析消息中的业务字段 -->
		<dependency>
			<groupId>com.alibaba</groupId>
			<artifactId>fastjson</artifactId>
			<version>1.2.6</version>
		</dependency>
```

### 配置管理
属性配置activemq.properties
``` bash
mq.host=tcp://localhost:61616?wireFormat.maxInactivityDurationInitalDelay=30000
mq.queue.name=queue
mq.user=activemq
mq.password=activemq123
```

### spring注入
读取消息配置文件
``` bash
	<context:property-placeholder location="classpath:conf/activemq.properties" ignore-unresolvable="true" />
```

配置连接工厂
``` bash
	<!-- Spring提供的JMS工具类，它可以进行消息发送、接收等 -->
	<bean id="jmsTemplate" class="org.springframework.jms.core.JmsTemplate">
		<!-- 这个connectionFactory对应的是我们定义的Spring提供的那个ConnectionFactory对象 -->
		<property name="connectionFactory" ref="connectionFactory" />
	</bean>
	<!-- 真正可以产生Connection的ConnectionFactory，由对应的 JMS服务厂商提供 -->
	<bean id="targetConnectionFactory" class="org.apache.activemq.ActiveMQConnectionFactory">
		<property name="brokerURL">
			<value>${mq.host}</value>
			<!-- <value>"tcp://192.168.1.19:61613"</value> -->
		</property>
		<property name="userName">
			<value>${mq.user}</value>
		</property>
		<property name="password">
			<value>${mq.password}</value>
		</property>
	</bean>
	<!-- Spring用于管理真正的ConnectionFactory的ConnectionFactory -->
	<bean id="connectionFactory"
		class="org.springframework.jms.connection.SingleConnectionFactory">
		<!-- 目标ConnectionFactory对应真实的可以产生JMS Connection的ConnectionFactory -->
		<property name="targetConnectionFactory" ref="targetConnectionFactory" />
	</bean>

```

配置queue或topic
``` bash
	<!--这个是队列目的地 1:1 -->
	<bean id="queueDestinationAudio" class="org.apache.activemq.command.ActiveMQQueue">
		<constructor-arg>
			<value>${mq.queue.name}</value>
		</constructor-arg>
	</bean>
	</bean>
	<!--这个是主题目的地，top:一对多的 -->
	<bean id="topicDestination" class="org.apache.activemq.command.ActiveMQTopic">
		<constructor-arg value="xyz.hollysys.mq.topic" />
	</bean>
```

配置侦听者
我们只需要实现这个类，即可完成消息的业务处理．
``` bash
	<!-- 音频消息监听器 -->
	<bean id="audioMessageListener" class="xyz.hollysys.mq.AudioMessageListener" />
```

配置消息容器，消息容器将侦听者，主题topic或者消息队列queue与broker关联起来．
``` bash
	<bean id="jmsContainerAudio"
		class="org.springframework.jms.listener.DefaultMessageListenerContainer">
		<property name="connectionFactory" ref="connectionFactory" />
		<!-- <property name="destination" ref="topicDestination" /> -->
		<property name="destination" ref="queueDestinationAudio" />
		<property name="messageListener" ref="audioMessageListener" />
		<property name="concurrentConsumers" value="1" />
	</bean>
```

ok,配置完成了，下面可以开始写业务代码了．


### 代码编写
如前所述，我们只需要实现**audioMessageListener**,即可完成消息的业务处理．
典型代码如下：
``` bash
public class AudioMessageListener implements MessageListener {
	private static final Logger logger = Logger.getLogger("audio");

	// message 里面的内容，取决于具体业务，见前面的协议设计
	public void onMessage(Message message) {
		logger.info( "收到的消息类型 ==>" + message.getClass());
		
		if(message instanceof ActiveMQBytesMessage ){
			onActiveMQBytesMessage(message);
		}else if(message instanceof TextMessage){
			onTextMessage(message);
		}else{
			logger.error("不支持的消息类型 ==> " + message.toString());
		}
	}
}
```