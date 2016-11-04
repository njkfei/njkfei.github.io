title: spring jetty集成
date: 2016.01.08
categories: java #分类
tags: [java, spring, jetty, web]
toc: true
---
  使用mvn install 打包成jar包，再放到tomcat的webapp目录下，生产环境没有什么问题，但是，如果调试时也这样，那效率也太低了．好在有这样的一个插件:_jetty_.
  _jetty_可以和eclipse集成，在eclipse环境下就能进行web调试，真的很方便．
  　废话少说，上步骤．

## step1 加入插件
  在pom的build/plugins下面，加入
 ``` bash
<plugin>
	<groupId>org.eclipse.jetty</groupId>
		<artifactId>jetty-maven-plugin</artifactId>
		<version>9.2.11.v20150529</version>
		<configuration>
		<scanIntervalSeconds>10</scanIntervalSeconds>
		<webApp>
		<contextPath>/</contextPath>
		</webApp>
		<jettyXml>jetty.xml</jettyXml>
		</configuration>
</plugin>
```

## step2 加入jetty.xml
在与pom.xml同目录下，编辑文件jetty.xml，内容如下：
``` bash
<?xml version="1.0"?>
<!DOCTYPE Configure PUBLIC "-//Jetty//Configure//EN" "http://www.eclipse.org/jetty/configure_9_3.dtd">
<Configure id="Server" class="org.eclipse.jetty.server.Server">
  <Call name="addConnector">
    <Arg>
      <New id="httpConnector" class="org.eclipse.jetty.server.ServerConnector">
        <Arg name="server"><Ref refid="Server" /></Arg>
        <Set name="host"><Property name="jetty.http.host" deprecated="jetty.host" /></Set>
        <Set name="port"><Property name="jetty.http.port" deprecated="jetty.port" default="8888" /></Set>
        <Set name="idleTimeout"><Property name="jetty.http.idleTimeout" deprecated="http.timeout" default="30000"/></Set>
      </New>
    </Arg>
  </Call>
</Configure>
```

## step3 运行项目
在命令行下，输入
``` bash
mvn jetty:run
```
  OK.